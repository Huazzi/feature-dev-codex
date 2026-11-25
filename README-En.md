# Feature Dev Codex

[简体中文](README.md)

A Codex plugin for medium-to-large feature development. It first splits a requirement into small, independently verifiable modules, then for each module prefers existing repository code and mature public practice, and writes a plan a human can review (in Chinese). After the plan is approved it implements module by module under the team's conventions, with an independent child-agent review. Recoverable planning files and two explicit approval gates control key decisions.

> Best for feature work that involves design trade-offs, cross-file changes, or risk control. It is not intended for simple tasks with a single, clearly defined change.

## Quick Start

In a **new task** after installation, describe the feature to build and explicitly invoke the Skill:

```text
Use feature-dev-codex to plan and build a user login feature.
Explore the current state, split it into modules, and verify what can be reused first; do not modify production code until I approve the plan.
```

For medium-complexity or high-risk tasks, the plugin explores the current state and splits the requirement into modules, verifies reusable existing implementations and public practice per module, then produces a Chinese review document and waits for your approval. Only then does it implement module by module, and it independently reviews the resulting changes.

## Included Skills

- `feature-dev-codex`: the orchestrator and the only role permitted to modify production code, tests, and planning files.
- `feature-dev-code-explorer`: a read-only codebase-exploration role that finds reusable implementations and project conventions.
- `feature-dev-code-researcher`: a read-only public-practice research role that checks official documentation and mature projects and verifies applicability.
- `feature-dev-code-architect`: a read-only architecture role that advises on a single module.
- `feature-dev-code-reviewer`: a read-only post-implementation review role that checks defects, conformance to project conventions, and unneeded complexity.

## Recommended Review Dependency

`feature-dev-code-reviewer` prefers `$open-code-review-delegate` with `ocr delegate` to obtain deterministic reviewable files, exclusion reasons, and rule groups. This dependency is not bundled with this plugin; for the best review path, install the `open-code-review-delegate` skill separately and make sure `ocr delegate` runs locally.

If that skill or `ocr delegate` is unavailable, `feature-dev-code-reviewer` still falls back internally to read-only Git-based or complete diff artifact review and reports that OCR rules and exclusions were unavailable.

## When to Use It

Explicitly use `feature-dev-codex` when the work involves:

- multi-file feature development, or new APIs, data flows, or cross-layer logic;
- work where the repository may already contain a similar implementation, or that is a general technical problem (verification codes, idempotency, caching), so reuse should come before design;
- a need to retain exploration findings, decisions, and implementation progress in the repository;
- high-risk changes involving authentication, authorization, migrations, concurrency, payments, or similar concerns.

For typo fixes or low-risk, single-file changes with clear behavior, a direct request is usually more efficient and does not require the full workflow.

## Workflow

For medium-complexity and high-risk work, `feature-dev-codex` follows a **five-step workflow** with two approval gates. Independent modules follow dependency order; choices sharing state or consistency requirements are designed jointly before module-by-module implementation. Multiple lenses examine the current module or coupled group so an early storage choice does not silently constrain later modules.

1. **Explore and split**: define user operations, results, and constraints; inspect entry points, call chains, and reusable implementations. Use written rules or create a conventions card when none exist. Distinguish business requirements, current explicit technical constraints, historical choices, and verified repository facts. Past approval alone does not prove necessity; current constraints remain binding. Record module responsibilities, dependencies, and acceptance scenarios, and clarify material ambiguities.
2. **Verify reuse per module**: inspect existing implementations and infrastructure, then public practice. For stateful work, establish lifetime, loss/retry behavior, atomic operations, and audit requirements before selecting storage. Adopt a sufficient simple candidate; justify extra complexity with a concrete gap and remedy. Unknowns are not rejection evidence. Compare shared choices jointly, dispatch architects for real trade-offs, and preserve research questions, sources, and child evidence in `findings.md`.
3. **Write a plan that is easy to review**: produce `architecture.md` for agents and `design-review.md` for humans, explaining behavior before decisions. Check for circular reasons, unsupported source claims, local defects used to reject whole mechanisms, and candidates that miss the actual choice. Failed checks return to the affected design choices; this is an agent check, not another approval gate. **Gate 1 approves the plan.**
4. **Implement module by module under team conventions**: before each module, declare the applicable rules, the reference implementation, and the files expected to change; place new code where the reference implementation lives; verify each module before the next and record `verified / partial / failed`. When the plan conflicts with the code, return only to the affected module.
5. **Verify and review**: verify success, boundary, and failure scenarios per module, run the cross-module main flow, and check existing callers and tests; then an independent `feature-dev-code-reviewer` pass. **Gate 2 decides the review disposition.**

Child agents are read-only and return structured reports. Only the orchestrator edits source code and planning files, preventing concurrent write conflicts. The number of child agents scales with a module's uncertainty; high-risk work uses three lenses.

## Approval Gates

This plugin does not silently advance critical decisions:

- **Gate 1 (plan approval)**: medium-complexity and high-risk work must present `design-review.md` and receive explicit approval before production-code changes begin. Decisions include new tables, migrations, dependencies or stores, proposed changes to historical choices or current constraints, and material joint choices. Revisit all affected modules when adjusting the plan, and synchronize both plan documents and the task plan.
- **Gate 2 (review disposition)**: post-implementation review findings must first be presented to the user; the user must approve their disposition before review-driven fixes are made or the task is declared complete.
- Gate 1 cannot be skipped for high-risk work, even when the user asks to “just do it.”
- Clarifying questions in Step 1 are not gates; the plugin may ask you at any time during that step.

High-risk work includes authentication, authorization, secrets, PII, payments, irreversible writes or deletions, database or data migrations, public API or compatibility changes, concurrent or distributed behavior, security-sensitive changes, and broad cross-system refactors.

## Persistent Planning

Each feature uses its own project-local planning directory:

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  design-review.md
  review.md
  progress.md
```

These files retain module splits, exploration facts and reuse evidence, the final plan and its approval, review conclusions, and implementation history, preventing important decisions from being lost after context compaction or across tasks. `design-review.md` is the Chinese plan written for human review; the other files are for agents.

When the repository has no written rules, the plugin also creates a project-level `.planning/conventions.md` (a conventions card) recording package structure, layering, naming, exception and response models, HTTP calls, and test style, each with a sample file path, for reuse by later features.

The plugin is compatible with the `.planning/` file protocol used by `planning-with-files` and `planning-with-files-zh`. It maintains the same files even when neither Skill is installed.

## Repository Structure

```text
.agents/
  plugins/
    marketplace.json              # Marketplace List
plugins/
  feature-dev-codex/
    .codex-plugin/plugin.json     # Plugins list
    skills/
    templates/
docs/                              # Redesign notes and acceptance cases
README.md
README-En.md
ATTRIBUTION.md
```

## Installation and Usage

### Install Through the Codex Desktop App (Recommended)

1. Open the Codex desktop app and go to **Plugins**.
2. Select **Add** → **Add Marketplace** → **Add from a repository**.
3. Enter the repository URL:

   ```text
   https://github.com/Huazzi/feature-dev-codex
   ```

4. Select **Sync**.
5. Find **Feature Dev Codex** under Huazzi Plugins and select **Install**.
6. Confirm that the plugin appears in the **Installed** area.
7. Create a new Codex task before using the plugin.

> Labels may vary slightly between Codex versions. The essential steps are to add this Git repository as a Marketplace, then install the `feature-dev-codex` plugin from it.

### Install Globally Through the Codex CLI

Run from any directory:

```powershell
codex plugin marketplace add Huazzi/feature-dev-codex --ref master
codex plugin list --marketplace huazzi-plugins --available
codex plugin add feature-dev-codex@huazzi-plugins
```

If you already cloned the repository locally, you can add the local checkout as the Marketplace:

```powershell
codex plugin marketplace add D:\Codes\plugins\feature-dev-codex
codex plugin add feature-dev-codex@huazzi-plugins
```

Use the full selector `feature-dev-codex@huazzi-plugins`. If another Marketplace on the same machine already contains a plugin with the same name, using only `feature-dev-codex` may install an older plugin from that other Marketplace.

### Verify Installation

Use the prompt in [Quick Start](#quick-start) for a medium-to-large feature. The plugin should create:

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  design-review.md
  review.md
  progress.md
```

If the directory is created and Codex asks you to approve the plan (Gate 1) before implementation, the plugin has loaded correctly.

### Invoking the Plugin

For stable, predictable behavior, explicitly name the Skill in your request. For example:

```text
Use feature-dev-codex to plan this feature.
```

Or in Chinese:

```text
使用 feature-dev-codex 执行高风险功能开发流程。
```

## Updating the Plugin

This section covers two audiences:

- **Plugin maintainers** who update this repository’s Skills, templates, or manifest;
- **Plugin users** who have installed the plugin and want the latest version.

> Updated Skills are normally loaded in new tasks. After an update, reinstall the plugin and validate it in a new task.

### Users: Update an Installed Plugin

1. Open **Plugins** in Codex.
2. Find **Feature Dev Codex**.
3. Select **Update**, or select **Install** again.
4. After installation completes, create a **new Codex task**.
5. Validate it with the prompt in [Quick Start](#quick-start).

When the plugin works, it creates a directory like this:

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  design-review.md
  review.md
  progress.md
```

### Why Create a New Task?

Skills, tools, and plugin metadata are typically loaded when a task starts. An existing task may continue using an older context and Skill definition, so test the update in a new task:

```text
Updating a plugin ≠ an existing task automatically receiving the update
```

### Maintainers: Publish an Update

Run the following steps from the plugin root that contains `.codex-plugin/plugin.json`.

#### 1. Modify and Validate the Plugin

After changing a Skill, template, or `plugin.json`, validate the plugin first:

```powershell
$pluginRoot = (Get-Location).Path

python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\validate_plugin.py" `
  $pluginRoot
```

Do not publish if validation fails. Fix `plugin.json`, the directory structure, or the Skill files first.

#### 2. Update the Codex Cachebuster

Codex may cache installed plugins. The cachebuster script updates the `version` field in `.codex-plugin/plugin.json`, allowing Codex to treat the changed contents as a new version:

```powershell
python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\update_plugin_cachebuster.py" `
  $pluginRoot
```

For example:

```text
0.1.0
```

becomes something like:

```text
0.1.0+codex.local-20260715-143000
```

Follow these rules:

- For a functional release, update the base version according to semantic versioning, for example `0.1.0` → `0.1.1`.
- Use the cachebuster to reload content; do not keep increasing numeric versions such as `0.1.0`, `0.1.1`, and so on merely to refresh the cache.
- Replace an existing `+codex.*` suffix rather than appending multiple suffixes.
- Do not manually modify Marketplace configuration for routine updates.

#### 3. Validate Again and Push

```powershell
python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\validate_plugin.py" `
  $pluginRoot

git status
git add .
git commit -m "feat: update feature-dev-codex"
git push
```

Before committing, ensure that only files required for this release are included. A release normally includes:

- updated Skill, template, or plugin files;
- the updated `.codex-plugin/plugin.json`;
- any necessary README or release-note changes.

### Troubleshooting

| Symptom | Cause and resolution |
| --- | --- |
| Behavior does not change after updating code | Update the cachebuster and reinstall the plugin. |
| The plugin still behaves like the old version after reinstalling | You are using an existing task. Create a new task. |
| The validation script fails | Fix `.codex-plugin/plugin.json`, Skill metadata, or the directory structure, then validate again. |
| The plugin is visible under Personal but cannot be used | The Marketplace has been discovered, but the plugin has not been installed. Confirm that it appears under **Installed**. |

## Attribution

This plugin is an independent workflow implementation for Codex, inspired by the workflow structure of Anthropic Claude Code’s `feature-dev` plugin, and it borrows some mechanisms from [github/spec-kit](https://github.com/github/spec-kit) (the clarification question format, the complexity justification table, and `NEEDS CLARIFICATION` markers). This repository does not include source files, commands, or agent definitions from these projects. See [ATTRIBUTION.md](ATTRIBUTION.md).
