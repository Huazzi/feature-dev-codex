# Findings

## Verified Facts

- 

## Written Repository Rules and Conventions

- 

## Inherited Decisions

Technical directions already approved in the requirement input, and storage, tables, or libraries it mandates. Re-check each in Step 2; note whether it still holds.

| Decision | Source (file:line, wording) | Re-checked against | Outcome |
| --- | --- | --- | --- |

## Entry Points and Data Flow

- 

## Module: <name>

### Reuse Comparison

| Need | Existing implementation (path) | Decision: reuse / extend / new | Evidence or reason |
| --- | --- | --- | --- |

When the module needs state, atomic consumption, locking, retry, or throttling, include the repository's existing primitives as needs: cache with TTL, distributed lock, rate limiter, queue, scheduler. Cite the class and one existing caller.

### Public Practice (general technical problems only)

| Question | Source link | Type | Accessed | Takeaway | Applicability check |
| --- | --- | --- | --- | --- | --- |

### Recommended Approach

- Recommendation:
- Simplest conventional option:
- Why it is not enough (evidence: repository path or source):
- Not doing:
- Open questions: (write unresolved items as `[NEEDS CLARIFICATION: ...]`)

### Essential Files

- 

## Cross-Module Contracts

- 

## Design Couplings

Choices in one module that force a constraint on another. Each coupling is a decision-list item unless the reason is the requirement itself.

- 

## Risks and Open Questions

- 
