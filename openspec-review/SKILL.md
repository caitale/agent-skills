---
name: openspec-review
description: Use when reviewing, auditing, or giving feedback on in-progress OpenSpec change folders, proposal.md, design.md, tasks.md, or specs/*/spec.md before approval, during implementation, or before archive.
---
# OpenSpec Review

## 概览

把 OpenSpec change 当作可执行的规划产物来审查，而不是普通文档。重点判断 `proposal.md`、`specs/`、`design.md`、`tasks.md` 以及进行中的实现是否已经对齐到足以进入下一步。

## 开始

先确定要审查的 change：

- 优先使用用户给出的 change id 或路径。
- 否则检查 `openspec/changes/`，排除 `archive/`，只有在候选项唯一且明确时才推断 active change。
- 如果安装了 `openspec`，在有帮助时使用 `openspec list`、`openspec show <change>`、`openspec validate <change>`。
- 审查前先读取项目指令：`openspec/project.md`、`openspec/AGENTS.md`、仓库级 `AGENTS.md`，以及受影响目录里的本地指令。

按依赖顺序阅读产物：

1. 读 `proposal.md`，确认意图、范围、方案、影响，以及明确的 from/to 行为变化。
2. 读 `specs/*/spec.md`，确认行为变化。
3. 读现有 `openspec/specs/<capability>/spec.md`，确认当前行为和潜在冲突。
4. 当 change 涉及架构、数据、迁移、安全、性能或跨边界风险时，读 `design.md`，确认技术决策。
5. 读 `tasks.md`，确认实现顺序、验证方式和完成状态。
6. 只有在实现中审查或 archive 前审查时，再检查代码 diff 和测试。

## 审查项

检查 proposal 质量：

- 问题、目标、范围内工作、范围外工作、受影响用户或系统是否清晰。
- 每个重要行为变化是否有 from/to 描述、原因和影响。
- 是否说明 breaking changes、迁移、发布要求、兼容性和运维风险。
- 范围是否足够收敛，能在没有隐藏工作的情况下实现和审查。

检查 spec 质量：

- `spec.md` 是否描述外部可观察行为、接口、约束和错误处理。
- 实现细节是否放在 `design.md` 或 `tasks.md`，而不是行为规格 `spec.md`。
- 每个 requirement 是否使用 `### Requirement:`，并至少包含一个 `#### Scenario:`。
- scenario 是否具体、可验证，最好使用 GIVEN/WHEN/THEN 风格条件。
- ADDED、MODIFIED、REMOVED、RENAMED requirements 是否与现有 source spec 一致。
- 被修改或移除的 requirement 是否保留了足够上下文，让人理解改了什么以及为什么改。
- 是否引入了跨 capability 的重复、矛盾或孤立 requirement。

检查 design 质量：

- 设计决策是否解释了选择的方案；只有在风险值得时才要求记录关键备选方案。
- 是否覆盖横切影响：API、存储、认证、权限、并发、失败模式、可观测性、迁移、回滚、隐私和性能。
- design 是否能映射回 proposal 和每个非平凡 requirement。
- 假设和未决决策是否显式写出，而不是藏在实现任务里。

检查 task 可执行性：

- task 是否有顺序、粒度足够小，并覆盖测试和验证。
- 在相关场景下，task 是否包含迁移、文档、清理、发布和 archive 准备。
- 已勾选的 checkbox 是否与实际代码、测试和 spec 状态一致。
- task 之间的依赖是否足够清楚，能避免并行实现冲突。

当代码已经存在时，检查实现对齐：

- 对照 OpenSpec requirements 检查代码和测试，而不仅是对照 task checkbox。
- 标出这些问题：实现了但未写入 spec、写入 spec 但未实现、测试了但没有对应 requirement。
- archive 前确认最终 source specs 反映已部署行为，并且 active change 产物没有过期内容。

## 输出格式

先输出 findings，并按严重程度排序：

- `Blocker`: 修复前不应 approval 或 archive。
- `Major`: 很可能造成实现歧义、行为缺口或审查风险。
- `Minor`: 清晰度、格式或完整性问题，但不阻塞推进。

每条 finding 包含：

- 可用时给出文件和行号。
- 具体问题。
- 为什么这会影响 OpenSpec workflow 或实现。
- 明确的修复建议或需要做出的决策。

然后给出 open questions 和 readiness 判断：

- `Ready for approval`
- `Needs revision before approval`
- `Ready to continue implementation`
- `Not ready to archive`

总结保持简短。除非用户要求编辑，否则不要重写整份 spec。

## 边界

除非用户明确要求，否则审查期间不要实现 change。优先指出能消除歧义的最小 spec、design 或 task 修改。

当用户使用中文提问时，用中文输出审查结果，同时保留文件名、标题和 requirement 关键字的原文。
