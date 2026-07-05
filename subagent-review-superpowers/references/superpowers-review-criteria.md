# Superpowers Review Criteria

## 来源文档质量

**Superpowers Spec 来源时：**

- 问题、目标、范围内工作、范围外工作、目标用户或系统是否清晰。
- 行为变化是否具体到可观察结果，而不只是实现愿望。
- 架构、组件职责、数据流、错误处理、性能、安全、迁移和回滚风险是否覆盖到与变更规模相称的程度。
- 是否把多个独立子系统硬塞进一个 spec；如果是，建议拆成可独立实现和验证的子项目。
- 是否存在 TBD、TODO、占位符、未决决策、互相矛盾的描述或可被两种方式解释的需求。

**OpenSpec 来源时：**

- Proposal：问题、目标、范围内/外工作、from/to 行为变化、breaking changes、迁移和运维风险是否清晰收敛。
- Spec：是否描述外部可观察行为、接口、约束和错误处理；每个 requirement 是否有 scenario；ADDED/MODIFIED/REMOVED 是否与现有源 spec 一致。
- Design：技术决策是否解释了方案选择；是否覆盖横切影响（API、存储、认证、并发、失败模式、可观测性、迁移、回滚、性能）。
- Tasks：是否有顺序、粒度足够小、覆盖测试和验证；task 之间的依赖是否清楚。

## Plan 质量

- 是否包含 Superpowers plan 要求的标题、agentic worker 提示、Goal、Architecture、Tech Stack 和分隔线。
- 文件结构是否列出精确路径，并说明每个文件的职责；新增、修改、测试文件边界是否明确。
- 每个任务是否足够小，能产生可独立验证的增量，并且顺序符合依赖关系。
- TDD 步骤是否完整：先写失败测试、运行确认失败、最小实现、运行确认通过、再提交。
- 代码步骤是否给出实际代码或足够精确的补丁内容；命令步骤是否给出准确命令和期望结果。
- 是否避免了 "TBD"、"TODO"、"add appropriate handling"、"similar to previous task"、"write tests" 等不可执行占位语。
- 类型、函数名、文件名、命令、提交信息和后续任务引用是否前后一致。

## 来源/Plan 对齐

- 来源文档的每个需求、约束和风险是否能映射到 plan 中的任务、验证或明确不做的范围。
- Plan 是否引入了来源文档没有批准的新行为、架构迁移、依赖或范围扩张。
- OpenSpec 来源时，plan 的任务是否覆盖 OpenSpec tasks.md 的全部内容，未遗漏任何 task，也未凭空增加 task。
- 测试计划是否覆盖关键用户行为、错误路径、回归风险和集成边界。
- 执行方式是否与 Superpowers workflow 匹配：plan 应能交给 subagent-driven-development 或 executing-plans 逐任务执行。
- 已勾选的 checkbox 是否与实际代码、测试和提交状态一致。

## 输出格式

先输出 findings，按严重程度排序：

- Blocker: 修复前不应 approval、handoff 或 execution。
- Major: 很可能造成实现歧义、遗漏需求、错误执行或返工。
- Minor: 清晰度、格式或完整性问题，不阻塞推进。

每条 finding 包含：

- 文件和行号（可用时）。
- 具体问题。
- 为什么影响 Superpowers workflow 或后续实现。
- 明确的修复建议或需要做出的决策。

然后给出 open questions 和 readiness 判断：

- Ready for approval
- Needs spec revision
- Needs plan revision
- Ready for execution
- Not ready for execution
