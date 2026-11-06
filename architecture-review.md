# SplitSmart Architecture Review

**Review status:** Baseline provisionally selected; no completed alternative proposal found. **Authority:** `specs/001-splitsmart-expense-sharing/spec.md`.

## Source inventory and gaps

| Source | Type and relevant content |
|---|---|
| `UserRequirement.md` | Customer-level requirements: accounts, groups, expenses, notifications, approval, balances, payments, reports, web/Android/iOS. |
| `specs/001-splitsmart-expense-sharing/spec.md` | Detailed authoritative requirements: FR-001–FR-009, NFR-001–NFR-003, scenarios, edge cases, success criteria, assumptions, and open questions. |
| `architecture-baseline.md` | Actual layered modular monolith proposal for Java Swing MVP; contains packages, entity outline, diagrams, traceability and quality considerations. |
| `prompts/architecture-alternative.md` | Prompt/instructions for producing an alternative; not a proposal. |
| `prompts/chatgpt-review.md`, `prompts/copilot-architecture.md`, `prompts/chatgpt/architecture.md` | Review or architecture-generation prompts; not alternative architecture proposals. |
| `review-report.md` | Existing baseline assessment; highlights financial, authorization, projection, and future-platform risks. |
| `consolidation-instructions.md` | Current deliverable and validation instructions; not product requirements. |

The baseline repeats proposed entity/data/UML concepts in several sections. Key issues requiring resolution were treating Balance both as source and derived data; underspecified remainder allocation and payment behavior; broad package list for a small MVP; and confusing Swing delivery with full-product platform scope. The clarified spec resolves payer identity, eligible reviewer, currency baseline, notification channel, and review count for MVP. Payment policy remains open. No alternative proposal or future web/mobile technology selection exists.

## Criteria and scoring method

Each available proposal is rated 1–10 per criterion; weighted points = rating * weight, yielding a score out of 10. A missing proposal is not scored.

| Criterion | Weight | Baseline score | Weighted points | Evidence / caveat |
|---|---:|---:|---:|---|
| Requirements alignment | 25% | 8 | 2.00 | Covers all capabilities; platform boundary must be explicit. |
| Domain and data model | 20% | 7 | 1.40 | Core entities present; original balance authority and payment rules needed clarification. |
| Security and data integrity | 15% | 6 | 0.90 | Security services identified; enforcement and transaction detail incomplete. |
| Maintainability and testability | 15% | 8 | 1.20 | Clear layers and testable domain; risk of too many premature classes. |
| Scalability/platform evolution | 10% | 6 | 0.60 | Reusable domain is promising; local desktop is not multi-client backend. |
| Implementation practicality | 10% | 8 | 0.80 | Proportionate modular monolith, though stack/storage choices need confirming. |
| Documentation quality | 5% | 7 | 0.35 | Thorough baseline; duplicated and inconsistent details needed consolidation. |
| **Weighted total** | **100%** |  | **7.25/10** | Assessment of proposal, not implementation. |

## Decision

Select the **layered modular monolith** provisionally for the MVP. It keeps Swing thin, centralizes financially sensitive rules, supports local relational persistence, and avoids distributed operations that have no demonstrated value at this scale. Retain repository adapters, independent domain/application behavior, and transactional use cases. Do not claim comparison against an alternative that is absent.

**Strengths:** coherent layers; one deployable unit; testable business rules; adequate baseline entities; plausible path to API-backed clients.

**Weaknesses and accepted trade-offs:** local persistence limits multi-device sharing and centralized recovery; no selected full-product hosting/API stack; additional layers may overcomplicate a small prototype; baseline needed sharper integrity, authorization, and payment rules.

**Rejected:** microservices for the MVP, due to operational and consistency overhead without independent scaling need. No unprovided alternative patterns are credited or rejected.

## Risks and decisions still needed

- Financial correctness: exact currency policy and rounding remain product assumptions; tested design must reconcile shares.
- Payment behavior: partial/overpayment unresolved; implement only after policy approval.
- Security: password reset, local file protection, and every-operation authorization need concrete review.
- Platform: web, Android, and iOS are full-product requirements; their architecture and technologies remain future work.
- Receipt/file coordination: file writes cannot join a relational transaction; cleanup/recovery is required.

## Requirement traceability

See `docs/requirements-traceability.md` for FR/NFR and success-criteria mapping to architecture, design, data, UML, and verification.
