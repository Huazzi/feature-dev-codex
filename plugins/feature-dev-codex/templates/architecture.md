# Architecture Decisions

Plan for agents. Keep the recommended approach and concrete paths per module, marked proposed until approved; useful alternatives live in the decision list of `design-review.md`. Mark unanswered user decisions as `[NEEDS CLARIFICATION: ...]`; reference technical check IDs from `task_plan.md` for unverified facts. Direction approval does not resolve those checks.

## Context

- Feature:
- Risk classification:
- Modules (see `task_plan.md`):
- Current constraints and historical choices being retained or changed (input IDs from `findings.md`):

## Joint Decisions (when applicable)

| ID | Affected modules | State / consistency requirement and source | Recommended choice and evidence | Proposed / approved |
| --- | --- | --- | --- | --- |

## Module: <name>

- Decision:
- Evidence: existing code paths reused or extended; public practice links
- Technical check IDs and readiness: essential blockers versus later-stage checks
- Files to create or modify:
- Interfaces shared with other modules:
- Joint decision references and applicable state/failure requirements:
- Data flow and error handling:
- Acceptance scenarios covered (ids from `task_plan.md`):
- Test plan and verification commands:
- Compatibility or rollback notes:
- Not doing:

## Complexity Justification

One row per new component, store, or abstraction. A new business method or adapter can be part of the simplest sufficient solution. Explain added complexity only where an evidenced gap requires it. Leave the table empty when nothing new is introduced.

| New element | Requirement served | Simplest candidate and sufficiency verdict | Evidence; gap and remedy if more complex |
| --- | --- | --- | --- |

## Decisions Pending User

Copied into the decision list of `design-review.md`.

| # | Approval item | Recommendation and evidence | Impact / cost; useful alternative only if needed |
| --- | --- | --- | --- |

## Gate 1 Approval

- Readiness and blocking check IDs:
- Approved decisions:
- User approval:
- Approval timestamp:
- Material changes after approval:
