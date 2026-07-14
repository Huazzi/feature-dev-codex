# Feature Dev Codex

[English](README.md)

一个面向 Codex 的中大型功能开发 plugin。它将独立的代码库探索、架构设计和实现后审查组织成多子 agent 工作流，并使用可恢复的计划文件和显式审批门来控制关键决策。

## 提供的 Skill

- `feature-dev-codex`：编排器，也是唯一允许修改生产代码、测试和计划文件的角色。
- `feature-dev-code-explorer`：只读代码库探索角色。
- `feature-dev-code-architect`：只读架构设计角色。
- `feature-dev-code-reviewer`：只读实现后审查角色。

## 工作流

面对中等复杂度或高风险任务，编排器会直接创建子 agent，完成三轮彼此独立的 pass：

1. **探索**：梳理相似功能、数据流、测试方式、项目规范和风险边界。
2. **架构**：比较实现方案，给出建议、文件变更图、测试方案及风险分析。
3. **审查**：在实现与初步验证后，独立检查正确性、回归、规范、测试与风险。

子 agent 均为只读，只返回结构化结论；只有编排器可以写入代码和计划文件，因此不会发生并发修改冲突。

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

## 安装与使用

从 Codex marketplace 安装此 plugin，或将其作为 personal plugin 加入 Codex。安装或升级后，请新开一个 Codex 线程，以便加载新的 skill。

使用 `feature-dev-codex` 启动中大型功能开发流程；简单、局部且低风险的改动无需强制走完整的三轮 pass。

## 归属说明

本 plugin 是为 Codex 独立实现的工作流，受到 Anthropic Claude Code 的 `feature-dev` plugin 工作流结构启发。本仓库不包含 Anthropic 项目的源文件、命令、agent 定义或文档文本。详见 [ATTRIBUTION.md](ATTRIBUTION.md)。
