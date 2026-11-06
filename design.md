# SplitSmart Design

## System and module design

The selected system is a layered modular monolith for the temporary Java Swing MVP. `specs/001-splitsmart-expense-sharing/spec.md` is authoritative. Presentation calls application services; services authorize, coordinate domain policies, transactions, and repositories; domain owns business rules; persistence and infrastructure implement technical ports.

| Service | Inputs | Responsibilities and dependencies | Output |
|---|---|---|---|
| AccountService | registration/sign-in/reset command, actor context | Normalize email; hash/check password; issue/consume expiring reset token; account repository | Session/account result; safe validation errors |
| GroupService | actor, group/member command | Create group; manage membership and manager roles; enforce active manager and last-manager policy | Group/member view |
| ExpenseService | actor, group, amount/date/description, payer, participants/split, optional receipt | Validate group and membership; calculate/reconcile shares; coordinate expense/share/notification transaction and optional receipt storage | Pending expense and shares |
| ApprovalService | reviewer actor, expense, decision | Confirm pending state, active manager, reviewer differs from submitter; save decision and status atomically | Approved/rejected status |
| NotificationService | expense event, group members | Record one in-app notification for each member except submitter; retrieve/read notices | Notification list |
| BalanceService | actor, group, optional period | Authorize; load approved expenses/shares and payments; derive pairwise balances; reconcile optional projection | Directed obligations |
| PaymentService | actor, group, payer, payee, amount/date | Validate distinct group accounts and positive amount; apply policy once decided; persist transaction; refresh/reconcile balance projection | Payment and resulting balance view |
| ReportService | actor, group, report type, optional inclusive dates | Authorize; query relevant records; aggregate expense, balance and payment summaries consistently | Report DTO with criteria and totals |

Domain classes include Account, Group, Membership, Expense, ExpenseShare, Approval, Notification, Payment, Balance projection, Receipt metadata, Money, and DateRange. Swing frames/dialogs format and display; they do not own calculation or authority.

## Workflow designs

### Expense submission (FR-003, FR-004, FR-006)

1. Member enters amount, date, description, payer, participants, split method, and optionally receipt.
2. ExpenseService verifies actor's active group membership and validates required fields and positive amount.
3. Verify payer and every participant are active members of that group; payer may be submitter and may be outside participant set.
4. SplitCalculator calculates equal or percentage shares. Reject percentage totals other than 100%; allocate currency remainders deterministically and assert exact total.
5. In one DB transaction insert PENDING expense, shares, and durable notifications for every other active group member. Do not notify submitter. A missing/unreadable optional image permits saving without it; receipt storage is separate and orphan cleanup is required.
6. Return pending confirmation; balances remain unchanged.

### Expense approval (FR-005)

The group manager loads a pending expense. ApprovalService checks active membership, MANAGER role, reviewer != submitter, and status still PENDING. Within one transaction insert the unique decision and update status with a pending-state guard. A concurrent/duplicate review loses the guarded update and is rejected. Approval makes shares eligible for balance queries; rejection preserves the record and contributes zero. No self-approval.

### Notification workflow (FR-006)

Submission transaction creates one in-app notice per other group member with group, expense, event type, created/read timestamps. Recipient reads are scoped to that account. Email/push are not part of MVP.

### Balance calculation (FR-007)

Load approved expenses only, their shares and payer references, plus applicable payments. For each expense credit payer by total and debit participants by their exact shares. A payer-participant overlap nets naturally (payer receives total credit and their share debit). Aggregate positions across events, then pair deficits against surpluses deterministically. Apply each payment exactly once in its payer-to-payee direction. Return balances only after group membership authorization. Expenses are never also represented as separate debits in a second ledger, preventing double count.

### Payment recording (FR-008)

Resolve payer/payee accounts, group, and current derived obligation; require distinct active members and a positive amount. Partial-payment and overpayment behavior are open product questions (spec open question 4). The service must not assume either behavior. Once policy is approved, enforce it transactionally against a consistent balance snapshot/locking strategy, persist payment, and rebuild or update the derived projection atomically. Confirm the recorded payment and resulting balances.

### Reporting (FR-009)

Select expense, balance, payment, or combined report; accept inclusive start/end or no range. Reject reversed dates. Authorize group before queries. Filter expenses and payments by their respective business dates; balance report is computed from financial records relevant to the selected range, while a full-history summary omits filters. Label statuses in expense activity and include only approved expenses in balance totals. Reconcile displayed totals to matching records.

## Validation, authorization, and transaction boundaries

Field validation returns safe, field-specific errors. Authorization failures reveal no protected group details. Application service authorization precedes each read/write, including receipt access. Database constraints backstop uniqueness and references; domain checks handle cross-row rules such as share totals and manager eligibility.

| Operation | Transaction |
|---|---|
| Submit expense | Expense + shares + submission notifications; optional file handled with cleanup/recovery |
| Review | Approval row + guarded status transition |
| Membership change | Membership status/role; preserve financial history and at least one manager |
| Record payment | Payment + optional projection refresh after approved policy validation |

## Testing and verification strategy

Per constitution, business-rule verification uses JUnit 5: account privacy, manager permissions, self-review denial, split totals/remainders, payer/participant overlap, pending/rejected exclusion, payment direction and policy boundaries, report ranges, and projection reconciliation. Acceptance scenarios and success criteria are traced in `docs/requirements-traceability.md`. Tests are a future implementation activity, not evidence supplied by this design document.

## Assumptions and open questions

MVP uses one configured two-decimal currency, one eligible non-submitting manager review, in-app notices, and basic date-range reports per spec. Currency rules, payment policy (partial/overpayment), other notification channels, approval levels, receipt limits/retention, advanced report/export needs, and account deletion remain unresolved. No future web/mobile stack is selected.
