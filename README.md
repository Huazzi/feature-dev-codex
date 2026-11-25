# Feature Dev Codex

[English](README-En.md)

一个面向 Codex 的中大型功能开发插件。它先把需求拆成可独立验证的小模块，逐个模块优先复用仓库现有实现和公开成熟实践，再写出便于人工评审的中文方案；方案获批后按团队规范分模块实施，并由独立子 agent 审查。关键决策由可恢复的计划文件和两个显式审批门控制。

> 适用于需要设计取舍、跨文件改动或风险控制的功能开发；不适合只有单个明确改动点的简单任务。

## 快速开始

在安装完成后的**新任务**中，直接说明要开发的功能，并显式调用 Skill：

```text
使用 feature-dev-codex 规划并开发用户登录功能。
先探索现状、拆分模块并核实复用方案；在我批准方案前不要修改生产代码。
```

对于中等复杂度或高风险任务，插件会先探索现状并拆分模块，逐个模块核实可复用的现有实现和公开实践，再生成一份中文评审稿等待你批准；批准后才开始分模块实现，实现后还会独立审查变更。

## 提供的 Skill

- `feature-dev-codex`：编排器，也是唯一允许修改生产代码、测试和计划文件的角色。
- `feature-dev-code-explorer`：只读代码库探索角色，负责找可复用的现有实现和项目约定。
- `feature-dev-code-researcher`：只读公开实践调研角色，负责查官方文档和成熟项目的做法，并核对适用条件。
- `feature-dev-code-architect`：只读架构设计角色，针对单个模块给出简明建议。
- `feature-dev-code-reviewer`：只读实现后审查角色，同时检查缺陷、是否符合项目约定、是否引入不必要的复杂度。

## 推荐审查依赖

`feature-dev-code-reviewer` 会优先使用 `$open-code-review-delegate` 与 `ocr delegate` 来获得确定性的审查文件列表、排除原因和规则分组。该依赖不会随本 plugin 打包；如需最佳审查路径，建议单独安装 `open-code-review-delegate` skill，并确保本机可运行 `ocr delegate`。

如果该 skill 或 `ocr delegate` 不可用，`feature-dev-code-reviewer` 仍会在自身内部降级为基于 Git 或完整 diff artifact 的只读审查，并在报告中说明 OCR 规则和排除信息不可用。

## 适用范围

优先在以下场景显式使用 `feature-dev-codex`：

- 多文件功能开发，或新增接口、数据流、跨层逻辑；
- 仓库里可能已有类似实现，或属于验证码、幂等、缓存等通用技术问题，需要先复用再设计；
- 需要把探索结论、决策和实施进度保留在仓库中；
- 身份认证、权限、迁移、并发、支付等高风险改动。

对于拼写修正、单文件且行为明确的低风险改动，直接说明需求通常更高效，无须启用完整工作流。

## 工作流

对于中等复杂度或高风险任务，`feature-dev-codex` 使用**五步主流程**和两个审批门。需求先拆成模块，模块之间**串行**处理；多视角并行只发生在单个模块内部。拆分的目的是每次只解决一个小问题，而不是提高并行度。

1. **探索现状，拆分需求**：写清用户操作、预期结果和约束；查看入口、调用链和相似实现；先找仓库里已成文的规则（`docs/coding-styles`、`AGENTS.md` 等），没有才生成项目约定卡；拆成可独立验证的模块，并写明每块的职责、依赖和验收场景。需要澄清时，一次一问、最多 5 问，每问带推荐答案。
2. **逐模块核实复用方案**：对每个模块按固定顺序判断——先看仓库现有实现能否复用或扩展，再查公开成熟实践和官方文档并核对适用条件，都不满足才新设计。纯复用的模块只需 1 个探索 agent；有真实设计取舍的模块才开多视角 architect。
3. **撰写方便评审的方案**：生成给 Agent 用的 `architecture.md` 和给人读的 `design-review.md`。评审稿先讲用户流程和必要的技术背景，再放各模块方案、带推荐和理由的决策清单、明确不做的事项和复杂度说明。**Gate 1 评审方案。**
4. **按团队规范分模块实施**：每个模块动手前先声明适用的规则、参考实现和预计改动的文件；新代码落在参考实现所在的包和层；完成一块验证一块，结果记为 `verified / partial / failed`。方案与代码冲突时，只回到该模块重新核实。
5. **验证并评审**：先验证各模块的成功、边界和失败场景，再跑跨模块主流程，并检查已有调用方和测试；随后由独立 `feature-dev-code-reviewer` 审查。**Gate 2 处置审查结论。**

子 agent 均为只读，只返回结构化结论；只有编排器可以写入代码和计划文件，避免并发修改冲突。子 agent 的数量随模块的不确定性缩放，高风险任务使用三个视角。

## 审批门

本 plugin 不会把关键决策悄悄推进：

- **Gate 1（方案确认）**：中等复杂度和高风险任务必须展示 `design-review.md`（含模块拆分、各模块方案、待决策清单），获得明确批准后才能修改生产代码。方案需要调整时，只回到受影响的模块。
- **Gate 2（评审处置）**：实施后的审查发现必须先展示给用户；用户批准处置方式后，才会修复审查问题或宣布任务完成。
- 高风险任务不能跳过 Gate 1，即使用户要求“直接做”。
- 步骤 1 中的澄清提问不属于审批门，可以随时向你提问。

高风险包括身份认证、权限、密钥、PII、支付、不可逆写入或删除、数据库或数据迁移、公共 API/兼容性变更、并发或分布式行为、安全敏感改动，以及跨系统的大范围重构。

## 持久化计划

每个功能在项目目录中使用独立的规划目录：

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  design-review.md
  review.md
  progress.md
```

这些文件记录模块拆分、探索事实与复用依据、最终方案与批准、审查结论和实施历史，避免在上下文压缩或新会话后丢失关键决策。其中 `design-review.md` 是给人评审的中文方案，其余供 Agent 使用。

仓库没有成文规范时，还会生成项目级的 `.planning/conventions.md`（项目约定卡），记录包结构、分层、命名、异常与响应模型、HTTP 调用和测试写法，并附样例文件路径，供之后的需求复用。

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
docs/                              # 改造设计与验收用例
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

### 通过 Codex CLI 全局安装

在任意目录执行：

```powershell
codex plugin marketplace add Huazzi/feature-dev-codex --ref master
codex plugin list --marketplace huazzi-plugins --available
codex plugin add feature-dev-codex@huazzi-plugins
```

如果已经 clone 到本地，也可以直接把本地仓库作为 Marketplace：

```powershell
codex plugin marketplace add D:\Codes\plugins\feature-dev-codex
codex plugin add feature-dev-codex@huazzi-plugins
```

请使用完整选择器 `feature-dev-codex@huazzi-plugins`。如果本机曾经安装过同名的个人插件，直接写 `feature-dev-codex` 可能会匹配到其他 Marketplace 中的旧版本。

### 验证安装

对中大型功能使用“快速开始”中的提示后，插件会在项目中创建：

```text
.planning/<feature-slug>/
  task_plan.md
  findings.md
  architecture.md
  design-review.md
  review.md
  progress.md
```

看到该目录被创建，并且 Codex 在实现前要求你批准方案（Gate 1），即表示插件已正常加载。

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
  design-review.md
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

本 plugin 是为 Codex 独立实现的工作流，受到 Anthropic Claude Code 的 `feature-dev` plugin 工作流结构启发，并借鉴了 [github/spec-kit](https://github.com/github/spec-kit) 的部分机制（澄清提问格式、复杂度说明表、`NEEDS CLARIFICATION` 标记等）。本仓库不包含这些项目的源文件、命令或 agent 定义。详见 [ATTRIBUTION.md](ATTRIBUTION.md)。
