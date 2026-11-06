# SplitSmart Architecture

## Overview and decision

SplitSmart lets individuals create accounts and groups, submit shared expenses for review, split costs, track pairwise obligations, record payments, and review summaries. This document selects a layered modular monolith for the Java Swing desktop MVP. The complete product remains primarily web-based and must also be available on Android and iOS (NFR-001); the Swing prototype does not satisfy that requirement.

The baseline proposal is the only completed architecture proposal found. `prompts/architecture-alternative.md` and the ChatGPT/Copilot architecture prompts are instructions, not generated proposals. Accordingly, this is a provisional selection of the baseline, strengthened with the financial, authorization, and data integrity details below. No comparison score is assigned to a nonexistent alternative. See `architecture-review.md` and `docs/consolidation-analysis.md`.

A single deployable process is appropriate for the MVP scope. Microservices would add deployment and data consistency costs without demonstrated independent scaling needs. Keep domain and application rules independent of Swing and local storage so they can later sit behind a shared backend API.

## Technology choices

**MVP implementation decisions:** Java 21, Maven, Java Swing, a local relational database (SQLite is a candidate, not a final vendor selection), JDBC/repository adapters, JUnit 5, PlantUML, and `BigDecimal` for money. These are project decisions, not customer requirements. The full-product web and mobile technologies are undecided; none are selected here.

## Layers and dependencies

1. **Presentation:** Swing screens/controllers collect input, perform basic formatting checks, display results, and call application services. No approval authorization or financial calculation lives only in the UI.
2. **Application:** Use-case services authenticate actor context, authorize every protected operation, coordinate domain behavior, repositories, and transactions, and return view-ready results.
3. **Domain:** Account, Group, Membership, Expense, ExpenseShare, Approval, Payment, Receipt metadata, and value objects; split, review, payment, and balance rules. No Swing, JDBC, or filesystem dependency.
4. **Persistence:** Repository interfaces/adapters, SQL mapping, foreign keys, and transaction support. Durable financial source records are expense, shares, approval, and payments.
5. **Infrastructure:** Password hashing/reset-token support, receipt file storage, notification delivery, clock, and configuration.

Dependency direction is Presentation -> Application -> Domain. Persistence and infrastructure implement interfaces used by application services; domain does not depend on either implementation.

## Major module responsibilities

| Module | Responsibility |
|---|---|
| Account | Registration, sign-in, reset, private account data |
| Group | Group lifecycle, access context, manager membership |
| Membership | Active role and historical participation; authoritative manager role is stored here |
| Expense | Submitted facts and lifecycle status; submitter and payer are separate references |
| ExpenseShare | Exact assigned participant amounts and input percentage where applicable |
| Approval | Immutable reviewer decision; MVP permits one decision by eligible manager |
| Notification | Durable in-app expense-submission notice for each other group member |
| Payment | Recorded transfer from payer to payee; payment policy remains open |
| Balance | Derived/rebuildable pairwise projection; never an independent financial event |
| Report | Derived view over authorized expense, balance, and payment records |

## Security and privacy

Authenticate using email/password; store salted password hashes, never plaintext. Password reset uses a single-use, expiring random token stored as a hash; responses should avoid account enumeration. Application services enforce account ownership, active group membership, manager role, and reviewer-not-submitter rules on every protected operation. Receipt retrieval is authorized through its expense's group membership. Database and receipt files need local access protection. NFR-002 applies regardless of client; hiding controls in Swing is not authorization.

## Financial architecture

Amounts use `BigDecimal` and a configured currency scale; the specification assumes one currency with two fractional digits for MVP, while multi-currency requirements remain open. Equal splitting converts to integer minor units; remainder units go in deterministic stable participant order. Percentage inputs must total exactly 100% at the defined input precision; rounded shares are reconciled by deterministic residual allocation. Every stored share set must sum exactly to its expense amount.

Only approved expenses affect balances. For each approved expense, credit the payer by the full expense amount and debit each participant by their stored share; a payer is debited only if also a participant. Aggregate member net positions, then derive directed debtor-to-creditor obligations without counting shares twice. Apply recorded payments to their payer/payee direction under the eventual approved policy. Pending and rejected expenses contribute zero. Historical financial rows remain when membership is removed. Any cached Balance is rebuildable from source records and must reconcile before use.

Partial payment and overpayment policy are explicitly unresolved in the specification. The MVP must not silently establish either as product behavior. Payment recording requires a stakeholder-approved validation policy; concurrency control must prevent two simultaneous requests from both consuming the same remaining obligation if a cap is approved.

## Transactions and reliability

Expense submission persists expense, shares, and required notification records atomically. Approval persists decision and status atomically. Payment persistence and any balance projection update are atomic. Receipt bytes and relational data cannot share a database transaction; receipt failure is treated as optional attachment failure, with cleanup of orphan files or a recoverable attachment state. Recompute balances from authoritative events to repair projections.

## Platform evolution and trade-offs

The MVP's local database and Swing client are temporary and do not provide synchronized multi-device access or centralized service operations. For full product, expose authenticated application use cases through a shared backend serving a web client and Android/iOS clients. API, hosting, identity provider, database vendor, deployment topology, and notification channels remain future decisions. Keeping application/domain behavior client-independent preserves this path without pretending those platforms are delivered now.

## Traceability

FR-001 Account module and owner authorization; FR-002 Group/Membership; FR-003 Expense/Receipt; FR-004 ExpenseShare/SplitCalculator; FR-005 Approval and status policy; FR-006 Notification; FR-007 Balance projection; FR-008 Payment; FR-009 Report. NFR-001 is preserved by the platform boundary; NFR-002 by service authorization; NFR-003 by decimal arithmetic and exact reconciliation. Detailed mapping is in `docs/requirements-traceability.md`.
