---
name: subagent-review-superpowers
description: Use when you need an unbiased, context-isolated review of Superpowers spec or plan documents, including plans derived from OpenSpec changes, by dispatching an independent architect subagent, especially when the current session has already formed opinions or when reviewing critical architecture, security, migration, or cross-subsystem decisions.
---

# Subagent Review Superpowers

## 概览

通过独立 subagent（扫地僧）审查 Superpowers spec、plan，或从 OpenSpec change 生成的 Superpowers plan，避免当前会话上下文污染审查结果。

**核心原则：** 主 agent 不读取 spec、plan 或 OpenSpec change 文档内容，只定位客观路径并派发审查。扫地僧独立阅读、独立判断，返回结构化 findings。

## 何时使用

- 当前会话已读过代码或方案，担心影响审查质量。
- 用户需要独立的架构审查意见、second opinion、subagent review 或扫地僧审查。
- Spec、plan 或 OpenSpec 来源涉及重大架构、安全、数据迁移或跨子系统决策。

## 流程

### Step 1: 确定文档范围

- 优先使用用户给出的 Superpowers spec 路径、plan 路径、OpenSpec change id 或 OpenSpec change 路径。
- 否则检查 `docs/superpowers/specs/` 和 `docs/superpowers/plans/`；只有候选项唯一且明确时才推断目标。
- 如果用户给出 OpenSpec change id 或路径，确认它对应一个存在的 `openspec/changes/<change>/` 目录，排除 `archive/`。
- 如果只得到 plan 路径，不要读取 plan 正文来反查 spec 或 OpenSpec 来源；把未知路径写为 `无`，交给扫地僧读取 plan 后判断。

**完成条件：** 得到至少一个存在的 spec、plan 或 `openspec/changes/<change>/` 路径；能用绝对路径时优先记录绝对路径。多个候选项无法唯一判断时停止并请用户指定。不要读取 spec、plan、`proposal.md`、`design.md`、`tasks.md`、`specs/*/spec.md` 或现有 source spec 的内容。

### Step 2: 准备派发材料

收集要传给扫地僧的客观路径：

- Step 1 得到的 Superpowers spec 路径（如有）。
- Step 1 得到的 Superpowers plan 路径（如有）。
- Step 1 得到的 OpenSpec change 目录路径，或该目录下已存在的 `proposal.md`、`specs/*/spec.md`、`design.md`、`tasks.md` 路径（如有）。
- 仓库根 `AGENTS.md`（如存在）。
- 已知 spec、plan、OpenSpec change 路径所在目录及其父目录中的本地 `AGENTS.md`（如存在）。
- `openspec/project.md` 和 `openspec/AGENTS.md`（如存在，且用户给出或唯一确认了 OpenSpec 来源）。
- 用户明确给出的其他上下文文件路径（如有）。

**完成条件：** 已准备 `<SPEC_PATH>`、`<PLAN_PATH>`、`<OPENSPEC_PATHS>` 和 `<PROJECT_INSTRUCTION_PATHS>` 四个占位符的替换值；不存在或未知的路径写 `无`。主 agent 仍然不读取任何审查文档内容。

### Step 3: Dispatch 扫地僧

1. 读取 [`references/prompt-template.md`](references/prompt-template.md)。
2. 读取 [`references/superpowers-review-criteria.md`](references/superpowers-review-criteria.md)。
3. 替换 prompt 模板中的 `<SPEC_PATH>`、`<PLAN_PATH>`、`<OPENSPEC_PATHS>`、`<PROJECT_INSTRUCTION_PATHS>` 和 `<SUPERPOWERS_REVIEW_CRITERIA>`。
4. 按当前平台派发 subagent；具体调用语法见 [`references/dispatch.md`](references/dispatch.md)。

**占位符映射：**

| 占位符 | 替换为 | 格式 |
|--------|--------|------|
| `<SPEC_PATH>` | Step 1 得到的 Superpowers spec 路径 | 优先绝对路径；没有或未知则写 `无` |
| `<PLAN_PATH>` | Step 1 得到的 Superpowers plan 路径 | 优先绝对路径；没有或未知则写 `无` |
| `<OPENSPEC_PATHS>` | Step 1/2 得到的 OpenSpec change 目录或产物路径 | 多行 bullet；没有或未知则写 `无` |
| `<PROJECT_INSTRUCTION_PATHS>` | Step 2 收集到的已存在项目指令和用户明确给出的上下文路径 | 多行 bullet；没有则写 `无` |
| `<SUPERPOWERS_REVIEW_CRITERIA>` | `references/superpowers-review-criteria.md` 的完整内容 | 原样嵌入 markdown |

**派发约束：**

- 不 fork 当前上下文；subagent 只接收替换后的 prompt。
- Prompt 只包含路径、审查标准和客观指令，不加入主 agent 的判断。
- 默认继承当前模型；只有用户明确要求或审查风险很高时才指定模型。架构审查可使用 high effort。

**完成条件：** subagent 已启动，并且返回可用于等待结果的 agent id。

### Step 4: 呈现结果

- 等待扫地僧返回结果，并在获取结果后关闭 subagent slot。
- 将扫地僧的 findings 原样呈现给用户。
- 如需补充当前会话观察到的上下文，单独标注为“主 agent 补充”，不要改写、淡化或合并扫地僧的核心 findings。

**完成条件：** 用户看到完整 findings、open questions 和 readiness 判断；主 agent 的补充意见与扫地僧输出清晰分离。

## 常见错误

| 错误 | 正确做法 |
|------|----------|
| 自己先读 spec、plan 或 OpenSpec change 文档再 dispatch | 只确定路径，不读内容 |
| 只给 plan 时读取正文猜测 spec 或 OpenSpec 来源 | 传入 plan 路径，把未知路径写 `无`，让扫地僧判断 |
| 多个候选文档时自行猜测 | 停止并请用户指定 |
| 在 prompt 中加入自己的判断 | 只传递路径、审查标准和客观事实 |
| 修改扫地僧的 findings | 原样呈现，可补充不可篡改 |
| 省略审查标准 | 嵌入 `references/superpowers-review-criteria.md` 的完整内容 |
