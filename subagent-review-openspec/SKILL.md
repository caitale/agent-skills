---
name: subagent-review-openspec
description: Use when you need an unbiased, context-isolated review of OpenSpec change documents by dispatching an independent architect subagent, especially when current session has already formed opinions or when reviewing critical architectural decisions.
---

# Subagent Review OpenSpec

## 概览

通过独立 subagent（扫地僧）审查 OpenSpec change，避免当前会话上下文污染审查结果。

**核心原则：** 主 agent 不读取 change 文档内容，只定位路径并派发审查。扫地僧独立阅读、独立判断，返回结构化 findings。

## 何时使用

- 当前会话已读过代码或方案，担心影响审查质量。
- 用户需要独立的架构审查意见、second opinion、subagent review 或扫地僧审查。
- Change 涉及重大架构、安全、数据迁移或跨子系统决策。

## 流程

### Step 1: 确定 change 目录

- 优先使用用户给出的 change id 或路径。
- 否则检查 `openspec/changes/`，排除 `archive/`。
- 只有候选项唯一且明确时才推断 active change；多个候选项时停止并请用户指定。

**完成条件：** 得到一个存在的 `openspec/changes/<change>/` 目录路径；能用绝对路径时优先记录绝对路径。不要读取 `proposal.md`、`design.md`、`tasks.md`、`specs/*/spec.md` 或现有 source spec 的内容。

### Step 2: 准备派发材料

收集要传给扫地僧的客观路径：

- Change 目录路径。
- `openspec/project.md`（如存在）。
- `openspec/AGENTS.md`（如存在）。
- 仓库根 `AGENTS.md`（如存在）。

**完成条件：** 已准备 `<CHANGE_DIR_PATH>` 和 `<PROJECT_INSTRUCTION_PATHS>` 两个占位符的替换值；不存在的路径不要传入。主 agent 仍然不读取 change 文档内容。

### Step 3: Dispatch 扫地僧

1. 读取 [`references/prompt-template.md`](references/prompt-template.md)。
2. 读取 [`references/openspec-review-criteria.md`](references/openspec-review-criteria.md)。
3. 替换 prompt 模板中的 `<CHANGE_DIR_PATH>`、`<PROJECT_INSTRUCTION_PATHS>` 和 `<OPENSPEC_REVIEW_CRITERIA>`。
4. 按当前平台派发 subagent；具体调用语法见 [`references/dispatch.md`](references/dispatch.md)。

**占位符映射：**

| 占位符 | 替换为 | 格式 |
|--------|--------|------|
| `<CHANGE_DIR_PATH>` | Step 1 得到的 change 目录路径 | 优先绝对路径，例如 `/repo/openspec/changes/add-foo` |
| `<PROJECT_INSTRUCTION_PATHS>` | Step 2 收集到的已存在项目指令路径 | 多行 bullet；没有则写 `无` |
| `<OPENSPEC_REVIEW_CRITERIA>` | `references/openspec-review-criteria.md` 的完整内容 | 原样嵌入 markdown |

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
| 自己先读 change 文档再 dispatch | 只确定路径，不读内容 |
| 多个候选 change 时自行猜测 | 停止并请用户指定 |
| 在 prompt 中加入自己的判断 | 只传递路径、审查标准和客观事实 |
| 修改扫地僧的 findings | 原样呈现，可补充不可篡改 |
| 省略审查标准 | 嵌入 `references/openspec-review-criteria.md` 的完整内容 |
