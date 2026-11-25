# Findings

## Verified Facts

- 

## Written Repository Rules and Conventions

- 

## Decision Inputs

Classify business requirements, current explicit technical constraints, historical choices, and verified repository facts. Record assumptions and unknowns separately; past approval alone does not prove technical necessity.

| ID | Input and type | Source (file:line, wording) | Evidence / current applicability | Decision or question |
| --- | --- | --- | --- | --- |

## Entry Points and Data Flow

- 

## Module: <name>

### Reuse Comparison

| Need | Existing implementation (path) | Decision: reuse / extend / new | Evidence or reason |
| --- | --- | --- | --- |

Describe behavioral needs before selecting primitives. For applicable state, atomicity, or throttling needs, cite a reusable class and an existing caller.

### State and Failure Requirements (when applicable)

| State / operation | Lifetime and loss behavior | Required atomicity and source | Uncertain result / retry behavior | Audit retention | Unknowns |
| --- | --- | --- | --- | --- | --- |

### Candidate Comparison

| Candidate | Requirements / constraints addressed (input IDs) | Sufficient / insufficient / unknown | Evidence or concrete gap | Remedy and cost if needed |
| --- | --- | --- | --- | --- |

### Public Practice (general technical problems only)

| Open question | Source link | Type / evidence kind | Accessed | Supported claim and limits | Applicability / remaining gap |
| --- | --- | --- | --- | --- | --- |

### Recommended Approach

- Recommendation:
- Simplest plausible candidate and sufficiency verdict:
- Requirement-to-evidence reasoning (gap and remedy only if insufficient):
- Not doing:
- Open questions: (write unresolved items as `[NEEDS CLARIFICATION: ...]`)

### Essential Files

- 

## Cross-Module Contracts

- 

## Design Couplings

Jointly compare choices that constrain each other before fixing either. Record the requirement/current constraint, affected modules, coherent candidates, and evidence; an earlier provisional choice is not proof of necessity.

- 

## Risks and Open Questions

- 

## Evidence Check Before Gate 1

- Requirement-based reasons for accepting/rejecting candidates:
- Joint choices reopened or confirmed, and evidence:
- Source claims and local repair options checked:
- Outcome: ready for user decisions / return affected choices to Step 2
- Remaining verification gaps:

## Evidence Appendix

Keep actual research questions/queries, retrieved-source details, and child reports or source-backed excerpts. Record role and scope so the handoff can be inspected. Separate failed retrievals and unverified leads from evidence; do not invent execution records.

- Role / scope:
- Original question and queries used:
- Report or evidence-backed excerpt:
- Sources and limits:
