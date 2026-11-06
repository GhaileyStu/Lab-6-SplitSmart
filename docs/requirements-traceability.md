# Requirements Traceability

| ID | Requirement | Architecture module | Design location | Data entities | UML | Verification method |
|---|---|---|---|---|---|---|
| FR-001 | Register/sign in/reset password; owner access to personal data | Account | AccountService; security | Account, PasswordResetToken | use-case, component, class, sequence | Acceptance: registration, sign-in, reset; verify another account cannot read private data. |
| FR-002 | Authorized group and membership management | Group, Membership | GroupService; membership authorization | Group, Membership, Account | use-case, component, class | Acceptance: add member; unrelated account denied; manager-only operations. |
| FR-003 | Record amount/date/description/payer/participants/optional receipt | Expense, Receipt | ExpenseService submission | Expense, ExpenseShare, Receipt | use-case, component, class, expense sequence | Persist/display all fields; payer/group validation; missing receipt fallback. |
| FR-004 | Equal and percentage splits; percentage total 100% | ExpenseShare | SplitCalculator | Expense, ExpenseShare | class, expense sequence | Equal/percentage examples; reject invalid percentages; exact total at currency unit. |
| FR-005 | Review; pending/rejected excluded; eligible manager other than submitter | Approval, Expense | ApprovalService | Expense, Approval, Membership | use-case, class, approval sequence, state | Approve/reject; self-review denial; duplicate/terminal review denial; balance unchanged unless approved. |
| FR-006 | Notify group members on another member's expense; MVP in-app | Notification | NotificationService | Notification, Expense, Membership | use-case, component, class, expense sequence | SC-005: each eligible recipient has exactly one persisted submission notice. |
| FR-007 | Pairwise balances from approved expenses and payments; payer credit/participant debits | Balance | BalanceService | Expense, ExpenseShare, Payment, Balance projection | component, class, balance sequence | SC-002/003; worked examples; compare projection to authoritative events. |
| FR-008 | Record settlement payment and reflect in balances | Payment | PaymentService | Payment, Balance projection | use-case, class, payment sequence | Record payment and verify direction/recalculation under approved policy; policy remains unresolved. |
| FR-009 | Expense/balance/payment summaries, selected date range | Report | ReportService | Expense, ExpenseShare, Payment; derived Balance | use-case, component, report sequence | SC-004: compare filtered and unfiltered totals against source records. |
| NFR-001 | Full product web-first plus Android and iOS | Future client/API boundary | Architecture platform evolution | Shared domain records | component | SC-006: agreed workflows pass on all required full-product clients; Swing alone does not count. |
| NFR-002 | Account owner/group-member privacy | Application authorization | Security and error handling | Account, Membership, Receipt | use-case, component, sequences | Authorization scenarios for unrelated account, inactive member, unauthorized receipt. |
| NFR-003 | Exact money and shares sum to expense | Domain money/split policies | Split and balance workflows | Expense, ExpenseShare, Payment, Balance | class, sequence, state | SC-002/003/004; deterministic rounding and reconciliation cases. |

## Success criteria mapping

| Criterion | Requirements | Architecture/design | Verification |
|---|---|---|---|
| SC-001 | FR-002, FR-003 | Swing presentation and ExpenseService | Timed first-use task: at least 9/10 unaided within 4 minutes. |
| SC-002 | FR-004, FR-005, NFR-003 | SplitCalculator and approval-gated BalanceService | All defined split cases reconcile; pending/rejected have no balance effect. |
| SC-003 | FR-007, FR-008 | BalanceService and PaymentService | Compare pairwise outputs to reference calculations after expenses/payments. |
| SC-004 | FR-009 | ReportService | Each selected date range totals exactly matching records. |
| SC-005 | FR-006 | NotificationService | Verify notice record for every eligible recipient/expense. |
| SC-006 | NFR-001 | Future shared backend/client boundary | Full acceptance requires web, Android and iOS workflows; Swing does not satisfy it. |
