# Feature Specification: SplitSmart Expense Sharing

**Feature Branch**: `001-splitsmart-expense-sharing`

**Created**: 2026-10-29

**Status**: Draft

**Input**: Authoritative customer requirements in `lab6/UserRequirement.md`; feature request to specify SplitSmart and distinguish customer requirements from implementation decisions.

## Clarifications

### Session 2026-10-29

- Q: Should the Java Swing MVP be treated as a temporary desktop prototype while the full SplitSmart product remains primarily web-based and available on Android and iOS? → A: It is a temporary desktop prototype; web, Android, and iOS remain requirements for the full product.
- Q: How should a person create and access their SplitSmart account? → A: People register with an email and password and can reset a forgotten password.
- Q: Who should be allowed to approve or reject a pending expense in the MVP? → A: Only a group manager who did not submit that expense.
- Q: Who is the payer for an expense — always the submitter, or can it be a different member? → A: The payer is a required field, separate from the submitter and from the participants. The payer may be any group member, including the submitter.

## User Scenarios & Testing *(mandatory)*

### Actors

- **Account holder**: A person who signs in and manages their own account information.
- **Group member**: An account holder who participates in a group's expenses, balances, and payments.
- **Group manager**: A member authorized to create a group and manage its membership.
- **Expense submitter**: A group member who records and submits an expense for review.
- **Expense reviewer**: A group manager other than the submitter who may approve or reject a pending expense.
- **Payer**: A group member who paid the expense and is credited in balance calculations; may be the submitter or another member.

### User Story 1 - Set Up an Account and Group (Priority: P1)

A person creates an account, signs in, creates a group for a shared activity, and adds the people who will share expenses.

**Why this priority**: Accounts and group membership establish who can participate and whose expenses and balances are visible.

**Independent Test**: Create two accounts, sign in as one, create a group, add the second account, and verify both members can see the group while an unrelated account cannot.

**Acceptance Scenarios**:

1. **Given** a person without an account, **When** they register with an email and password and sign in, **Then** they can access their own expense and balance information.
2. **Given** a signed-in person, **When** they create a group and add another account, **Then** both accounts appear as group members.
3. **Given** an account that is not a group member, **When** it attempts to view that group's expenses, **Then** the group information is not shown.
4. **Given** an account holder who forgot their password, **When** they complete the password reset flow, **Then** they can sign in with a new password.

---

### User Story 2 - Submit and Review a Shared Expense (Priority: P1)

A group member records an expense with its amount, date, description, payer, participants, split method, and optional receipt. The group reviews it before it changes balances.

**Why this priority**: Shared expense entry and approval are the source of the balances users need to settle.

**Independent Test**: Submit equal and percentage-split expenses with different payers, approve one and reject another, and verify the resulting shares, notifications, and balances.

**Acceptance Scenarios**:

1. **Given** a group member and an active group, **When** they submit a valid expense with participants and an equal split, **Then** the expense is pending review and the group members are notified.
2. **Given** a valid expense, **When** its creator selects percentage shares totaling 100%, **Then** the system records each participant's share.
3. **Given** a pending expense submitted by a group member, **When** a different group manager approves it, **Then** it is included in group balances.
4. **Given** a pending expense, **When** a different group manager rejects it, **Then** it is retained as rejected and does not change balances.
5. **Given** a split whose percentages do not total 100% or an amount that is not positive, **When** it is submitted, **Then** the system identifies the invalid input and does not create an approvable expense.
6. **Given** a pending expense submitted by a group manager, **When** that same manager attempts to approve or reject it, **Then** the action is denied and the expense remains pending.
7. **Given** a group member recording an expense, **When** they enter amount, date, description, a payer, participants, and a split method, **Then** all fields are persisted and displayed on the expense record.
8. **Given** a group member records an expense with an optional receipt image, **When** the expense is saved, **Then** the receipt is attached; **When** no receipt is provided, **Then** the expense saves without one.
9. **Given** a group member records an expense with a payer who is not the submitter, **When** the expense is approved, **Then** the payer is credited and participants are debited per their shares.
10. **Given** a group member creates an expense, **When** it is saved, **Then** each other group member has a recorded in-app notification for that expense.

---

### User Story 3 - Settle and Review Shared Costs (Priority: P2)

A group member checks who owes whom, records a payment, and reviews expense, balance, and payment summaries for a selected period.

**Why this priority**: Settlement and reporting complete the shared-expense workflow and let members verify its outcomes.

**Independent Test**: Approve a known set of expenses, record a full or partial payment, and compare the displayed balances and date-filtered report with the known records.

**Acceptance Scenarios**:

1. **Given** approved expenses, **When** a member views group balances, **Then** the system shows the amount owed by each member to each other member.
2. **Given** an outstanding balance, **When** a member records a payment between the relevant members, **Then** the payment is recorded and the outstanding balance is reduced by that amount.
3. **Given** expense and payment records within and outside a date range, **When** a member requests a report for that range, **Then** the report summarizes only matching expenses, balances, and payments.
4. **Given** approved expenses and recorded payments, **When** a member requests an unfiltered summary, **Then** the system shows total expenses, balances, and payments without a date restriction.

### Edge Cases

- An expense with no participants, a participant outside the group, or an invalid amount is rejected with a clear validation result.
- A percentage split not totaling 100% is not submitted for approval.
- A rounding remainder in a split is assigned consistently so participant shares total the expense amount exactly.
- A rejected or pending expense never changes a balance; approval is the only transition that adds an expense to balances.
- A payment equal to the remaining balance settles it; a payment greater than the remaining balance is rejected unless the product's overpayment policy is clarified.
- Removing a member does not erase their historical expenses, approvals, or payments.
- A missing or unreadable optional receipt does not prevent the user from recording the expense without that receipt.
- A payer who is not a participant is allowed; the payer is credited and only participants are debited.
- A payer outside the group is rejected with a clear validation result.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST let each person register with an email and password, reset a forgotten password, sign in, and access their own expense and balance information. *(Customer requirement: individual accounts; email and password plus reset are clarified MVP decisions.)*
- **FR-002**: System MUST let authorized members create and manage groups and their memberships. *(Customer requirement; Swing MVP: included.)*
- **FR-003**: System MUST let a group member record an expense with amount, date, description, payer, involved users, and an optional receipt image. *(Customer requirement; payer field added as clarified MVP decision; Swing MVP: included.)*
- **FR-004**: System MUST support equal and percentage-based expense splits and validate that percentage shares total 100%. *(Customer requirement; Swing MVP: included.)*
- **FR-005**: System MUST provide an expense review workflow, exclude pending and rejected expenses from balances, and allow only a group manager other than the submitter to approve or reject an expense; only an approved expense may adjust balances. *(Customer requirement; Swing MVP: one review by an eligible group manager.)*
- **FR-006**: System MUST notify group members when another member creates an expense. *(Customer requirement; Swing MVP: included as an in-app notification; email and push delivery are not assumed.)*
- **FR-007**: System MUST calculate and show the amount each user owes to every other user from approved expenses (crediting the payer and debiting participants per their shares) and recorded payments. *(Customer requirement; Swing MVP: included.)*
- **FR-008**: System MUST let users record payments made to settle an outstanding balance and reflect those payments in subsequent balances. *(Customer requirement; Swing MVP: included.)*
- **FR-009**: System MUST provide expense, balance, and payment summaries and let a user filter reports by a selected date range. *(Customer requirement; Swing MVP: included; other custom parameters are deferred.)*

### Customer Requirement Inventory and MVP Boundary

| Source | Customer requirement | Requirement mapping | Java Swing MVP |
| --- | --- | --- | --- |
| UserRequirement.md: user accounts | Individual accounts and access to personal expense/balance information | FR-001 | Included |
| UserRequirement.md: group management | Create and manage groups of people | FR-002 | Included |
| UserRequirement.md: expense creation | Amount, date, description, equal/percentage split, optional receipt, involved users | FR-003, FR-004 | Included; payer field added as clarified MVP decision |
| UserRequirement.md: notifications | Notify members when another member creates an expense | FR-006 | Included as in-app notification |
| UserRequirement.md: approval workflow | Review and approve expenses before balance adjustments; one or multiple review levels | FR-005 | Included with one reviewer; multiple levels deferred |
| UserRequirement.md: balance tracking | Track each user's debt to every other user across expenses | FR-007 | Included |
| UserRequirement.md: payment tracking | Record payments and update balances | FR-008 | Included |
| UserRequirement.md: reporting | Expense, balance, and payment summaries; custom date ranges and parameters as needed | FR-009 | Basic summaries and date range included; other parameters deferred |
| UserRequirement.md: supported OS | Primary web experience and apps that run on Android and iOS | NFR-001 | Not delivered by a Java Swing desktop MVP; remains full-product scope |

### Java Swing MVP Subset

The MVP is a temporary desktop prototype. It includes FR-001 through FR-009 with the limits stated above. It does not claim to satisfy the customer's primary-web and Android/iOS requirement in NFR-001; those platforms remain required for the full product. The desktop implementation is a delivery decision, not a change to the customer requirement inventory.

### Non-Functional Requirements

- **NFR-001**: The full product MUST be primarily web-based and MUST also be available on Android and iOS. *(Customer requirement; not satisfied by the Java Swing MVP.)*
- **NFR-002**: System MUST restrict account information to its account owner and group information to authorized group members. *(Quality requirement inferred from account and group access.)*
- **NFR-003**: System MUST calculate monetary shares and balances consistently, and the sum of expense shares MUST equal the recorded expense amount to the currency's smallest unit. *(Quality requirement inferred from financial calculations.)*

### Implementation Decisions (Not Customer Requirements)

These are implementation and project-process choices, not statements attributed to the customer:

- Use Java 21 and Maven for the Java Swing desktop MVP.
- Use JUnit 5 for automated tests and PlantUML for design diagrams.
- Apply object-oriented design and keep GUI behavior separate from business logic.
- Use `BigDecimal` for monetary calculations.
- Trace requirements through design, implementation, and tests using the IDs in this specification.
- Require human review of AI-generated project artifacts before accepting them.

### Open Questions

1. Should the full product support configurable or multiple approval levels beyond the MVP's one eligible manager review?
2. Which notification channels are required beyond in-app notifications (email, push, or both), and should members be notified before or only after approval?
3. Which currencies and precision rules are required, and how should a rounding remainder be assigned?
4. Are partial payments allowed, and should overpayments be rejected or carried as credit?
5. Which report filters, export formats, and receipt image limits are required?
6. What account deletion policy is required?

### Key Entities *(include if feature involves data)*

- **Account**: A person's sign-in identity and private expense/balance view.
- **Group**: A shared context with members, expenses, reviews, and balances.
- **Membership**: A person's participation and permissions within a group.
- **Expense**: Amount, date, description, payer, creator, participants, split method, shares, optional receipt, and review status.
- **Expense Share**: The portion of an expense assigned to one participant.
- **Approval**: A review decision by an eligible group manager other than the expense submitter.
- **Notification**: A notice that a group expense was created.
- **Balance**: The net amount one member owes another, derived from approved expenses and recorded payments.
- **Payment**: A recorded transfer between members that reduces an outstanding balance.
- **Report**: A summary of expenses, balances, and payments for selected criteria such as a date range.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 9 of 10 first-time test participants can create a group and submit a valid shared expense without assistance within 4 minutes.
- **SC-002**: In all defined split test cases, participant shares total the expense amount to the smallest currency unit; no pending or rejected expense changes a balance.
- **SC-003**: In all reference scenarios, displayed pairwise balances match the expected balances after approved expenses and recorded payments.
- **SC-004**: For every tested date range, report totals match the corresponding expense and payment records exactly.
- **SC-005**: Every group member eligible to receive an expense notification has a corresponding notification recorded for each submitted group expense.
- **SC-006**: Before full-product acceptance, the same agreed core workflows pass on web, Android, and iOS; the Swing MVP alone does not count as satisfying this criterion.

## Assumptions

- The Java Swing application is a temporary desktop prototype that demonstrates the expense-sharing workflows; it does not replace the customer's web and mobile platform requirement.
- The MVP uses one configured currency and two fractional digits unless a product decision establishes different currency rules.
- Expense approvals use one group manager other than the submitter in the MVP; configurable or multiple approval levels are a full-product question.
- The MVP's notification channel is in-app. Email and push delivery require a separate product decision.
- The MVP includes basic date-range reports; additional custom filters and report exports are not included until specified.
- Payments may be recorded against an outstanding balance, but partial-payment and overpayment policies remain open questions.
- The payer is a required expense field and may be any group member, including the submitter; this applies to both the MVP and the full product.