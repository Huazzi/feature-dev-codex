# Attribution

Feature Dev Codex is an independent Codex plugin implementation inspired by the high-level workflow of Anthropic's Claude Code `feature-dev` plugin.

Upstream reference: https://github.com/anthropics/claude-code/tree/main/plugins/feature-dev

No source files, commands, agent definitions, or documentation text from the upstream project are included in this repository. This repository contains an independently written Codex workflow and documentation.

## github/spec-kit

Some mechanisms in workflow version 3 are adapted from [github/spec-kit](https://github.com/github/spec-kit) (MIT License, Copyright GitHub, Inc.):

- the clarification question format (one question at a time, a capped number of questions, "why it matters" and a recommended answer), in `references/clarification.md`;
- the three-column complexity justification table and the `[NEEDS CLARIFICATION: ...]` marker, in the plan templates;
- the idea of recording a stop with a stated reason, and of counting only a `verified` result as success.

The wording of the instructions and templates is written independently for this plugin; the table columns and question format follow the upstream structure. No other upstream files, commands, or documentation text are included.
