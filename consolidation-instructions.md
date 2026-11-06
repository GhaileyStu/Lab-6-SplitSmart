# SplitSmart — Final Architecture and Design Consolidation Instructions

**Project:** SplitSmart Expense Sharing
**Repository:** `GhaileyStu/Lab-6-SplitSmart`
**Purpose:** Produce one consistent, complete, and implementation-ready requirements, architecture, design, data-model, and UML documentation package by reconciling the available architecture proposals.

**Critical instruction:** This is a documentation and design assignment. Complete the requirements analysis, architecture comparison, design, data model, and UML diagrams before beginning implementation. Do not generate application code, database implementation scripts, or GUI code as a substitute for the required design deliverables.

---

# 1. Objective and Expected Outcome

Review the existing SplitSmart project documents and consolidate them into one authoritative set of project documentation.

Use these documents as the sources of truth:

- `UserRequirement.md`
- `specs/001-splitsmart-expense-sharing/spec.md`
- `architecture-baseline.md`
- The completed alternative architecture proposal, if available.
- `prompts/architecture-alternative.md`
- `prompts/chatgpt-review.md`
- `prompts/copilot-architecture.md`

Important distinction: A file containing an architecture-generation prompt is not an architecture proposal. Locate the actual generated alternative architecture before claiming that two designs have been compared.

The final deliverables must describe one coherent system. Requirements, architecture, design, data model, and UML diagrams must agree with one another.

The goal is not to select the most complicated or technologically advanced architecture. Select the design that best satisfies SplitSmart's requirements while remaining realistic, maintainable, testable, and appropriate for the MVP.

# 2. Mandatory Design-First Workflow

Complete the following phases in order.

**Do not skip phases. Do not jump directly from reviewing the prompts to writing code.**

| Phase | Activity                                             | Required output                                    |
| ----- | ---------------------------------------------------- | -------------------------------------------------- |
| 1     | Inspect source documents and repository              | Source inventory and gap analysis                  |
| 2     | Reconcile functional and non-functional requirements | Updated requirements specification                 |
| 3     | Evaluate and select architecture                     | Architecture review and decision                   |
| 4     | Design the system                                    | Architecture and design documents                  |
| 5     | Design the detailed data model                       | Entity, relationship, and constraint documentation |
| 6     | Generate UML diagrams                                | Five required UML diagram types                    |
| 7     | Validate documentation                               | Requirements traceability and consistency report   |
| 8     | Finalize deliverables                                | Complete documentation package                     |

Do not begin implementation until all documentation phases have been completed and reviewed.

If a source document is missing, identify it as missing. Do not invent its contents or claim to have compared an architecture that does not exist.

# 3. Phase 1 — Inspect Existing Documents

Before making changes, inspect the repository and identify the contents of each relevant file.

At minimum, inspect:

- `UserRequirement.md`
- `specs/001-splitsmart-expense-sharing/spec.md`
- `architecture-baseline.md`
- The completed alternative architecture proposal, if present.
- All architecture-related prompts.

Create an inventory identifying:

1. Which documents exist.
2. Which documents contain actual requirements.
3. Which documents contain architecture proposals.
4. Which documents contain prompts or instructions only.
5. Which requirements or design decisions are duplicated.
6. Which decisions conflict.
7. Which information is missing.

Do not delete original proposals. Retain them as source material for the consolidation.

**Deliverable:** A short source-document inventory and gap analysis, included in `architecture-review.md` or a separate `docs/consolidation-analysis.md`.

# 4. Phase 2 — Reconcile and Preserve All Requirements

**Target file:** `specs/001-splitsmart-expense-sharing/spec.md`

Use `UserRequirement.md` as the customer-requirement source and the existing `spec.md` as the detailed feature specification.

Preserve all existing functional requirements. Do not remove or silently change any requirement simply because the MVP implementation is simpler.

## 4.1 Mandatory Functional Requirements

The consolidated specification must preserve every requirement from FR-001 through FR-009.

| ID     | Required functionality | Details that must be preserved                                                                                                                                       |
| ------ | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| FR-001 | Account management     | Register with email and password, sign in, reset forgotten passwords, and access personal expense and balance information.                                           |
| FR-002 | Group management       | Authorized members can create and manage groups and memberships.                                                                                                     |
| FR-003 | Expense entry          | Record amount, date, description, payer, involved users, and optional receipt image.                                                                                 |
| FR-004 | Expense splitting      | Support equal and percentage-based splits and validate that percentages total 100%.                                                                                  |
| FR-005 | Expense approval       | Provide review and approval workflow; exclude pending and rejected expenses from balances; only a group manager other than the submitter can approve or reject.      |
| FR-006 | Notifications          | Notify group members when another member creates an expense. The MVP uses in-app notifications.                                                                      |
| FR-007 | Balance tracking       | Calculate how much each user owes every other user using approved expenses and recorded payments. Credit the payer and debit participants according to their shares. |
| FR-008 | Payment tracking       | Record payments made to settle outstanding balances and reflect payments in subsequent balances.                                                                     |
| FR-009 | Reporting              | Provide expense, balance, and payment summaries with date-range filtering.                                                                                           |

Do not combine, omit, or renumber these requirements.

## 4.2 Mandatory Non-Functional Requirements

Preserve all three existing non-functional requirements.

**NFR-001 — Supported platforms**

The complete product must be primarily web-based and available on Android and iOS.

The Java Swing application is a temporary desktop MVP. It does not replace or satisfy the full-product platform requirement.

**NFR-002 — Security and privacy**

Account information must be restricted to the account owner, and group information must be restricted to authorized group members.

**NFR-003 — Financial accuracy**

Monetary calculations must be exact. The sum of expense shares must equal the expense amount to the smallest currency unit.

## 4.3 Preserve All User Scenarios

Ensure the specification retains all three user stories and their acceptance scenarios:

1. Set up an account and group.
2. Submit and review a shared expense.
3. Settle and review shared costs.

Preserve the existing acceptance scenarios, including:

- Successful registration and sign-in.
- Group creation and membership.
- Prevention of unauthorized group access.
- Password reset.
- Expense submission.
- Equal and percentage splitting.
- Manager approval and rejection.
- Prevention of self-approval.
- Invalid percentage and amount validation.
- Persistence of expense details.
- Optional receipt handling.
- Payer different from submitter.
- In-app notifications.
- Pairwise balances.
- Payment recording.
- Date-filtered and unfiltered reports.

## 4.4 Preserve All Edge Cases

The consolidated requirements must explicitly address:

- Expenses without participants.
- Participants outside the group.
- Invalid or nonpositive amounts.
- Percentage allocations that do not total 100%.
- Deterministic allocation of rounding remainders.
- Pending and rejected expenses not affecting balances.
- Payments equal to the remaining balance.
- Overpayments.
- Historical records after membership removal.
- Missing or unreadable optional receipts.
- Payers who are not participants.
- Payers outside the group.

Do not remove an edge case simply because it is not yet implemented.

## 4.5 Preserve Success Criteria

Keep all six measurable success criteria from the existing specification:

- SC-001: First-time group and expense creation usability.
- SC-002: Exact split calculations and correct approval effects on balances.
- SC-003: Correct pairwise balances.
- SC-004: Accurate date-filtered report totals.
- SC-005: Notification records for eligible group members.
- SC-006: Successful core workflows on web, Android, and iOS before full-product acceptance.

## 4.6 Separate Requirements from Implementation Decisions

Clearly distinguish:

**Customer requirements**

- Accounts.
- Groups.
- Expense creation.
- Splitting.
- Approval.
- Notifications.
- Balances.
- Payments.
- Reporting.
- Supported platforms.

**Clarified MVP decisions**

- Email and password authentication.
- Separate payer field.
- One eligible manager reviewer.
- In-app notifications.
- Java Swing temporary desktop prototype.

**Implementation decisions**

- Java 21.
- Maven.
- JUnit 5.
- PlantUML.
- BigDecimal for monetary calculations.

Do not present implementation decisions as if they were original customer requirements.

## 4.7 Preserve Open Questions

Do not silently resolve these existing product questions:

1. Multiple approval levels beyond the MVP.
2. Notification channels beyond in-app notifications.
3. Supported currencies, currency precision, and rounding policy.
4. Partial payments and overpayment treatment.
5. Additional report filters, exports, and receipt limits.
6. Account deletion policy.

Document any proposed design assumptions separately and identify which decisions require approval.

**Phase 2 deliverable:** Updated `spec.md` with all original requirements, user stories, acceptance scenarios, edge cases, success criteria, assumptions, and open questions preserved.

# 5. Phase 3 — Evaluate and Select the Architecture

**Target file:** `architecture-review.md`

Compare the available architecture proposals using the consolidated requirements.

The existing baseline is a layered modular monolith using Java 21, Maven, Java Swing, and relational persistence.

Evaluate the alternative proposal only if the actual generated proposal exists.

## 5.1 Required Comparison Criteria

Evaluate both architectures using:

1. Functional requirement coverage.
2. Non-functional requirement coverage.
3. Business-rule enforcement.
4. Domain-model completeness.
5. Security and privacy.
6. Financial correctness.
7. Implementation complexity.
8. Maintainability and testability.
9. Scalability and future platform support.
10. Documentation and UML consistency.

Use a consistent scoring method for both proposals.

A weighted scoring table should include:

| Criterion                          | Suggested weight |
| ---------------------------------- | ---------------: |
| Requirements alignment             |              25% |
| Domain and data model              |              20% |
| Security and data integrity        |              15% |
| Maintainability and testability    |              15% |
| Scalability and platform evolution |              10% |
| Implementation practicality        |              10% |
| Documentation quality              |               5% |
| **Total**                          |         **100%** |

Explain each score. Do not assign unsupported scores to a missing proposal.

## 5.2 Architecture Selection Rules

Use the following decision principles:

- Prefer a design that supports all actual requirements.
- Avoid unnecessary distributed-system complexity.
- Keep business logic independent of the graphical interface.
- Ensure financial calculations are centralized and testable.
- Ensure authorization is enforced outside the presentation layer.
- Preserve a path toward web and mobile support.
- Adopt alternative-design features only when they offer a measurable benefit.

The layered modular monolith is the recommended starting point for the MVP. It should remain the selected architecture unless the completed alternative demonstrates a better fit.

## 5.3 Record the Architecture Decision

Document:

- Selected architecture.
- Reasons for selection.
- Strengths of the selected design.
- Weaknesses and accepted trade-offs.
- Useful ideas retained from the other proposal.
- Rejected approaches and reasons for rejection.
- Outstanding assumptions and risks.

**Phase 3 deliverable:** A documented architecture decision based on an actual comparison, or an explicit statement that the alternative proposal is unavailable and the baseline is provisionally selected.

# 6. Phase 4 — Create the Consolidated Architecture

**Target file:** `architecture.md`

Create one authoritative architecture document.

Do not simply paste the baseline and alternative documents together. Resolve conflicting responsibilities, technology choices, dependencies, and design decisions.

Use the following sections.

## 6.1 Architecture Overview

Explain:

- What SplitSmart does.
- Who uses the application.
- The current MVP scope.
- The full-product platform requirements.
- The selected architecture style.
- Why this architecture fits the project.

## 6.2 Technology Stack

Document the selected technologies and distinguish MVP technologies from future platform decisions.

The existing baseline identifies:

- Java 21.
- Maven.
- Java Swing.
- SQLite or another appropriate local relational database.
- JUnit 5.
- PlantUML.

Do not invent a future web or mobile technology stack. Document future platform support as an architectural requirement and identify technology choices that remain undecided.

## 6.3 Architecture Layers

Document the responsibilities and dependencies of:

**Presentation layer**

- Swing screens and controllers.
- Input collection and display.
- Basic presentation validation.

**Application layer**

- Use-case orchestration.
- Application services.
- Authorization checks.
- Transaction coordination.

**Domain layer**

- Business entities.
- Value objects.
- Expense split rules.
- Approval rules.
- Balance calculations.
- Payment rules.

**Persistence layer**

- Repositories.
- Database access.
- Transaction support.
- Data mapping.

**Infrastructure layer**

- Password hashing.
- Receipt storage.
- Notification delivery.
- Clock and utility implementations.

The domain layer must not depend on Swing or concrete database implementations.

## 6.4 Major Modules

Document the responsibilities and dependencies of:

- Account.
- Group.
- Membership.
- Expense.
- ExpenseShare.
- Approval.
- Notification.
- Payment.
- Balance.
- Report.

Keep the module structure understandable and appropriate for the MVP. Do not introduce empty abstractions or services without a real responsibility.

## 6.5 Security Architecture

Document:

- Authentication.
- Password storage and reset.
- Group membership authorization.
- Manager permissions.
- Expense review permissions.
- Receipt access control.
- Protection of personal information.
- Validation of every protected application operation.

The interface must never be the only enforcement point for authorization.

## 6.6 Financial Architecture

Explain:

- How expense shares are calculated.
- How rounding is handled.
- How approved expenses affect balances.
- How payments reduce balances.
- How pairwise balances are calculated.
- How financial history is preserved.
- How data integrity is protected.

The authoritative financial records must be expenses, shares, approvals, and payments. If balances are stored for performance, document how they are rebuilt or reconciled.

## 6.7 Platform Evolution

Explain how the selected architecture can evolve from the temporary Swing MVP to a backend supporting web, Android, and iOS clients.

Keep domain and application behavior independent of the presentation technology.

**Phase 4 deliverable:** A complete `architecture.md` describing one coherent architecture.

# 7. Phase 5 — Create the Detailed System Design

**Target file:** `design.md`

This phase must be completed before implementation.

Describe how the selected architecture will satisfy each major requirement.

## 7.1 Required Design Sections

1. System overview.
2. Module responsibilities.
3. Application service responsibilities.
4. Class responsibilities.
5. Expense creation workflow.
6. Expense review workflow.
7. Notification workflow.
8. Balance calculation workflow.
9. Payment workflow.
10. Reporting workflow.
11. Validation and error handling.
12. Authorization.
13. Transaction boundaries.
14. Testing strategy.
15. Design assumptions and unresolved questions.

## 7.2 Service Responsibilities

Use these services as a starting point:

| Service             | Responsibility                                                      |
| ------------------- | ------------------------------------------------------------------- |
| AccountService      | Account registration, sign-in, account information, password reset. |
| GroupService        | Group creation, membership, and management.                         |
| ExpenseService      | Expense validation, creation, and split calculation.                |
| ApprovalService     | Reviewer eligibility, approval, and rejection.                      |
| NotificationService | In-app notification creation and retrieval.                         |
| BalanceService      | Pairwise balance calculation and display.                           |
| PaymentService      | Payment validation, recording, and balance updates.                 |
| ReportService       | Expense, balance, and payment summaries.                            |

The final design must identify the inputs, outputs, responsibilities, and important dependencies of each service.

## 7.3 Required Workflow Designs

### A. Expense submission

Document:

1. A group member enters the expense details.
2. The system validates the amount, date, description, payer, participants, and split.
3. The system verifies that the payer and participants belong to the group.
4. The system calculates the shares.
5. The system verifies that shares reconcile to the expense amount.
6. The expense and shares are saved with pending status.
7. In-app notifications are recorded for the other group members.
8. The submitter receives confirmation.

### B. Expense approval

Document:

1. A group manager opens a pending expense.
2. The system verifies active group membership and manager authority.
3. The system verifies that the reviewer is not the submitter.
4. The approval decision is recorded.
5. The expense status is changed atomically.
6. Only approval makes the expense eligible for balance calculations.
7. Rejection preserves the expense record without changing balances.

### C. Balance calculation

Document:

1. Retrieve approved expenses.
2. Retrieve their participant shares and payer information.
3. Retrieve relevant recorded payments.
4. Calculate each member's net position.
5. Derive pairwise amounts owed.
6. Return the balances to the requesting authorized group member.

Specify how the implementation prevents double-counting and handles payer-participant overlap.

### D. Payment recording

Document:

1. Identify the members involved.
2. Retrieve the outstanding balance.
3. Validate the payment amount.
4. Reject invalid or excessive payments according to the approved policy.
5. Record the payment.
6. Update or recalculate balances.
7. Confirm the result to the user.

Do not assume a partial-payment or overpayment policy has been finalized. Document the alternatives and mark the product decision as unresolved where necessary.

### E. Reporting

Document:

1. Select report type.
2. Select date range or request an unfiltered summary.
3. Retrieve authorized records.
4. Calculate totals.
5. Return expense, balance, and payment information.
6. Ensure the report totals reconcile with the underlying records.

**Phase 5 deliverable:** A complete `design.md` describing the application's design and workflows without writing application code.

# 8. Phase 6 — Create the Detailed Data Model

**Target file:** `data-model.md`

This is a critical deliverable.

SplitSmart depends on correct financial data and pairwise balance tracking. A list of entity names is not sufficient. The data model must specify attributes, types, keys, relationships, cardinalities, constraints, and calculation rules.

Use the following entities as the minimum starting set.

## 8.1 Account

**Purpose:** Represents a person with a SplitSmart account.

Document fields such as:

- `accountId`
- `email`
- `passwordHash`
- `displayName`
- `createdAt`
- `updatedAt`
- `accountStatus`

Specify:

- Primary key.
- Unique email constraint.
- Password storage requirements.
- Account ownership and privacy.
- Relationship to memberships and submitted expenses.

Do not store plaintext passwords.

## 8.2 Group

**Purpose:** Represents a shared expense group.

Document fields such as:

- `groupId`
- `name`
- `description`
- `createdBy`
- `createdAt`
- `groupStatus`

Specify:

- Group creator.
- Membership relationship.
- Expense relationship.
- Group access restrictions.
- Group manager responsibilities.

## 8.3 Membership

**Purpose:** Represents an account's participation and permissions within a group.

Document fields such as:

- `membershipId`
- `groupId`
- `accountId`
- `role`
- `joinedAt`
- `membershipStatus`
- `leftAt`, if needed.

Specify:

- Relationship to Account.
- Relationship to Group.
- Membership uniqueness.
- Manager permissions.
- Active versus historical membership.
- Preservation of historical financial records after removal.

Resolve whether manager authority is stored in Membership, Group, or another authoritative mechanism. Avoid maintaining conflicting sources of truth.

## 8.4 Expense

**Purpose:** Represents a shared expense submitted for review.

Document fields such as:

- `expenseId`
- `groupId`
- `submitterId`
- `payerId`
- `amount`
- `currency`
- `expenseDate`
- `description`
- `splitMethod`
- `status`
- `receiptId`, if applicable
- `createdAt`
- `updatedAt`

Explicitly distinguish:

- The submitter.
- The payer.
- The participants.

The payer is required and must be a member of the group. The payer does not have to be a participant and may be different from the submitter.

Specify:

- Positive amount constraint.
- Valid expense status.
- Group relationship.
- Payer relationship.
- Submitter relationship.
- Expense-share relationship.
- Approval relationship.

## 8.5 ExpenseShare

**Purpose:** Represents the amount of an expense assigned to one participant.

Document fields such as:

- `expenseShareId`
- `expenseId`
- `participantId`
- `percentage`, when applicable
- `shareAmount`
- `createdAt`

Specify:

- Each participant's share.
- Equal split calculation.
- Percentage split calculation.
- Percentage total validation.
- Currency precision.
- Rounding mode.
- Deterministic remainder allocation.
- Constraint that the sum of shares equals the expense amount.
- Prevention of duplicate participant shares for the same expense.

A participant must belong to the expense's group.

The payer must not automatically be treated as a participant. A payer who is not a participant must still receive credit for paying.

## 8.6 Approval

**Purpose:** Records the review decision for an expense.

Document fields such as:

- `approvalId`
- `expenseId`
- `reviewerId`
- `decision`
- `reviewComment`
- `reviewedAt`

Specify:

- The reviewer must be an eligible group manager.
- The reviewer cannot be the submitter.
- Only pending expenses can be reviewed.
- Approval and expense status changes must be atomic.
- A completed review cannot be duplicated.
- Historical review information must be retained.

If future multiple-review workflows are proposed, document them as a future extension rather than silently changing the MVP rules.

## 8.7 Notification

**Purpose:** Records an in-app notification associated with an expense creation event.

Document fields such as:

- `notificationId`
- `recipientId`
- `groupId`
- `expenseId`
- `notificationType`
- `createdAt`
- `readAt`
- `notificationStatus`

Specify:

- Which group members receive notifications.
- Whether the submitter is excluded from recipient notifications.
- Relationship to the expense.
- Unread and read behavior.
- In-app delivery for the MVP.

Do not assume email or push delivery is required in the MVP.

## 8.8 Payment

**Purpose:** Represents a recorded transfer between group members that reduces an outstanding balance.

Document fields such as:

- `paymentId`
- `groupId`
- `payerId`
- `payeeId`
- `amount`
- `currency`
- `paymentDate`
- `status`
- `createdAt`

Specify:

- Sender and recipient relationships.
- Group membership validation.
- Positive payment amount.
- Relationship to the outstanding pairwise balance.
- Duplicate-record prevention strategy.
- Payment effect on the balance.
- Historical payment preservation.

The specification includes payment tracking and reducing outstanding balances. Partial-payment behavior and overpayment treatment remain open product questions. Do not invent a finalized policy.

If partial payments are supported, explain how multiple payments reduce the outstanding amount. If overpayments are rejected, explain how the outstanding amount is checked consistently when concurrent payment requests occur.

## 8.9 Balance

**Purpose:** Represents the net amount one member owes another member.

Document whether Balance is:

- Derived dynamically from approved expenses and payments; or
- Stored as a rebuildable projection for performance.

The underlying approved expense, expense-share, and payment records must remain authoritative.

If persisted, document fields such as:

- `balanceId`
- `groupId`
- `debtorId`
- `creditorId`
- `amount`
- `currency`
- `updatedAt`

Specify:

- Debtor and creditor relationships.
- Direction of the balance.
- Uniqueness of the debtor-creditor pair within the group and currency.
- Zero-balance handling.
- Recalculation and reconciliation.
- Prevention of double-counting.
- Behavior when members leave a group.

Do not treat a stored balance as an independent financial event.

## 8.10 Receipt

**Purpose:** Represents optional receipt image metadata and storage reference.

Document fields such as:

- `receiptId`
- `expenseId`
- `storageReference`
- `fileName`
- `contentType`
- `uploadedAt`

Specify:

- Optional relationship to Expense.
- Access authorization.
- File metadata validation.
- Behavior when an optional receipt is missing or unreadable.
- Historical association with its expense.

Do not assume that the image itself must be stored directly in the relational database.

## 8.11 Required Relationship and Cardinality Definitions

At minimum, define:

- Account to Membership.
- Group to Membership.
- Group to Expense.
- Account to submitted Expense.
- Account to paid Expense.
- Expense to ExpenseShare.
- Account to ExpenseShare.
- Expense to Approval.
- Account to Approval.
- Expense to Notification.
- Account to Notification.
- Group to Payment.
- Account to Payment as payer.
- Account to Payment as payee.
- Group to Balance.
- Account to Balance as debtor.
- Account to Balance as creditor.
- Expense to Receipt.

Specify the correct one-to-one, one-to-many, and many-to-many relationships and their optionality.

## 8.12 Required Balance Calculation Explanation

The data-model document must explain how balances are derived.

For an approved expense:

- Credit the payer by the expense amount.
- Debit each participant by their assigned share.
- Do not debit the payer automatically unless the payer is also a participant.
- Do not credit a participant simply because they are included in the expense.
- Apply the recorded shares exactly once.

The net position of each member must reconcile across all approved expenses and payments.

Explain how net positions are converted into pairwise amounts owed. Define the direction of the debtor-creditor relationship and how payments reduce the relevant outstanding amount.

Include worked examples covering:

1. Equal split among three participants with a rounding remainder.
2. Percentage split.
3. Payer who is not a participant.
4. Payer who is also a participant.
5. Rejected expense.
6. Pending expense.
7. Multiple expenses involving the same members.
8. Payment reducing an existing balance.
9. Member removal while historical balances remain relevant.

Do not fabricate a product decision for any open payment or currency question. Label assumptions clearly.

## 8.13 Database Integrity Requirements

Document the required constraints:

- Primary keys.
- Foreign keys.
- Unique account emails.
- Unique group membership combinations.
- Valid membership and role values.
- Positive expense amounts.
- Positive payment amounts.
- Valid expense status values.
- Valid approval decisions.
- Valid payer and participant group membership.
- Valid percentage totals.
- Expense-share reconciliation.
- Historical record retention.
- Appropriate transaction boundaries.
- Consistent currency precision.

Distinguish constraints enforced directly by the database from rules enforced by application services or domain logic.

**Phase 6 deliverable:** A detailed `data-model.md` that is sufficient for another developer to understand the financial relationships and design the database without guessing.

# 9. Phase 7 — Generate the Required UML Diagrams

Create all five required UML diagram types using PlantUML.

Store editable `.puml` source files and their rendered images in:

`docs/diagrams/`

Required files:

```text
docs/
└── diagrams/
    ├── use-case-diagram.puml
    ├── component-diagram.puml
    ├── class-diagram.puml
    ├── sequence-diagrams.puml
    └── state-diagram.puml
```

Render each diagram to an image format such as PNG or SVG and retain the editable source.

## 9.1 Use Case Diagram

**File:** `use-case-diagram.puml`

Actors:

- Account Holder.
- Group Member.
- Group Manager.

Show the use cases for:

- Register account.
- Sign in.
- Reset password.
- View personal expense and balance information.
- Create group.
- Manage group.
- Add and manage members.
- Submit expense.
- Upload optional receipt.
- Select payer.
- Select participants.
- Select split method.
- Approve expense.
- Reject expense.
- View notifications.
- View group balances.
- Record payment.
- View expense reports.
- View balance reports.
- View payment reports.

Show that approval and rejection require an eligible group manager who did not submit the expense.

## 9.2 Component Diagram

**File:** `component-diagram.puml`

Show the five layers:

- Presentation.
- Application.
- Domain.
- Persistence.
- Infrastructure.

Show the major services, repositories, and domain responsibilities.

Show dependency direction.

Ensure that:

- Swing is in the presentation layer.
- Application services coordinate use cases.
- Domain logic owns financial and approval rules.
- Repositories handle persistence.
- Infrastructure handles supporting technical capabilities.

Show future web and mobile clients as future consumers of backend application capabilities. Clearly distinguish them from the current Swing MVP.

## 9.3 Class Diagram

**File:** `class-diagram.puml`

Include all important domain classes:

- Account.
- Group.
- Membership.
- Expense.
- ExpenseShare.
- Approval.
- Notification.
- Payment.
- Balance.
- Receipt.

Show:

- Important attributes and data types.
- Primary relationships.
- Multiplicities.
- Ownership and foreign-key relationships.
- Payer and submitter as separate Expense relationships.
- Expense participants and shares.
- Approval reviewer relationship.
- Payment payer and payee relationships.
- Balance debtor and creditor relationships.

Include service classes where useful, but do not turn the domain class diagram into an unstructured list of every method.

The class diagram must agree with `data-model.md`.

## 9.4 Sequence Diagrams

**File:** `sequence-diagrams.puml`

Create sequence diagrams for these five workflows.

**A. Expense submission**

Show the user, presentation layer, ExpenseService, domain split calculation, repositories, and NotificationService.

Include validation of the group, payer, participants, split method, and exact share total.

**B. Expense approval**

Show the manager, presentation layer, ApprovalService, authorization check, approval persistence, and expense status update.

Include the prevention of self-approval and the atomic approval transaction.

**C. Balance calculation**

Show the requesting member, BalanceService, expense and payment repositories, calculation logic, and returned pairwise balances.

Include only approved expenses.

**D. Payment recording**

Show payment submission, outstanding balance retrieval, validation, payment persistence, and balance recalculation or projection update.

**E. Report generation**

Show report selection, date-range input, ReportService, relevant repositories, aggregation, and report response.

Each diagram must use the actual service and repository names established in the design.

## 9.5 State Diagram

**File:** `state-diagram.puml`

Show the expense lifecycle.

Include the applicable states:

- Pending.
- Approved.
- Rejected.

Include Draft only if the final design explicitly supports saving drafts.

Show:

- Initial submission to Pending.
- Pending to Approved.
- Pending to Rejected.
- No approval or rejection from an already completed state.
- Only an eligible group manager can perform review transitions.
- Rejected and pending expenses do not affect balances.
- Approved expenses become eligible for balance calculation.

If payment states are modeled, include them in a separate state diagram or a clearly separated region. Do not invent states unsupported by the selected design.

# 10. Phase 8 — Requirements Traceability

Create or include a requirements traceability matrix in `architecture-review.md` or `docs/requirements-traceability.md`.

For every functional and non-functional requirement, identify:

- Requirement ID.
- Requirement description.
- Architecture module.
- Design section.
- Data entities involved.
- UML diagram reference.
- Acceptance test or verification method.

At minimum, all FR-001 through FR-009 and NFR-001 through NFR-003 must appear.

Also map the six success criteria to their relevant requirements and verification methods.

No requirement should disappear between the specification, architecture, design, data model, and diagrams.

# 11. Phase 9 — Cross-Document Validation

After generating all documentation, perform a complete consistency review.

## Requirements versus Architecture

Verify:

- All nine functional requirements are supported.
- All three non-functional requirements are addressed.
- The MVP boundary is explicit.
- Future web and mobile requirements are preserved.
- The architecture supports the approval and financial business rules.

## Architecture versus Design

Verify:

- Every major module has a clear responsibility.
- Every service belongs to the correct layer.
- Dependency direction is consistent.
- Presentation components do not own business rules.
- Transaction boundaries are documented.

## Design versus Data Model

Verify:

- All required domain entities exist.
- Relationships and cardinalities are defined.
- Payer, submitter, and participants are distinct.
- Expense shares reconcile to the expense total.
- Approval history is preserved.
- Payments affect the correct pairwise balances.
- Historical records survive membership removal.

## Data Model versus UML

Verify:

- Every important entity appears in the class diagram.
- Relationships and multiplicities agree.
- Component dependencies agree with the architecture.
- Sequence diagrams use the actual services.
- State transitions agree with the approval rules.

## Financial Correctness

Verify:

- Monetary calculations use exact values.
- Rounding is deterministic.
- Every split reconciles exactly.
- Pending and rejected expenses are excluded.
- Approved expenses are counted once.
- Payments reduce the correct balances.
- Pairwise amounts are consistent.
- Balance projections, if stored, can be reconciled with authoritative records.

## Documentation Quality

Verify:

- Terminology is consistent.
- No conflicting technology stacks remain.
- No missing or invented requirements are presented as customer requirements.
- Open questions are visible.
- All diagrams are rendered and readable.
- All documents refer to the same selected architecture.

# 12. Required Final Repository Structure

Create or update the documentation files so the final repository contains the following structure:

```text
Lab-6-SplitSmart/
│
├── UserRequirement.md
├── architecture-baseline.md
├── architecture-review.md
├── architecture.md
├── design.md
├── data-model.md
│
├── specs/
│   └── 001-splitsmart-expense-sharing/
│       └── spec.md
│
├── prompts/
│   ├── architecture-alternative.md
│   ├── chatgpt-review.md
│   └── copilot-architecture.md
│
└── docs/
    ├── consolidation-analysis.md
    ├── requirements-traceability.md
    │
    └── diagrams/
        ├── use-case-diagram.puml
        ├── use-case-diagram.png
        ├── component-diagram.puml
        ├── component-diagram.png
        ├── class-diagram.puml
        ├── class-diagram.png
        ├── sequence-diagrams.puml
        ├── sequence-diagrams.png
        ├── state-diagram.puml
        └── state-diagram.png
```

Retain original files and prompts as appropriate. Do not overwrite source documents without preserving the original information.

# 13. Final Deliverables and Completion Checklist

The documentation assignment is complete only when all the following items are satisfied.

## Requirements

- [ ] All original functional requirements FR-001 through FR-009 are preserved.
- [ ] All non-functional requirements NFR-001 through NFR-003 are preserved.
- [ ] All three user stories and their acceptance scenarios are retained.
- [ ] All edge cases are addressed.
- [ ] All six success criteria are retained.
- [ ] Customer requirements and implementation decisions are distinguished.
- [ ] Open questions are documented.

## Architecture

- [ ] Existing architecture documents have been inspected.
- [ ] The actual alternative proposal has been located or its absence documented.
- [ ] The architecture comparison uses consistent criteria.
- [ ] One architecture has been selected with a documented rationale.
- [ ] `architecture.md` describes one coherent system.
- [ ] The MVP and full-product platform requirements are distinguished.

## Design

- [ ] `design.md` describes all major modules.
- [ ] Service responsibilities are documented.
- [ ] Expense submission is designed.
- [ ] Expense approval is designed.
- [ ] Notifications are designed.
- [ ] Balance calculation is designed.
- [ ] Payment recording is designed.
- [ ] Reporting is designed.
- [ ] Authorization and transaction boundaries are documented.

## Data Model

- [ ] `data-model.md` documents every required entity.
- [ ] Attributes and data types are specified.
- [ ] Primary and foreign keys are identified.
- [ ] Relationships and cardinalities are specified.
- [ ] Payer, submitter, and participants are distinct.
- [ ] Expense shares and rounding are defined.
- [ ] Approval records are modeled.
- [ ] Payment relationships and outstanding balances are explained.
- [ ] Balance calculation and reconciliation are documented.
- [ ] Historical financial records are preserved.
- [ ] Database constraints are documented.
- [ ] Worked financial examples are included.

## UML

- [ ] Use case diagram generated.
- [ ] Component diagram generated.
- [ ] Class diagram generated.
- [ ] Sequence diagrams generated.
- [ ] State diagram generated.
- [ ] PlantUML source files retained.
- [ ] Rendered diagrams inspected.
- [ ] Diagrams agree with the written documents.

## Final Validation

- [ ] Requirements traceability matrix completed.
- [ ] No requirement is missing from the design.
- [ ] No contradictory architecture decisions remain.
- [ ] All unresolved questions are identified.
- [ ] All required documentation files exist.
- [ ] Documentation is reviewed before implementation begins.

# 14. Final Instruction to the AI Agent

Work through this assignment in the exact phase order specified above.

**Your job is to produce a complete design and documentation package, not to start coding the application.**

First inspect the source documents. Then reconcile the requirements, compare the architectures, select and document one architecture, create the system design, define the detailed financial data model, generate all five UML diagram types, and validate consistency across the deliverables.

Do not skip the detailed data model. Correct expense-share and pairwise balance tracking is central to SplitSmart.

Do not remove any functional requirement to make the architecture easier to implement.

Do not invent a completed alternative architecture if only a prompt is available.

Do not silently resolve open product questions.

Do not generate Java classes, GUI screens, database implementation scripts, or other application code during this documentation assignment.

If a decision is blocked by missing information, document the question, explain its impact, and continue with the remaining design work using an explicitly labeled provisional assumption where possible.

**The final result must be a coherent, traceable, technically detailed design package that another developer can use to begin implementation only after the documentation has been reviewed and approved.**
