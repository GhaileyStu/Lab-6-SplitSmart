# SplitSmart Data Model

## Conventions and financial authority

UUID primary keys; UTC timestamps; business dates as DATE. `Money` is represented as `DECIMAL(19,2)` for the provisional single-currency MVP and BigDecimal in application logic. Currency is a configured group/product context; add an ISO currency column before supporting mixed currencies. These types are design guidance, not database implementation scripts.

Authoritative financial facts are Expense, ExpenseShare, Approval, and Payment. Balance is a derived projection, never an independent event. Reports are query results. Account removal disables access but does not cascade-delete historical financial records.

## Entities and attributes

| Entity | Attributes (type; PK/FK marked) | Constraints / meaning |
|---|---|---|
| Account | `accountId UUID PK`, `email VARCHAR(254)`, `passwordHash VARCHAR`, `displayName VARCHAR`, `createdAt TIMESTAMP`, `updatedAt TIMESTAMP`, `accountStatus ENUM` | Normalized email unique; password hash only; owner-only private information. |
| Group | `groupId UUID PK`, `name VARCHAR`, `description VARCHAR NULL`, `createdBy UUID FK Account`, `createdAt TIMESTAMP`, `groupStatus ENUM` | Access requires active membership; creator initially manager. |
| Membership | `membershipId UUID PK`, `groupId UUID FK`, `accountId UUID FK`, `role ENUM`, `joinedAt TIMESTAMP`, `membershipStatus ENUM`, `leftAt TIMESTAMP NULL` | One active membership per group/account. Role MANAGER/MEMBER is authoritative here; retain inactive rows. At least one active manager while group active. |
| Expense | `expenseId UUID PK`, `groupId UUID FK`, `submitterId UUID FK Account`, `payerId UUID FK Account`, `amount DECIMAL(19,2)`, `currency CHAR(3)`, `expenseDate DATE`, `description VARCHAR`, `splitMethod ENUM`, `status ENUM`, `createdAt TIMESTAMP`, `updatedAt TIMESTAMP` | Positive amount; payer and submitter are distinct role references; both are group members at submission; payer need not be participant. Status PENDING/APPROVED/REJECTED. |
| ExpenseShare | `expenseShareId UUID PK`, `expenseId UUID FK`, `participantId UUID FK Account`, `percentage DECIMAL(7,4) NULL`, `shareAmount DECIMAL(19,2)`, `createdAt TIMESTAMP` | Unique (expense, participant); participant is active group member at submission; shares sum exactly to expense amount. Percentage populated for percentage split. |
| Approval | `approvalId UUID PK`, `expenseId UUID FK UNIQUE`, `reviewerId UUID FK Account`, `decision ENUM`, `reviewComment VARCHAR NULL`, `reviewedAt TIMESTAMP` | MVP one immutable decision; reviewer active group manager and not submitter; only pending expense can be reviewed. |
| Notification | `notificationId UUID PK`, `recipientId UUID FK Account`, `groupId UUID FK`, `expenseId UUID FK`, `notificationType ENUM`, `createdAt TIMESTAMP`, `readAt TIMESTAMP NULL`, `notificationStatus ENUM` | Durable in-app event; one EXPENSE_SUBMITTED notice per (expense, recipient), submitter excluded. |
| Payment | `paymentId UUID PK`, `groupId UUID FK`, `payerId UUID FK Account`, `payeeId UUID FK Account`, `amount DECIMAL(19,2)`, `currency CHAR(3)`, `paymentDate DATE`, `status ENUM`, `createdAt TIMESTAMP`, `note VARCHAR NULL`, `idempotencyKey VARCHAR NULL` | Distinct active group accounts; positive amount. Duplicate prevention via idempotency key when supplied. Partial and overpayment policy are unresolved; status semantics must not invent policy. |
| Balance | `balanceId UUID PK`, `groupId UUID FK`, `debtorId UUID FK Account`, `creditorId UUID FK Account`, `amount DECIMAL(19,2)`, `currency CHAR(3)`, `updatedAt TIMESTAMP`, `sourceVersion BIGINT` | Optional rebuildable projection; unique group/debtor/creditor/currency; debtor != creditor; positive rows only; omit zero. Never source of truth. |
| Receipt | `receiptId UUID PK`, `expenseId UUID FK UNIQUE`, `storageReference VARCHAR`, `fileName VARCHAR`, `contentType VARCHAR`, `uploadedAt TIMESTAMP` | Zero-or-one per expense; metadata/reference in DB, image may be in filesystem. Authorize through expense group. Missing/unreadable image does not block expense without attachment. |

## Relationships and cardinalities

- Account 1 -- 0..* Membership; Group 1 -- 1..* Membership (group creation adds its creator as first manager). Together they form Account/Group many-to-many over time.
- Group 1 -- 0..* Expense.
- Account 1 -- 0..* Expense as submitter; separately Account 1 -- 0..* Expense as payer.
- Expense 1 -- 1..* ExpenseShare; Account 1 -- 0..* ExpenseShare as participant. This realizes expense/participant many-to-many with allocation attributes.
- Expense 1 -- 0..1 Approval (MVP); Account 1 -- 0..* Approval as reviewer.
- Expense 1 -- 0..* Notification; Account 1 -- 0..* Notification as recipient; Group 1 -- 0..* Notification.
- Group 1 -- 0..* Payment; Account 1 -- 0..* Payment separately as payer and payee.
- Group 1 -- 0..* Balance projection; Account 1 -- 0..* Balance separately as debtor and creditor.
- Expense 1 -- 0..1 Receipt.

## Integrity and enforcement

**Database-enforceable:** primary/foreign keys; unique normalized email; unique active membership pair (partial unique index where supported); valid enum/check values; positive amount checks; unique expense/participant; unique approval per expense; unique notification expense/recipient; unique projection pair; distinct payment parties; receipt unique expense; idempotency-key uniqueness where non-null.

**Domain/application-enforced transactionally:** payer and participants belong to expense group; reviewer is current manager and not submitter; only pending review; percentage total=100%; sum of share amounts=expense amount; valid currency scale; minimum one active manager; receipt retrieval authorization; report authorization; payment eligibility under yet-to-be-approved payment policy. Cross-row share reconciliation may use transaction-level validation because ordinary SQL CHECK constraints cannot aggregate sibling rows.

Writes use transactions: expense+shares+notifications; approval+status; membership changes; payment+projection refresh if projection is used. Use guarded status updates/locking to prevent duplicate review. Rebuild projections from source events and reconcile before serving if stale.

## Balance derivation and examples

For each APPROVED expense E of amount A, add +A to payer's net position and subtract each stored share from that participant. Do not debit payer unless also a participant. Sum each person's positions across approved expenses; zero-sum positions are paired deterministically: debtors (negative) owe creditors (positive), up to the smaller remaining magnitude. The resulting direction is `(debtorId -> creditorId)`. Apply each valid recorded payment from payer to payee exactly once against that direction under the policy eventually approved. Pending/rejected expenses contribute zero. Use integer minor units internally for allocation and deterministic account-ID ordering for remainder cents.

Examples assume USD, two decimals, and stable IDs A < B < C; currency and payment policies remain open.

1. **Equal 3-way remainder:** $10.00 / 3 => A $3.34, B $3.33, C $3.33. Exact total $10.00.
2. **Percentage:** $100.00 with A 50%, B 30%, C 20% => $50, $30, $20; exactly $100.
3. **Payer not participant:** A pays $60; B/C share equally. Net A +60, B -30, C -30; B->A $30 and C->A $30.
4. **Payer is participant:** A pays $60; A/B share equally. A +60-30=+30, B -30; B->A $30. A is not debited twice.
5. **Rejected:** $40 rejected expense contributes zero, regardless of saved shares.
6. **Pending:** $40 pending contributes zero until approved.
7. **Multiple expenses:** Aggregate member positions across all approved expenses before matching debtors/creditors; this nets opposite events without duplicating expense/share rows.
8. **Payment:** If B owes A $30, an accepted $10 B->A payment leaves $20. This demonstrates arithmetic only; whether partial payments are allowed is unresolved.
9. **Member removal:** B's historical $20 debt, expense share, approval, and payment history remain linked to B after membership ends; access changes, history does not.

## Indexes

Unique Account(email normalized); Membership(group, account, active status); Expense(group, expenseDate, status), Expense(submitter), Expense(payer); ExpenseShare(expense, participant) unique and participant lookup; Approval(expense) unique; Notification(recipient, createdAt, readAt) and (expense, recipient) unique; Payment(group, paymentDate), payer/payee/date and idempotency key; Balance(group, debtor, creditor, currency) unique; Receipt(expense) unique; Password reset token account/expiry with unique token hash if reset tokens are modeled.
