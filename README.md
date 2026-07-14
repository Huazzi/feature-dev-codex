# Feature Dev Codex

A Codex-native plugin for medium and large feature development. It uses independent exploration, architecture, and review passes; persistent planning files; and explicit approval gates for important decisions.

## What It Provides

- `feature-dev-codex`: the orchestrator and only role allowed to edit code or planning files.
- `feature-dev-code-explorer`: read-only codebase exploration role.
- `feature-dev-code-architect`: read-only architecture role.
- `feature-dev-code-reviewer`: read-only post-implementation review role.

## Workflow

For medium and high-risk work, the orchestrator creates child agents for three independent passes:

1. Exploration: map existing patterns, data flow, tests, and risks.
2. Architecture: compare implementation approaches and recommend one.
3. Review: independently assess the completed diff for correctness, regressions, conventions, tests, and risk.

Only the orchestrator edits source code. Child agents return structured reports, which prevents concurrent write conflicts.

## Approval Gates

- High-risk scope and critical decisions require explicit user approval.
- Every medium or high-risk architecture selection requires explicit approval before implementation.
- Review findings require an explicit user-approved disposition before review-driven changes or completion.
- High-risk implementation cannot bypass the architecture gate, including when asked to "just do it".

High-risk work includes auth, permissions, secrets, PII, payments, irreversible writes, database or data migrations, public API changes, concurrency, distributed behavior, security-sensitive changes, and broad cross-system refactors.

## Persistent Planning

The plugin uses a project-local `.planning/<feature-slug>/` directory with:

- `task_plan.md`
- `findings.md`
- `architecture.md`
- `review.md`
- `progress.md`

It is compatible with the `planning-with-files` and `planning-with-files-zh` file protocol when either is installed, but does not require either plugin.

## Installation

Install this plugin from a Codex marketplace or add it as a local personal plugin. Start a new Codex thread after installation so the new skills are available.

## Attribution

This plugin was independently implemented for Codex and was inspired by the workflow structure of Anthropic's Claude Code `feature-dev` plugin. No source files from the Anthropic project are included here. See [ATTRIBUTION.md](ATTRIBUTION.md).
