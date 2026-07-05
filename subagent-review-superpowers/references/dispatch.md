# Dispatch Reference

## Claude Code

```js
Agent({
  description: "扫地僧审查: <doc-name>",
  prompt: "<替换后的完整 prompt>",
  subagent_type: "general-purpose"
})
```

指定模型时，用 `model` 参数替代 `subagent_type`，不要同时设置二者：

```js
Agent({
  description: "扫地僧审查: <doc-name>",
  prompt: "<替换后的完整 prompt>",
  model: "opus"
})
```

## Codex

使用当前环境暴露的 multi-agent 工具：

```js
multi_agent_v1.spawn_agent({
  message: "<替换后的完整 prompt>",
  agent_type: "default",
  fork_context: false,
  reasoning_effort: "high"
})
```

`spawn_agent` 会返回 agent id。等待和关闭时传入这个 id：

```js
multi_agent_v1.wait_agent({
  targets: ["<agent-id>"],
  timeout_ms: 600000
})

multi_agent_v1.close_agent({
  target: "<agent-id>"
})
```

参数说明：

- `message`: subagent 收到的完整 prompt，不是 `prompt`。
- `agent_type`: 通常用 `default`；只做代码库定位问题时才考虑 `explorer`。
- `fork_context`: 设为 `false`，保证上下文隔离。
- `model`: 默认省略并继承父 agent；只有用户明确要求或有清晰任务理由时指定。
- `reasoning_effort`: 架构审查可用 `high`；未设置时继承父 agent。
