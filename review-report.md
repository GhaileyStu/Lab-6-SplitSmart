SplitSmart Architecture Review

Review Date: November 5, 2026
Project: SplitSmart Expense Sharing
Repository: GhaileyStu/Lab-6-SplitSmart
Review Status: Baseline architecture reviewed; alternative architecture unavailable for direct comparison.

1. Executive Summary

This report evaluates the SplitSmart baseline architecture against the functional and non-functional requirements documented in specs/001-splitsmart-expense-sharing/spec.md.

The baseline architecture proposes a layered modular monolith using Java 21, Maven, Java Swing, and a relational database. This approach is appropriate for the current desktop MVP because it separates presentation, application logic, domain rules, persistence, and infrastructure without introducing unnecessary distributed-system complexity.

The baseline demonstrates good separation of concerns and identifies the major domain entities required by SplitSmart. However, several areas require additional design decisions, particularly financial calculations, balance management, payment processing, authorization, and future platform support.

The specification makes clear that Java Swing is a temporary desktop prototype. The complete product must remain primarily web-based and support Android and iOS. The architecture should therefore keep business logic independent of Swing so that it can eventually support multiple client applications.

Overall recommendation: Retain the layered modular monolith for the MVP, address the identified financial and security gaps, and preserve a clear path toward a shared backend for future web and mobile applications.

A completed alternative architecture proposal was not available in the repository during this review. The existing prompts/architecture-alternative.md contains instructions for generating an architecture proposal, not the proposal itself. Therefore, this report evaluates the available baseline without inventing details or assigning an unsupported score to an alternative.

2. Project Requirements

The specification identifies the following major requirements.

2.1 Functional Requirements

Requirement

Description

FR-001

Users can register, sign in, reset passwords, and access personal expense and balance information.

FR-002

Authorized users can create and manage groups and memberships.

FR-003

Group members can submit expenses containing an amount, date, description, payer, participants, and optional receipt.

FR-004

Expenses support equal and percentage-based splits. Percentage allocations must total 100%.

FR-005

Expenses require approval by a group manager other than the submitter. Pending and rejected expenses cannot affect balances.

FR-006

Group members receive in-app notifications when another member creates an expense.

FR-007

The system calculates pairwise balances using approved expenses and recorded payments.

FR-008

Users can record payments that reduce outstanding balances.

FR-009

Users can view expense, balance, and payment summaries with date-range filtering.

2.2 Non-Functional Requirements

Requirement

Description

NFR-001

The complete product must be primarily web-based and also available on Android and iOS.

NFR-002

Account information and group information must only be accessible to authorized users.

NFR-003

Monetary calculations must be exact, and expense shares must equal the original expense amount to the smallest currency unit.

2.3 Important Business Rules

The architecture must also support these clarified requirements:

The payer is separate from the expense submitter and participants.

The payer can be any group member, including the submitter.

Only a different group manager can approve or reject an expense.

Pending and rejected expenses never affect balances.

A payer who is not an expense participant is still credited for the expense.

Participants are debited according to their assigned shares.

Percentage splits must total exactly 100%.

Rounding differences must be allocated consistently.

Historical expenses, approvals, and payments must survive membership removal.

Payments equal to the remaining balance settle it.

Overpayments should be rejected unless a different policy is established.

3. Baseline Architecture Evaluation

3.1 Architecture Style

The baseline uses a layered modular monolith with five primary layers:

Presentation

Application

Domain

Persistence

Infrastructure

This is an appropriate architecture for the current MVP.

A modular monolith allows the project to remain relatively simple while maintaining clear boundaries between responsibilities. It also makes it easier to test business logic independently from the graphical interface.

Microservices would introduce additional deployment, communication, monitoring, and maintenance requirements without providing enough benefit for the current project size.

Strengths:

Clear separation of responsibilities.

Centralized business rules.

Easier testing and debugging.

Lower operational complexity than microservices.

Suitable for the Java Swing MVP.

Potential to reuse domain logic in future applications.

Weaknesses:

The package structure may be unnecessarily extensive for a small MVP.

The architecture does not fully define how the future web and mobile clients will communicate with the system.

Some boundaries between application, domain, and infrastructure responsibilities need further clarification.

Recommendation: Keep the modular monolith, but avoid creating unnecessary abstractions before they are needed. Maintain dependency direction so that domain logic does not depend on Swing or concrete persistence implementations.

3.2 Presentation Layer

The presentation layer uses Java Swing to provide the desktop interface.

The baseline identifies screens for:

Login and registration.

Dashboard.

Group management.

Expense entry.

Expense review.

Payments.

Reports.

This covers the major user workflows.

The presentation layer should only collect input, display information, and communicate with application services. It should not perform financial calculations or independently enforce approval permissions.

Recommendation: Keep all business logic outside Swing components. This will make the transition to web and mobile interfaces significantly easier.

3.3 Application Layer

The application layer coordinates user actions and business workflows.

The proposed services include:

AccountService

GroupService

ExpenseService

ApprovalService

NotificationService

BalanceService

PaymentService

ReportService

This organization is reasonable because each service corresponds to a major application capability.

However, the architecture should explicitly define transaction boundaries.

For example:

Expense creation should save the expense, its shares, and notification records consistently.

Expense approval should update the review status and create the approval record atomically.

Payment recording should update the payment ledger and any stored balance projections consistently.

Recommendation: Use application services to coordinate transactions and enforce authorization before changing financial records.

3.4 Domain Layer

The domain layer is the most important part of SplitSmart because it contains the application's financial rules.

The baseline appropriately includes:

Account

Group

Membership

Expense

ExpenseShare

Approval

Notification

Payment

Balance

Report

It also includes value objects and services for monetary values, percentages, split calculations, and balance rules.

This is a strong foundation.

However, several details require clarification.

Balance calculation

The architecture includes a persisted Balance entity but also states that balances should be derived from approved expenses and payments.

These approaches must be reconciled.

The recommended approach is to treat approved expenses and payments as the authoritative financial records. Balance records, if persisted for performance, should be treated as rebuildable projections rather than an independent source of truth.

Split calculations

The application must use BigDecimal rather than floating-point arithmetic.

The architecture should also specify:

Currency precision.

Rounding mode.

Deterministic allocation of leftover minor currency units.

Validation that all participant shares equal the expense total.

For example, a $10.00 expense split equally among three people cannot be divided into three identical two-decimal amounts. The system needs a consistent rule for assigning the remaining cent.

Approval workflow

The architecture must ensure that:

Only pending expenses can be reviewed.

The reviewer is a group manager.

The reviewer is not the expense submitter.

A completed approval cannot be duplicated.

Approval and status changes occur in one transaction.

Recommendation: Strengthen the domain rules before implementation, particularly around financial reconciliation and approval integrity.

3.5 Persistence Layer

The baseline uses repositories to separate data access from business logic.

This is a good design decision.

The proposed relational database approach supports the relationships between accounts, groups, memberships, expenses, shares, approvals, payments, and notifications.

Important database constraints should include:

Unique account email addresses.

Unique group membership combinations.

Valid foreign-key relationships.

Unique pairwise balance records if balances are persisted.

Positive monetary amounts.

Valid expense-share relationships.

Historical financial records should remain available even after a member leaves a group.

Recommendation: Use database transactions and constraints to protect financial data. Avoid relying exclusively on application-level validation.

3.6 Infrastructure Layer

The infrastructure layer includes:

Receipt storage.

In-app notification publishing.

Clock utilities.

Password hashing.

Authorization support.

These responsibilities are appropriate.

Security-related functionality should not be implemented solely in the user interface.

Password reset functionality also needs additional design details, including expiring reset tokens and protection against account enumeration.

Receipt files must only be accessible to authorized users.

Recommendation: Keep infrastructure implementations behind well-defined interfaces so they can be replaced or extended without changing domain logic.

4. Requirements Alignment

The following table evaluates how well the baseline addresses the specification.

Requirement

Coverage

Assessment

FR-001

Partial

Account functionality is represented, but password reset implementation details need clarification.

FR-002

Good

Groups and memberships are included in the data model and service structure.

FR-003

Good

Expense records include the required fields, including a separate payer.

FR-004

Partial

Equal and percentage splits are supported conceptually, but rounding rules need more detail.

FR-005

Partial

Approval entities and services exist, but transactional and authorization details need strengthening.

FR-006

Good

In-app notifications are included.

FR-007

Partial

Balance functionality exists, but the calculation and persistence strategy needs clarification.

FR-008

Partial

Payments are represented, but partial-payment and overpayment policies need definition.

FR-009

Good

Reporting services and date-range filtering are included conceptually.

NFR-001

Partial

Swing supports the temporary MVP but does not satisfy the complete web, Android, and iOS requirement.

NFR-002

Partial

Security responsibilities are identified, but authorization enforcement needs more detail.

NFR-003

Partial

BigDecimal is specified, but deterministic rounding and reconciliation rules need further definition.

The baseline covers the major functional requirements, but several requirements are represented conceptually rather than fully specified at the implementation level.

5. Architecture Risks and Weaknesses

5.1 Financial Accuracy

<ins>Severity: Critical</ins>

Incorrect balance calculations could cause users to owe the wrong amounts.

The architecture must ensure that all calculations use exact monetary representations and that shares always reconcile to the original expense amount.

5.2 Balance Source of Truth

<ins>Severity: High</ins>

Persisted balance records could become inconsistent with expense and payment records.

The system should calculate balances from authoritative financial events or maintain a rebuildable balance projection.

5.3 Payment Processing

<ins>Severity: High</ins>

The specification includes partial payments, but the architecture does not fully define their behavior.

The system needs explicit rules for:

Reducing outstanding balances.

Tracking multiple payments.

Rejecting payments greater than the outstanding amount.

Preventing duplicate payment records.

5.4 Authorization and Privacy

<ins>Severity: Critical</ins>

The application must ensure that users cannot access another account's private information or view groups they do not belong to.

Authorization must be checked by application services for every protected operation.

5.5 Future Platform Support

<ins>Severity: High</ins>

The Java Swing MVP does not satisfy the full product requirement.

If business logic becomes tightly coupled to Swing or local database assumptions, supporting web and mobile applications will become more difficult.

The domain and application layers should remain independent of the presentation technology.

5.6 Architectural Complexity

<ins>Severity: Medium</ins>

The proposed package structure contains many classes and layers.

While separation is beneficial, implementing every proposed abstraction immediately could slow development.

The project should prioritize working functionality and testable business rules over unnecessary architectural complexity.

6. Weighted Architecture Score

The available baseline is scored using the following criteria.

Evaluation Dimension

Weight

Score

Weighted Score

Requirements alignment

25%

7/10

17.5

Domain and data model quality

20%

7/10

14.0

Security and data integrity

15%

6/10

9.0

Maintainability and testability

15%

8/10

12.0

Scalability and platform evolution

10%

6/10

6.0

Implementation practicality

10%

8/10

8.0

Documentation quality

5%

8/10

4.0

Total

100%



70.5/100

Baseline Architecture Score: 7.05/10

The baseline receives a positive score because its overall structure is practical, maintainable, and aligned with the current MVP.

Its score is reduced by unresolved financial rules, incomplete security details, and the gap between the temporary desktop architecture and the complete product's platform requirements.

These scores evaluate the architecture proposal, not a completed implementation.

7. Alternative Architecture Comparison

The repository currently contains prompts/architecture-alternative.md, which is a prompt for generating an alternative architecture. It does not contain the generated architecture proposal.

The root-level files architecture-alternative.md and architecture.md were not present in the repository during this review.

As a result, an evidence-based comparison of two architecture proposals cannot yet be completed.

The alternative should be evaluated using the same criteria as the baseline:

Requirements alignment.

Architecture style and fit.

Domain and data model quality.

Security and data integrity.

Scalability and performance.

Maintainability.

Implementation complexity.

Documentation quality.

The alternative should not automatically receive a higher score simply because it uses newer technologies or a more complex architecture.

A modular monolith may be more appropriate than microservices for this project's size and current requirements.

Once the completed alternative proposal is available, its score should be added to the weighted evaluation table and compared directly against the baseline.

8. Final Recommendation

The layered modular monolith should remain the recommended architecture for the SplitSmart Java Swing MVP.

It provides an appropriate balance between simplicity, maintainability, and separation of concerns.

The architecture should be improved in the following areas before implementation is considered complete:

Define balances as derived financial information or rebuildable projections.

Specify deterministic monetary rounding and share reconciliation.

Define partial-payment behavior and reject overpayments until a policy is established.

Enforce approval eligibility and transactional consistency.

Strengthen authorization and password-reset security.

Preserve historical financial records after membership removal.

Keep domain and application logic independent of Swing.

Establish a future shared backend/API architecture for web, Android, and iOS clients.

Microservices are not recommended for the current MVP because they would add complexity without a demonstrated need.

A modular monolith can continue to support the first version of the full product through a centralized backend and multiple client applications.

9. Conclusion

The baseline architecture provides a strong starting point for SplitSmart. Its layered structure supports the current Java Swing prototype while keeping the application's most important business logic organized and testable.

The largest concerns are not the overall architecture style but the details of financial correctness, authorization, payment handling, and future platform evolution.

Addressing these issues will make the architecture more reliable and reduce the risk of expensive changes later.

Final decision: Retain the layered modular monolith for the MVP, implement the recommended improvements, and reserve final comparison and architecture selection until the completed alternative architecture proposal is available.