# Consolidation Analysis

## Phase 1: Source inventory

The customer source is `UserRequirement.md`; the authoritative detailed source is `specs/001-splitsmart-expense-sharing/spec.md`. `architecture-baseline.md` is an actual proposal for a layered Java Swing modular monolith. `prompts/architecture-alternative.md` asks for an alternative to be generated but is not that proposal. The ChatGPT and Copilot files under `prompts/` are prompts only. `review-report.md` is an existing review of the baseline. Consolidation directions are process instructions, not customer requirements.

## Coverage and conflicts

The spec already preserves the nine functional requirements, three NFRs, three user stories and acceptance scenarios, edge cases, six success criteria, implementation decisions, assumptions and open questions. No requirement renumbering or deletion is needed. Documents must preserve the platform requirement while identifying Swing as a temporary prototype. Payer is a required field distinct from submitter and participants; only a different group manager may review.

The baseline describes balances as both persisted entities and derived values. Consolidated design makes expense/share/approval/payment events authoritative and Balance a rebuildable projection. Existing high-level monetary policy is refined with deterministic minor-unit allocation. The specification's payment partial/overpayment policy is unresolved; no final rule is invented. No alternative architecture exists to score.

## Phase decisions

1. **Inspect:** inventory above; retain original proposal and prompts.
2. **Reconcile:** retain `spec.md` unchanged as it already contains all mandatory content and IDs.
3. **Select:** provisionally retain baseline modular monolith; weighted scoring and evidence are in `architecture-review.md`.
4. **Design:** see `architecture.md` and `design.md`.
5. **Data:** see `data-model.md`, including key constraints and worked financial cases.
6. **UML:** five PlantUML source files are in `docs/diagrams/`.
7. **Trace/validate:** see `docs/requirements-traceability.md`; cross-document consistency notes are below.

## Validation results

- FR-001–FR-009 and NFR-001–NFR-003 remain traceable.
- All three actor journeys, edge cases and SC-001–SC-006 remain in the authoritative spec.
- Swing remains prototype-only; future client stack is unspecified.
- Architecture dependencies point Presentation -> Application -> Domain; adapters implement persistence/infrastructure ports.
- Approval is PENDING -> APPROVED or REJECTED only, by a manager other than submitter.
- Shares reconcile exactly; only approved expenses contribute; payer and submitter are separate; payer may or may not be participant.
- Payment policy remains an open question across documents.
- Balance and report are derived; no double-counting source is introduced.
- Diagram sources use the names and relationships in the design/data model.

Rendered diagram images are not included in this workspace; PlantUML source is the editable deliverable. Rendering remains a documentation validation follow-up because no PlantUML renderer is installed in the available environment.
