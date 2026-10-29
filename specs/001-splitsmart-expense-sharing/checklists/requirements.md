# SplitSmart Requirements Index

**Source of truth**: `lab6/UserRequirement.md` (customer request, unmodified)
**Detailed specification**: `specs/001-splitsmart-expense-sharing/spec.md`
**Constitution**: `.specify/memory/constitution.md`

## Customer Requirement Inventory

Every customer feature from `UserRequirement.md` is preserved and mapped to a functional requirement ID in `spec.md`.

| # | Customer feature (UserRequirement.md) | Spec ID | Java Swing MVP |
|---|---|---|---|
| 1 | User accounts | FR-001 | Included |
| 2 | Group management | FR-002 | Included |
| 3 | Expense creation (amount, date, description, split method, optional receipt, involved users) | FR-003, FR-004 | Included (payer added per clarification) |
| 4 | Notification system | FR-006 | Included as in-app notification |
| 5 | Approval workflow | FR-005 | Included, single reviewer; multiple levels deferred |
| 6 | Balance tracking | FR-007 | Included |
| 7 | Payment tracking | FR-008 | Included |
| 8 | Reporting | FR-009 | Basic summaries and date range; other parameters deferred |
| 9 | OS supported (web primary, Android, iOS) | NFR-001 | Not delivered by Swing MVP; remains full-product scope |

## Implementation Decisions (Not Customer Requirements)

- Java 21, Maven, Java Swing, JUnit 5, PlantUML
- Object-oriented design; GUI separated from business logic
- `BigDecimal` for all monetary calculations
- Requirement traceability via IDs
- Human review of AI-generated artifacts

## Open Questions

See `spec.md` → Open Questions. Unresolved items are not silently answered.