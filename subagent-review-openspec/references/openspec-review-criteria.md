# OpenSpec Review Criteria

## Proposal 质量

- 问题、目标、范围内工作、范围外工作、受影响用户或系统是否清晰。
- 每个重要行为变化是否有 from/to 描述、原因和影响。
- 是否说明 breaking changes、迁移、发布要求、兼容性和运维风险。
- 范围是否足够收敛，能在没有隐藏工作的情况下实现和审查。

## Spec 质量

- `spec.md` 是否描述外部可观察行为、接口、约束和错误处理。
- 实现细节是否放在 `design.md` 或 `tasks.md`，而不是 `spec.md`。
- 每个 requirement 是否使用 `### Requirement:`，并至少包含一个 `#### Scenario:`。
- scenario 是否具体、可验证，最好使用 GIVEN/WHEN/THEN 风格。
- ADDED、MODIFIED、REMOVED、RENAMED requirements 是否与现有 source spec 一致。
- 被修改或移除的 requirement 是否保留足够上下文，让人理解改了什么以及为什么。
- 是否引入跨 capability 的重复、矛盾或孤立 requirement。

## Design 质量

- 设计决策是否解释了选择的方案；只在风险值得时才要求记录关键备选方案。
- 是否覆盖横切影响：API、存储、认证、权限、并发、失败模式、可观测性、迁移、回滚、隐私和性能。
- design 是否能映射回 proposal 和每个非平凡 requirement。
- 假设和未决决策是否显式写出，而不是藏在实现任务里。

## Task 可执行性

- task 是否有顺序、粒度足够小，并覆盖测试和验证。
- task 是否包含迁移、文档、清理、发布和 archive 准备（如适用）。
- 已勾选的 checkbox 是否与实际代码、测试和 spec 状态一致。
- task 之间的依赖是否足够清楚，能避免并行实现冲突。

## 输出格式

先输出 findings，按严重程度排序：

- `Blocker`: 修复前不应 approval 或 archive。
- `Major`: 很可能造成实现歧义、行为缺口或审查风险。
- `Minor`: 清晰度、格式或完整性问题，不阻塞推进。

每条 finding 包含：

- 文件和行号（可用时）。
- 具体问题。
- 为什么影响 OpenSpec workflow 或实现。
- 明确的修复建议或需要做出的决策。

然后给出 open questions 和 readiness 判断：

- `Ready for approval`
- `Needs revision before approval`
- `Ready to continue implementation`
- `Not ready to archive`
