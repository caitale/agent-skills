---
name: superpowers-review
description: Use when reviewing, auditing, or giving feedback on Superpowers-generated design specs or implementation plans, especially docs/superpowers/specs/*.md and docs/superpowers/plans/*.md before approval, execution, handoff, or commit.
---
# Superpowers Review

## 概览

把 Superpowers 生成的 spec 和 plan 当作即将驱动实现的工程契约来审查，而不是普通 Markdown。重点判断设计是否足够明确、计划是否可执行，以及二者是否能让后续 agent 在低上下文下可靠完成任务。

## 开始

先确定要审查的文档范围：

- 优先使用用户给出的 spec 或 plan 路径。
- 否则检查 `docs/superpowers/specs/` 和 `docs/superpowers/plans/`，只有在候选项唯一且明确时才推断目标。
- 如果只给了 plan，尝试从标题、Goal、文件名或正文引用定位对应 spec；无法确认时说明缺口。
- 审查前读取项目指令：仓库级 `AGENTS.md`、相关目录里的本地指令，以及文档中明确引用的上下文文件。

按依赖顺序阅读产物：

1. 读 spec，确认目标、范围、用户场景、设计边界、组件、数据流、错误处理和测试策略。
2. 读 plan，确认 header、目标、架构、技术栈、文件结构、任务分解、TDD 步骤、验证命令和提交边界。
3. 对照 spec 和 plan，确认每个需求都有实现任务，每个任务都来自 spec 或必要的工程支撑。
4. 只有在 plan 已部分执行、用户要求执行前复核，或文档引用现有实现时，再检查代码 diff 和测试。

## 审查项

检查 spec 质量：

- 问题、目标、范围内工作、范围外工作、目标用户或系统是否清晰。
- 行为变化是否具体到可观察结果，而不只是实现愿望。
- 架构、组件职责、数据流、错误处理、性能、安全、迁移和回滚风险是否覆盖到与变更规模相称的程度。
- 是否把多个独立子系统硬塞进一个 spec；如果是，建议拆成可独立实现和验证的子项目。
- 是否存在 TBD、TODO、占位符、未决决策、互相矛盾的描述或可被两种方式解释的需求。

检查 plan 质量：

- 是否包含 Superpowers plan 要求的标题、agentic worker 提示、Goal、Architecture、Tech Stack 和分隔线。
- 文件结构是否列出精确路径，并说明每个文件的职责；新增、修改、测试文件边界是否明确。
- 每个任务是否足够小，能产生可独立验证的增量，并且顺序符合依赖关系。
- TDD 步骤是否完整：先写失败测试、运行确认失败、最小实现、运行确认通过、再提交。
- 代码步骤是否给出实际代码或足够精确的补丁内容；命令步骤是否给出准确命令和期望结果。
- 是否避免了 "TBD"、"TODO"、"add appropriate handling"、"similar to previous task"、"write tests" 等不可执行占位语。
- 类型、函数名、文件名、命令、提交信息和后续任务引用是否前后一致。

检查 spec/plan 对齐：

- spec 的每个需求、约束和风险是否能映射到 plan 中的任务、验证或明确不做的范围。
- plan 是否引入了 spec 没有批准的新行为、架构迁移、依赖或范围扩张。
- 测试计划是否覆盖关键用户行为、错误路径、回归风险和集成边界。
- 执行方式是否与 Superpowers workflow 匹配：plan 应能交给 `superpowers:subagent-driven-development` 或 `superpowers:executing-plans` 逐任务执行。
- 已勾选的 checkbox 是否与实际代码、测试和提交状态一致。

## 输出格式

先输出 findings，并按严重程度排序：

- `Blocker`: 修复前不应 approval、handoff 或 execution。
- `Major`: 很可能造成实现歧义、遗漏需求、错误执行或返工。
- `Minor`: 清晰度、格式或完整性问题，但不阻塞推进。

每条 finding 包含：

- 可用时给出文件和行号。
- 具体问题。
- 为什么这会影响 Superpowers workflow 或后续实现。
- 明确的修复建议或需要做出的决策。

然后给出 open questions 和 readiness 判断：

- `Ready for approval`
- `Needs spec revision`
- `Needs plan revision`
- `Ready for execution`
- `Not ready for execution`

总结保持简短。除非用户明确要求编辑，否则不要重写整份 spec 或 plan。

## 边界

审查期间不要开始执行 plan，也不要修实现代码。优先指出能消除歧义的最小文档修改。

当用户使用中文提问时，用中文输出审查结果，同时保留文件名、标题、Goal、Architecture、Tech Stack、Task、Step 等关键字的原文。
