# Clarification Protocol

Use throughout planning to separate user decisions from technical checks, and to write the Gate 1 decision list. Adapted from the clarify command of github/spec-kit.

## Route Open Questions

| Kind | Action |
| --- | --- |
| Business preference or scope trade-off | Explain the consequences and ask the user to decide. |
| Technical fact that determines whether the approach can work | Verify it now; keep affected choices provisional until evidence resolves it. |
| Implementation detail or environment check that does not change the approach | Schedule it for the relevant implementation or verification step, with a completion condition. |

Use available code, supplied materials, public documentation, and authorized checks before requesting information. If external evidence is needed, ask for the specific contract, sample, or access needed and explain which choice it affects; continue independent work. An unanswered technical question does not become a user preference after Step 1.

When missing supplier evidence blocks a candidate, check which parts of the guarantee the application could own using available interfaces and repository capabilities. Where both boundaries are plausible, compare them under the same requirements and failure scenarios. Identify the smallest check that could change the choice and the affected modules. Keep viable but unverified choices provisional, and draft application-owned contracts and acceptance scenarios that do not depend on the missing fact.

For each technical check, record in `task_plan.md`: missing evidence, next action and who can perform it or supply the input, completion condition and when it is needed, affected modules/choices and what changes if the assumption fails, and status with a result link. Execute available checks rather than only listing them. Keep evidence and results in `findings.md`; summarize only decision-relevant blockers in `design-review.md`, referencing the task instead of duplicating it.

Close a check on evidence, including evidence that rules out the approach; in that case reopen the affected choices and dependents. Approval of a direction does not close a technical check. Essential unresolved checks block implementation readiness; later-stage checks retain their due step without requiring all production integration tests before Gate 1.

## Asking in Step 1

- Rank candidate questions by impact times uncertainty. Keep the ones that change the design or the tests; leave out questions the repository or public practice already answers.
- Ask at most 5 questions in Step 1, one at a time, revealing no later question in advance.
- Format each question:

  ```text
  **Question:** a full sentence ending in `?`
  **Why it matters:** one sentence on the stake for acceptance or shipping.
  **Recommended (for a genuine choice):** the proposed answer and one or two sentences of reasoning.
  ```

  Present 2 to 5 options in a short table when the question has several plausible answers.
- The user may answer with an option letter, "recommended", or their own words. When a reply is ambiguous, ask one quick disambiguation without using up a question.
- Record each accepted answer as `Q -> A` in the Clarification Log of `task_plan.md` and apply it to the relevant section of the plan.
- Stop when the ambiguities are resolved, the user says they are done, or the limit is reached.
- After Step 1, use the routing above. Ask for essential information when needed; only genuine user decisions go into the Gate 1 decision list.

## Decision List (Gate 1)

Default to a recommendation with its evidence, impact/cost, and what needs approval. A single sufficient approach is a complete review item; approval of a new store or dependency does not require an alternative.

Add a comparison only for a useful alternative addressing the same decision under the same requirements and constraints. Explain the real trade-off; mark an unverified alternative as conditional. Record candidates that violate confirmed constraints as rejected, not selectable alternatives. A proposal to change a constraint must state the exact change and its impact. Split independent decisions rather than bundling unrelated choices into A/B.
