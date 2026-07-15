# Feature Dev Codex

[English](README-En.md)

一个面向 Codex 的中大型功能开发插件。它将独立的代码库探索、架构设计和实现后审查组织成多子 agent 工作流，并用可恢复的计划文件和显式审批门控制关键决策。

> 适用于需要设计取舍、跨文件改动或风险控制的功能开发；不适合只有单个明确改动点的简单任务。

## 快速开始

在安装完成后的**新任务**中，直接说明要开发的功能，并显式调用 Skill：

```text
使用 feature-dev-codex 规划并开发用户登录功能。
先进行代码库探索和架构方案比较；在我批准架构前不要修改生产代码。
```

对于中等复杂度或高风险任务，插件会先完成探索与架构设计，等待你批准后才开始实现；实现后还会独立审查变更。

## 提供的 Skill

- `feature-dev-codex`：编排器，也是唯一允许修改生产代码、测试和计划文件的角色。
- `feature-dev-code-explorer`：只读代码库探索角色。
- `feature-dev-code-architect`：只读架构设计角色。
- `feature-dev-code-reviewer`：只读实现后审查角色。

## 适用范围

优先在以下场景显式使用 `feature-dev-codex`：

- 多文件功能开发，或新增接口、数据流、跨层逻辑；
- 存在多种可行实现，需要比较架构方案；
- 需要把探索结论、决策和实施进度保留在仓库中；
- 身份认证、权限、迁移、并发、支付等高风险改动。

对于拼写修正、单文件且行为明确的低风险改动，直接说明需求通常更高效，无须启用完整工作流。

## 工作流

面对中等复杂度或高风险任务，编排器会创建子 agent，完成三轮彼此独立的 pass：

1. **探索**：梳理相似功能、数据流、测试方式、项目规范和风险边界。
2. **架构**：比较实现方案，给出建议、文件变更图、测试方案及风险分析。
3. **审查**：在实现与初步验证后，独立检查正确性、回归、规范、测试与风险。

子 agent 均为只读，只返回结构化结论；只有编排器可以写入代码和计划文件，避免并发修改冲突。

## 审批门

本 plugin 不会把关键决策悄悄推进：

- 高风险任务的范围、关键决策和未决问题必须先获得明确批准。
- 所有中等复杂度和高风险任务的架构选择，必须展示方案、取舍、文件范围和测试计划，并在获得明确批准后才能开始修改生产代码。
- 实施后的审查发现必须先展示给用户；用户批准处置方式后，才会修复审查问题或宣布任务完成。
- 高风险任务不能跳过架构审批门，即使用户要求“直接做”。

高风险包括身份认证、权限、密钥、PII、支付、不可逆写入或删除、数据库或数据迁移、公共 API/兼容性变更、并发或分布式行为、安全敏感改动，以及跨系统的大范围重构。

## 持久化计划

每个功能在项目目录中使用独立的规划目录：

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  review.md
  progress.md
```

这些文件记录阶段状态、探索事实、架构方案与批准、审查结论和实施历史，避免在上下文压缩或新会话后丢失关键决策。

它兼容 `planning-with-files` 与 `planning-with-files-zh` 的 `.planning/` 文件协议；即使未安装这两个 skill，plugin 也会自行维护同一套文件。

## 仓库结构

```text
.agents/
  plugins/
    marketplace.json              # Marketplace 清单
plugins/
  feature-dev-codex/
    .codex-plugin/plugin.json     # 插件清单
    skills/
    templates/
README.md                          # Marketplace 使用说明
README-En.md
ATTRIBUTION.md
```

## 安装与使用

### 通过 Codex 桌面端安装（推荐）

1. 打开 Codex 桌面端，进入 **Plugins**。
2. 点击右上角 **Add** → **Add Marketplace** → **Add from a repository**。
3. 输入仓库地址：

   ```text
   https://github.com/Huazzi/feature-dev-codex
   ```

4. 点击 Sync。
5. 在 Huazzi Plugins 中找到 Feature Dev Codex，点击 Install。
6. 确认插件出现在 Installed 区域。
7. 新建一个 Codex 任务后再使用插件。

> 不同版本的 Codex 界面文案可能略有不同；关键是先添加该 Git 仓库作为 Marketplace，再安装其中的 `feature-dev-codex` 插件。

### 验证安装

对中大型功能使用“快速开始”中的提示后，插件会在项目中创建：

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  review.md
  progress.md
```

看到该目录被创建，并且 Codex 在实现前要求你批准架构，即表示插件已正常加载。

### 使用方式

为获得稳定、可预期的行为，请在需求中显式提及 Skill。可以使用英文：

```text
Use feature-dev-codex to plan this feature.
```

或中文：

```text
使用 feature-dev-codex 执行高风险功能开发流程。
```

## 插件更新

本节面向两类场景：

- **插件维护者**：修改本仓库的 Skill、模板或插件清单后发布更新；
- **插件使用者**：已安装插件，希望获取最新版本。

> 更新后的 Skill 通常在新任务中加载。完成更新后，建议重新安装插件并新建任务验证。

### 使用者：更新已安装插件

1. 打开 Codex 的 **Plugins** 页面。
2. 找到 **Feature Dev Codex**。
3. 执行 **Update** 或重新执行 **Install**。
4. 安装完成后，**新建一个 Codex 任务**。
5. 按“快速开始”中的提示，在新任务中验证。

如果插件正常工作，它会在项目中创建类似目录：

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  review.md
  progress.md
```

### 为什么建议新建任务？

Skill、工具和插件元数据通常在任务启动时加载；已有任务可能继续使用旧版本的上下文和 Skill 定义。因此，完成更新后应新建任务再测试：

```text
更新插件 ≠ 已有任务自动获得更新
```

### 维护者：发布一次更新

在包含 `.codex-plugin/plugin.json` 的插件根目录执行以下步骤。

#### 1. 修改并验证插件

完成 Skill、模板或 `plugin.json` 的修改后，先运行验证：

```powershell
$pluginRoot = (Get-Location).Path

python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\validate_plugin.py" `
  $pluginRoot
```

验证失败时不要发布；应先修复 `plugin.json`、目录结构或 Skill 文件中的问题。

#### 2. 更新 Codex cachebuster

Codex 可能缓存已安装插件。运行 cachebuster 脚本会更新
`.codex-plugin/plugin.json` 的 `version` 字段，使 Codex 将本次内容视为新版本：

```powershell
python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\update_plugin_cachebuster.py" `
  $pluginRoot
```

例如：

```text
0.1.0
```

会变为类似：

```text
0.1.0+codex.local-20260715-143000
```

规则如下：

- 正式功能发布时，可先按语义化版本更新基础版本，例如 `0.1.0` → `0.1.1`；
- cachebuster 用于让 Codex 重新加载内容，不应只为了刷新缓存而不断增加 `0.1.0`、`0.1.1` 这类数字版本；
- 每次更新应替换旧的 `+codex.*` 后缀，而不是叠加多个后缀；
- 不要在日常更新时手动修改 marketplace 配置。

#### 3. 再次验证并推送

```powershell
python `
  "$env:USERPROFILE\.codex\skills\.system\plugin-creator\scripts\validate_plugin.py" `
  $pluginRoot

git status
git add .
git commit -m "feat: update feature-dev-codex"
git push
```

提交前请确认仅包含本次发布需要的文件。提交中通常应包含：

- 修改后的 Skill、模板或插件代码；
- 更新后的 `.codex-plugin/plugin.json`；
- 必要的 README 或变更说明。

### 常见问题

| 现象                                | 原因与处理方式                                                               |
| ----------------------------------- | ---------------------------------------------------------------------------- |
| 更新代码后行为没有变化              | 未更新 cachebuster，或未重新安装插件                                         |
| 已重新安装但仍像旧版本              | 正在使用旧任务；请新建任务                                                   |
| 验证脚本失败                        | 修复 `.codex-plugin/plugin.json`、Skill 元数据或目录结构后再发布             |
| 插件在 Personal 中可见但无法使用    | 只完成了 marketplace 发现，尚未完成 Install；请确认它出现在 Installed 区域   |

## 归属说明

本 plugin 是为 Codex 独立实现的工作流，受到 Anthropic Claude Code 的 `feature-dev` plugin 工作流结构启发。本仓库不包含 Anthropic 项目的源文件、命令、agent 定义或文档文本。详见 [ATTRIBUTION.md](ATTRIBUTION.md)。
