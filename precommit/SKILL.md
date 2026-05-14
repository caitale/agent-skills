---
name: precommit
description: "Use when preparing Git commits: inspecting staged or unstaged changes, staging requested files, writing Conventional Commits messages, or running git commit. Trigger when the user asks to commit, stage changes, generate or improve a commit message, split changes into commits, or check what should be committed."
---

# 提交准备 (Precommit)

## 概述

此技能用于将当前 Git 变更转化为整洁、可审查的提交。优先输出简洁的中文 Conventional Commits 信息，并匹配仓库已有的提交风格。

提交信息格式：

```
<type>(<scope>): <中文描述>
```

- **type**：必填，选择最贴近变更语义的类型
- **scope**：可选，当变更集中于单一模块、包、目录时建议填写
- **描述**：中文祈使句，无结尾标点，尽量不超过 72 字符

## Type 选择

| 类型 | 何时使用 | 示例 |
|------|----------|------|
| `feat` | 新功能 | `feat(auth): 新增 OAuth2 第三方登录` |
| `fix` | Bug 修复 | `fix(api): 修复空数组越界导致 500` |
| `refactor` | 重构，不改功能 | `refactor(db): 抽离连接池为独立模块` |
| `docs` | 文档变更 | `docs(api): 补充接口错误码说明` |
| `chore` | 构建、依赖、工具、杂项 | `chore: 升级 ESLint 至 v9` |
| `style` | 代码格式（不影响逻辑） | `style: 统一缩进为 2 空格` |
| `test` | 添加或修改测试 | `test(utils): 补充日期格式化单元测试` |
| `perf` | 性能优化 | `perf(image): 用 WebP 替代 PNG 减少带宽` |
| `build` | 构建系统或依赖管理 | `build: 升级 webpack 至 v5` |
| `ci` | CI/CD 配置 | `ci: 新增 PR 标题规范检查` |
| `revert` | 回滚提交 | `revert: 回滚缓存预热逻辑` |

## Scope 选择

从暂存区 diff 涉及的主要包、应用、目录、功能或服务推断 scope：

- 项目未约定风格时使用**小写 kebab-case**
- 变更涉及整个仓库、仅根级文件或横跨多个不相关模块时**省略 scope**
- 仅 lockfile 或纯格式化变更通常不需要 scope，除非项目约定始终包含

## 描述规则

1. **中文祈使句**：用"新增"、"修复"、"优化"、"移除"，不用"新增了"、"修复了"
2. **说做了什么，不说为什么** — why 留给 PR description
3. **多个变更合并描述**：`feat(shop): 为商品详情和购物车新增优惠券入口`
4. **保持具体**：避免"更新代码"、"修改问题"、"处理逻辑"等模糊表述
5. **不超过 72 字符**；额外上下文放入 commit body 而非拉长标题

## 完整流程

1. **查看状态**：运行 `git status --short` 了解暂存、未暂存和未跟踪文件
2. **暂存文件**：
   - 用户明确指定文件时，仅暂存指定文件
   - 请求范围较宽时，先检查 diff，避免误暂存无关的本地工作
   - 暂存区为空时，检查 `git diff` 并告知用户当前无暂存内容（除非用户明确要求暂存）
3. **分析 diff**：运行 `git diff --staged`，分析变更内容
4. **生成信息**：根据 diff 推断 `type`、可选 `scope` 和简洁的中文描述
5. **仅需信息**：用户只要求生成提交信息时，直接输出信息本身（除非要求解释）
6. **执行提交**：确认暂存 diff 与预期一致后运行 `git commit -m "<message>"`。用户意图明确时无需额外确认
7. **处理钩子**：若提交因钩子修改文件而失败，检查状态并解释变更后再重试

## 拆分提交

暂存变更包含不相关目的时，建议拆分后再提交。拆分信号：

- 需要不同 type，如 `feat` + `fix` 混在一起
- 生成文件或 lockfile 与无关任务的源码变更混在一起
- 多个应用、包或服务变更缺乏共享原因

用户要求拆分时，使用 `git add <path>` 或交互式暂存仅添加相关文件/hunk。避免使用破坏性命令。

## 输出风格

- **仅生成信息**：返回一行提交信息即可
- **执行提交**：简短报告 commit hash 或提交完成状态，并附上使用的信息
- **暂存区不明确**：说明哪些已暂存、哪些未暂存，然后提一个最必要的问题
- 提交信息**不加 markdown 代码围栏**，除非用户要求多个备选方案或示例

## 常见错误与纠正

| 错误 | 正确 |
|------|------|
| `fix: 修复了列表加载` | `fix(list): 修复列表滚动加载重复触发` |
| `feat: add login page` | `feat(auth): 新增登录页面` |
| `update: 修改了一些东西` | 根据实际变更选 `fix`/`feat`/`refactor` |
| `feat(auth): 新增登录页面。` | 不加句号 |
| `chore: 修改配置` | `ci: 修复构建缓存未命中` |

