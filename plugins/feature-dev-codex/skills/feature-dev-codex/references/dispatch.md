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

**Researcher**: always for a module that introduces new state, storage, locking, concurrency, or throttling, and for any other general technical problem, even when existing infrastructure is reused. Supply generic behavioral requirements, current constraints, and separately labeled historical choices. Leave project names, company names, and business details out. Ask how to meet the behavior, not how to defend a chosen store. Use the `feature-dev-code-researcher` role.

**Research handoff**: preserve actual questions/queries, retrieved sources with supported claims and limits, and child reports or source-backed excerpts in the `findings.md` evidence appendix. Check that the research answers the open choice before giving it to architects. If evidence is missing or access is blocked, keep the choice provisional and report the gap; a list of source URLs alone does not complete the handoff.

**Architect** lenses, each scoped to the module or coupled group and given the explorer and researcher evidence, classified inputs, and applicable state/failure requirements:

- Simplest reuse: evaluate whether the simplest plausible candidate meets the requirements; recommend it when sufficient.
- Maintainable: readability, extension points, fit with the existing layering.
- Risk and compatibility (high risk only): security, migration, rollback, concurrency.

Use the `feature-dev-code-architect` role. Collect explorer/researcher evidence before dispatching architects. Advice covers the assigned scope and shared choices; unrelated whole-feature redesign is out of scope.

## Step 5: Reviewer Lenses

Correctness and regression; architecture, conventions, and unneeded complexity; tests, security, and migration (the third lens joins for high-risk work, medium work covers it inside the first two).

Prompt each reviewer with the `feature-dev-code-reviewer` role and a stable envelope:

- Project root.
- Review lens as emphasis, not exclusion.
- Target selector: workspace, commit hash, or `from`/`to` refs.
- Relevant plan files (`architecture.md`, `design-review.md`), the repository rules or `conventions.md`, and verification already run.
- Optional background, a repeatable diff command, or a diff artifact.

Leave file selection and exclusions to the reviewer unless the user narrowed the scope.
