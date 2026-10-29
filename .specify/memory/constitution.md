<!--
Sync Impact Report
Version change: uninitialized template -> 1.0.0 (initial adoption)
Modified principles: none; all project principles are new
Added sections: Technology and Product Constraints; Development Workflow and Quality Gates
Removed sections: none; template placeholders were replaced
Follow-up TODO: none
-->

# SplitSmart Constitution

## Core Principles

### I. Java 21 and Maven

Project Java source MUST target Java 21, and Maven MUST be the authoritative build and dependency-management tool. The Maven build MUST declare the Java release level and provide reproducible compile and test entry points. This keeps local development and automated checks consistent.

### II. Swing Is a Desktop Prototype

The Java Swing application MUST be treated as a temporary desktop prototype. It MUST NOT be described as replacing the customer's requirement for a primarily web-based product with Android and iOS apps. Any change to that customer scope requires explicit approval and an update to the requirements record.

### III. Object-Oriented Design

Production code MUST assign each class a clear responsibility, encapsulate state, and keep dependencies explicit. New abstractions MUST reduce coupling or clarify a domain responsibility; design patterns MUST NOT be added without a concrete need. This keeps the prototype understandable and testable.

### IV. Separate GUI and Business Logic

Swing components and event handlers MUST be limited to presenting information, collecting input, and invoking application behavior. Expense splitting, approval rules, payments, and balance calculations MUST live outside GUI classes and MUST be testable without launching the interface. This prevents UI changes from silently changing financial rules.

### V. JUnit 5 Verification

Automated Java tests MUST use JUnit 5. Business rules, validation, split calculations, approval state transitions, and balance updates MUST have focused tests; defects MUST receive regression tests. A change is not complete until the relevant Maven test checks pass.

### VI. PlantUML Design Diagrams

Architecture and interaction diagrams maintained for the project MUST use PlantUML source. Diagrams MUST reflect the implemented responsibilities and important flows, and MUST be updated when a change makes them inaccurate. Text-based diagrams keep design history reviewable alongside the project.

### VII. Exact Monetary Arithmetic

Money and monetary calculations MUST use `BigDecimal`; binary floating-point types MUST NOT represent money. Rounding scale and mode MUST be explicit at defined currency boundaries, and calculated shares MUST reconcile exactly to the expense total under the selected policy. This avoids hidden precision errors in shared balances.

### VIII. Trace Requirements and Preserve Their Source

Customer requirements and implementation decisions MUST be labeled and documented separately. Each customer requirement MUST have a stable identifier, and relevant design notes and tests MUST reference that identifier. An implementation choice MUST NOT be presented as a customer request, and implementation scope MUST NOT silently remove or rewrite customer requirements.

### IX. Human Review of AI Output

AI-generated requirements, diagrams, code, tests, and documentation MUST be reviewed and verified by a responsible human before acceptance. Reviewers MUST check correctness, requirement alignment, security-sensitive behavior, and test results; AI output alone is not evidence that a change is correct.

## Technology and Product Constraints

- The Java desktop prototype MUST use Java 21, Maven, and Java Swing. Tests MUST use JUnit 5, and project design diagrams MUST use PlantUML.
- The customer's full-product platform requirement remains a primarily web-based product with Android and iOS apps. The desktop prototype does not satisfy those platforms.
- The customer request in `UserRequirement.md` is the authoritative source for customer scope. The feature specification records its requirements separately from project implementation decisions.
- Expense, split, payment, and balance behavior MUST follow the applicable requirement identifiers and the explicit monetary rules in this constitution.

## Development Workflow and Quality Gates

- Before implementation, identify the customer requirement IDs addressed by the change and record any implementation decisions separately.
- Before accepting a change, run the relevant Maven/JUnit 5 tests and review the result. Add regression coverage for corrected defects and financial edge cases.
- Review updates to PlantUML diagrams when architecture or key interactions change.
- A human reviewer MUST approve AI-generated project artifacts and confirm that the change preserves customer requirements.
- Code review MUST check this constitution, requirement traceability, GUI/business-logic separation, and exact monetary arithmetic.

## Governance

This constitution governs project decisions and reviews. Amendments MUST be made in `.specify/memory/constitution.md`, reviewed by the project owner, and accompanied by an updated version and amendment date. Versioning follows semantic versioning: MAJOR for incompatible principle or governance changes, MINOR for new principles or materially expanded requirements, and PATCH for clarifications and non-semantic wording changes. Project reviews MUST verify compliance with the principles and record approved exceptions with their rationale and scope. No exception may silently change a customer requirement.

**Version**: 1.0.0 | **Ratified**: 2026-10-29 | **Last Amended**: 2026-10-29