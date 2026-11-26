# Decision Evidence

Use in Steps 1–3 when classifying inputs, choosing an implementation, and checking the plan before Gate 1. Record the results in the existing planning files.

## Classify the Inputs

| Type | Meaning | Treatment |
| --- | --- | --- |
| Business requirement | Observable behavior and acceptance criteria | Each candidate must satisfy it; cite its source. |
| Current explicit technical constraint | A technology or integration boundary the user or applicable repository rules currently require | Preserve it. Explain conflicts and obtain a changed decision before departing from it. |
| Historical choice | An earlier proposed or approved implementation recorded in supplied material | Re-check its rationale and applicability. Approval proves a past choice, not technical necessity; present proposed changes at Gate 1. |
| Verified repository fact | An inspected capability, caller, configuration, or test result | State exactly what it proves, with a path; distinguish available code from runtime verification. |

Keep agent assumptions and unknowns separate. If the user's current request adopts an earlier choice as mandatory, treat it as a current constraint. If that status is ambiguous and changes the design, ask a focused clarification while continuing independent work. Merely finding no existing implementation does not validate the historical choice.

## State Before Storage

For work involving state, establish only the applicable requirements:

- Lifetime and what happens on expiry or state loss: restart, retry, recovery, or rejection.
- Operations that must succeed together, their failure scenarios, and the source of that requirement.
- Behavior on timeouts, uncertain results, duplicates, and concurrent requests.
- Audit content and retention, distinguished from transient operational state.

Mark unspecified behavior as unknown. Do not turn an implementation convenience into a product guarantee. If choosing storage in one module would constrain another, jointly compare the affected choices before either is fixed. If discovered later, reopen the earlier choice too. An approved external boundary may legitimately constrain the comparison; an agent's own provisional choice may not prove an alternative impossible.

## Compare Candidates

Start with the simplest plausible candidate that fits current constraints. Map its behavior to requirements and inspect reusable code and relevant public evidence.

- **Sufficient**: adopt it and state the supporting evidence. Small adapters or new business methods may be necessary without rejecting the underlying simple mechanism.
- **Insufficient**: name the unmet requirement, a concrete failure scenario, and evidence. Explain how the added complexity resolves that gap and what it costs.
- **Unknown**: route the missing fact or decision using [clarification.md](clarification.md). Execute available technical checks and track external evidence requests with completion conditions. Keep the recommendation provisional; absence of verification does not itself prove a candidate inadequate.

Compare candidates under the same requirements and failure assumptions. A defect in a wrapper calls for evaluating repair or a safe adapter, not automatically rejecting the underlying technology. No storage engine or library is the universal default.

## Evidence Check Before Gate 1

The orchestrator checks the reasoning, not just whether fields are filled:

| Check | Required response if it fails |
| --- | --- |
| Does each rejection trace to a requirement/current constraint and a concrete failure, rather than prior approval alone? | Re-evaluate the candidate; retain the historical decision as context. |
| Does one provisional module choice provide the sole reason for another? | Reopen the coupled choices and compare coherent combinations. |
| Do sources support the actual selection claim? A safety rule, an API capability, and an implementation example prove different things. | Narrow the claim or research the unanswered question. Links alone are not evidence of suitability or prevalence. |
| Does a repairable local defect exclude an entire mechanism without comparing repair cost? | Evaluate the repair/adapter option under the same criteria. |
| Do the candidates actually vary the choice being decided? | Include the relevant simple candidate; avoid two variants that silently fix the disputed choice. |
| Does each optional alternative address the same decision with a real trade-off under the same constraints? | Split independent decisions; move rejected candidates out of selectable options. A single supported recommendation is enough. |
| Are facts essential to the approach verified, with actionable checks for remaining gaps? | Execute available checks or request specific evidence. Keep a discussion draft while essential checks are unresolved; schedule non-blocking checks for the relevant later step. |

Record the outcome and unresolved gaps in `findings.md`. Unsupported recommendations return to Step 2; a genuine business trade-off goes to the user. This is an agent quality check, not an additional approval gate.
