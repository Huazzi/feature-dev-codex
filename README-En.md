# Feature Dev Codex

[简体中文](README.md)

A Codex plugin for medium-to-large feature development. It organizes independent codebase exploration, architecture design, and post-implementation review into a multi-agent workflow, using recoverable planning files and explicit approval gates to control key decisions.

> Best for feature work that involves design trade-offs, cross-file changes, or risk control. It is not intended for simple tasks with a single, clearly defined change.

## Quick Start

In a **new task** after installation, describe the feature to build and explicitly invoke the Skill:

```text
Use feature-dev-codex to plan and build a user login feature.
Explore the codebase and compare architecture options first; do not modify production code until I approve the architecture.
```

For medium-complexity or high-risk tasks, the plugin first completes exploration and architecture design, waits for your approval, then begins implementation. It also independently reviews the resulting changes.

## Included Skills

- `feature-dev-codex`: the orchestrator and the only role permitted to modify production code, tests, and planning files.
- `feature-dev-code-explorer`: a read-only codebase-exploration role.
- `feature-dev-code-architect`: a read-only architecture-design role.
- `feature-dev-code-reviewer`: a read-only post-implementation review role.

## When to Use It

Explicitly use `feature-dev-codex` when the work involves:

- multi-file feature development, or new APIs, data flows, or cross-layer logic;
- multiple viable implementations that require an architecture comparison;
- a need to retain exploration findings, decisions, and implementation progress in the repository;
- high-risk changes involving authentication, authorization, migrations, concurrency, payments, or similar concerns.

For typo fixes or low-risk, single-file changes with clear behavior, a direct request is usually more efficient and does not require the full workflow.

## Workflow

For medium-complexity and high-risk work, `feature-dev-codex` follows a **seven-phase lifecycle**. The three independent child-agent passes are not the entire workflow; they are embedded in Phases 2, 4, and 6.

1. **Discovery and Planning**: define the feature, success criteria, constraints, non-goals, and risk classification; create persistent planning files.
2. **Codebase Exploration Pass**: create 2-3 `feature-dev-code-explorer` child agents to independently map similar features, data flow, conventions, tests, and risks.
3. **Clarifying Questions and Scope Approval**: present unresolved decisions that materially affect the design; high-risk and critical decisions require explicit approval.
4. **Architecture Design Pass**: create 2-3 `feature-dev-code-architect` child agents to compare minimum-change, maintainability, and risk/compatibility approaches.
5. **Approved Implementation**: present the recommended architecture, trade-offs, file scope, migration or rollback plan, and test plan. Medium and high-risk work cannot modify production code before architecture approval.
6. **Quality Review Pass**: after implementation and focused verification, create 2-3 `feature-dev-code-reviewer` child agents to independently assess correctness, regression, conventions, tests, and risk.
7. **Approved Summary**: present the review disposition for explicit approval; then apply approved fixes, verify the result, and deliver the final summary.

Child agents are read-only and return structured reports. Only the orchestrator edits source code and planning files, preventing concurrent write conflicts.
## Approval Gates

This plugin does not silently advance critical decisions:

- High-risk scope, critical decisions, and unresolved questions require explicit approval.
- Every architecture decision for medium-complexity and high-risk work must present the options, trade-offs, file scope, and test plan, then receive explicit approval before production-code changes begin.
- Post-implementation review findings must first be presented to the user; the user must approve their disposition before review-driven fixes are made or the task is declared complete.
- The architecture gate cannot be skipped for high-risk work, even when the user asks to “just do it.”

High-risk work includes authentication, authorization, secrets, PII, payments, irreversible writes or deletions, database or data migrations, public API or compatibility changes, concurrent or distributed behavior, security-sensitive changes, and broad cross-system refactors.

## Persistent Planning

Each feature uses its own project-local planning directory:

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  review.md
  progress.md
```

These files retain phase status, exploration facts, architecture options and approvals, review conclusions, and implementation history, preventing important decisions from being lost after context compaction or across tasks.

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
  review.md
  progress.md
```

If the directory is created and Codex asks you to approve the architecture before implementation, the plugin has loaded correctly.

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

This plugin is an independent workflow implementation for Codex, inspired by the workflow structure of Anthropic Claude Code’s `feature-dev` plugin. This repository does not include source files, commands, agent definitions, or documentation text from Anthropic’s project. See [ATTRIBUTION.md](ATTRIBUTION.md).
