# Dispatch Reference

How many child agents to create, which lenses they take, and what to put in each prompt. Every child agent is read-only and receives the project root, the role name, and its scope.

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

**Explorer**: the module scope, the conclusions of earlier modules, and the question "what in this repository already does this, and how is it called, configured, and error-handled?" Include the repository's existing primitives when the module needs state, atomic consumption, locking, retry, or throttling: cache with TTL, distributed lock, rate limiter, queue, scheduler, each with one existing caller.

**Researcher**: always for a module that introduces new state, storage, locking, concurrency, or throttling, and for any other general technical problem. State the problem in generic technical terms plus the constraints that matter (validity period, attempt limits, one-time consumption). Leave project names, company names, and business details out of the prompt. Ask for the dominant mechanism first, not for validation of a candidate design. Use the `feature-dev-code-researcher` role.

**Architect** lenses, each scoped to this module and given the explorer and researcher evidence:

- Simplest reuse: the smallest change that reuses what exists, naming the simplest conventional option and the evidence that it falls short.
- Maintainable: readability, extension points, fit with the existing layering.
- Risk and compatibility (high risk only): security, migration, rollback, concurrency.

Use the `feature-dev-code-architect` role. The advice covers this module and the interfaces it shares; a whole-feature design is out of scope.

## Step 5: Reviewer Lenses

Correctness and regression; architecture, conventions, and unneeded complexity; tests, security, and migration (the third lens joins for high-risk work, medium work covers it inside the first two).

Prompt each reviewer with the `feature-dev-code-reviewer` role and a stable envelope:

- Project root.
- Review lens as emphasis, not exclusion.
- Target selector: workspace, commit hash, or `from`/`to` refs.
- Relevant plan files (`architecture.md`, `design-review.md`), the repository rules or `conventions.md`, and verification already run.
- Optional background, a repeatable diff command, or a diff artifact.

Leave file selection and exclusions to the reviewer unless the user narrowed the scope.
