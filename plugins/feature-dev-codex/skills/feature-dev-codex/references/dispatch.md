# Dispatch Reference

How many child agents to create, which lenses they take, and what to put in each prompt. Every child agent is read-only and receives the project root, the role name, its scope, and relevant classified inputs from `findings.md`. A scope may include coupled modules whose state choices must be designed together.

## Scaling

Lens count follows uncertainty, never a fixed quota.

| Situation | Agents |
| --- | --- |
| Step 1, whole requirement | Medium: 2 explorers. High risk: 3 explorers. |
| Step 2, module that only reuses existing code | 1 explorer. |
| Step 2, module with a real design choice | 1 explorer, plus 1 researcher for a general technical problem, plus 2 architects. High risk: 3 architects. |
| Step 5, review | Medium: 2 reviewers. High risk: 3 reviewers. |

## Step 1: Explorer Lenses (whole requirement)

- Entry points and call chain: where the feature touches the system today.
- Similar features and reusable implementations: what earlier work already solves.
- Conventions and test boundaries: written rules, structure, test style, risk areas.

Medium work uses the first two lenses; the third joins for high-risk work or when no written rules exist.

Prompt: the feature request, the lens, the instruction to use the `feature-dev-code-explorer` role, and a concise structured report with file references.

## Step 2: Module Lenses

**Explorer**: the scope, earlier conclusions with their sources and proposed/approved status, and the question "what in this repository already does this, and how is it called, configured, and error-handled?" Include applicable primitives for the actual state, atomicity, or throttling needs, each with an existing caller. A candidate technology is not itself a requirement.

**Researcher**: always for a module that introduces new state, storage, locking, concurrency, or throttling, and for any other general technical problem, even when existing infrastructure is reused. Supply the open engineering choice, behavioral requirements, current constraints, and separately labeled historical choices. Remove private identifiers while preserving relevant protocols, integration boundaries, deployment limits, and user interaction requirements. Ask for concrete implementations and firsthand practice that solve the behavior, not a defense of a chosen mechanism. Use the `feature-dev-code-researcher` role.

**Research handoff**: preserve questions/queries, sources with supported claims and limits, and child reports or source-backed excerpts in `findings.md`. Before giving advice to architects, check whether they can explain how a candidate works, what existing practice demonstrates, why it may fit, and which conditions would invalidate it. Generic norms, API names, or a list of URLs alone do not answer solution discovery: return the unanswered choice for targeted research. When direct evidence remains unavailable, hand off an explicitly provisional report with search limits, documented building blocks versus inferred combinations, and actionable technical checks under the clarification protocol; do not certify it as established practice.

**Architect** lenses, each scoped to the module or coupled group and given the explorer and researcher evidence, classified inputs, and applicable state/failure requirements:

- Simplest reuse: evaluate whether the simplest plausible candidate meets the requirements; recommend it when sufficient.
- Maintainable: readability, extension points, fit with the existing layering.
- Risk and compatibility (high risk only): security, migration, rollback, concurrency.

Use the `feature-dev-code-architect` role. Collect explorer/researcher evidence before dispatching architects. Advice covers the assigned scope and shared choices; unrelated whole-feature redesign is out of scope.

When asking for architecture advice on a material choice, ask for the concrete request, failure, or retry most likely to invalidate the recommendation. Before accepting the advice, follow that scenario from the user's action to the observable result. At each step that the choice depends on, identify what the public example demonstrates, what an existing repository caller actually does, or which contract is still assumed.

When recommending that several changes commit together, interrupt the flow between the changes that justify that boundary. Explain what remains after a known failure, what the user can do on retry, and whether that guarantee is required or proposed. Cover an unknown commit outcome separately when it changes the answer.

Check available code or sources for any missing link. If it still depends on outside evidence, record the specific sample or check needed and what result would change the choice. Keep the choice provisional in both the architecture and the human review draft. Preserve the walkthrough briefly in `findings.md` and carry its decisive step, user-visible consequence and status into `design-review.md`.

## Step 5: Reviewer Lenses

Correctness and regression; architecture, conventions, and unneeded complexity; tests, security, and migration (the third lens joins for high-risk work, medium work covers it inside the first two).

Prompt each reviewer with the `feature-dev-code-reviewer` role and a stable envelope:

- Project root.
- Review lens as emphasis, not exclusion.
- Target selector: workspace, commit hash, or `from`/`to` refs.
- Relevant plan files (`architecture.md`, `design-review.md`), the repository rules or `conventions.md`, and verification already run.
- Optional background, a repeatable diff command, or a diff artifact.

Leave file selection and exclusions to the reviewer unless the user narrowed the scope.
