# Clarification Protocol

Use for the Step 1 questions to the user and for the Gate 1 decision list. Adapted from the clarify command of github/spec-kit.

## Asking in Step 1

- Rank candidate questions by impact times uncertainty. Keep the ones that change the design or the tests; leave out questions the repository or public practice already answers.
- Ask at most 5 questions in Step 1, one at a time, revealing no later question in advance.
- Format each question:

  ```text
  **Question:** a full sentence ending in `?`
  **Why it matters:** one sentence on the stake for acceptance or shipping.
  **Recommended:** option X - one or two sentences of reasoning.
  ```

  Present 2 to 5 options in a short table when the question has several plausible answers.
- The user may answer with an option letter, "recommended", or their own words. When a reply is ambiguous, ask one quick disambiguation without using up a question.
- Record each accepted answer as `Q -> A` in the Clarification Log of `task_plan.md` and apply it to the relevant section of the plan.
- Stop when the ambiguities are resolved, the user says they are done, or the limit is reached.
- A question that arises after Step 1 goes into the Gate 1 decision list.

## Decision List (Gate 1)

Each item in `design-review.md` section 5 has: a question, why it matters, options with their costs, a recommendation with reasoning, and the cost of not taking the recommendation.
