# 模型网关与容错

## 原则

不要试图让 LLM 输出永远完美。要让系统不会因为一次坏输出停住。

这个原则来自当前 Python demo：确定性 fallback 计划、本地 fallback 文件、非阻塞测试失败、缺失文件补齐，让无人值守执行成为可能。

## 模型网关职责

| 职责 | 说明 |
| --- | --- |
| Provider 抽象 | Claude、OpenAI、Gemini、Ollama 和 OpenAI-compatible endpoint |
| 路由 | 按角色、成本、质量、延迟和可用性选择模型 |
| 降级 | 在 provider、model、cache、rule fallback 之间切换，不让流程停住 |
| 预算控制 | 在工作流执行前和执行中追踪 token 和成本预算 |
| 结构化输出处理 | 解析、修复、提取，或替换无效输出 |
| 事件上报 | 发出模型调用、重试、fallback、cache、警告、成本事件 |

## 五层降级链

```text
Layer 1: primary model
Layer 2: configured fallback model
Layer 3: local model
Layer 4: cache hit
Layer 5: rule-based deterministic fallback
```

Layer 5 必须先存在，模型集成才算具备生产可用性的基础。

## 正式 fallback 契约

| Demo 行为 | Forge 正式契约 |
| --- | --- |
| `_fallback_project()` | Architect 规划失败时，创建确定性的最小任务图 |
| `_fallback_code_files()` | 实现输出为空时，生成最小可用的目标文件 |
| `_task_files_exist()` | 测试失败但目标文件存在时，标记为 `completed_with_warnings` |
| `_ensure_requested_files()` | 模型漏掉必需文件时，在继续前合成缺失文件 |
| `SKIP_COMMANDS` | 长运行或环境依赖命令默认作为警告，除非任务显式要求必须通过 |

## 结果状态

| 状态 | 含义 |
| --- | --- |
| `completed` | 交付物存在，且验证通过 |
| `completed_with_warnings` | 交付物存在，但 fallback 或非阻塞验证警告仍在 |
| `blocked` | 需要用户输入 |
| `failed` | 没有产出可用交付物 |

`completed_with_warnings` 应在 TUI 和 final package 中明显展示。它是“有风险的成功”，不是隐藏失败。

## 结构化输出策略

- 在有用时要求模型返回结构化 JSON。
- 先直接解析。
- 再尝试修复或提取。
- 校验必填字段。
- 填充安全默认值。
- 仍然无效时，使用确定性 fallback。

系统必须记录走了哪条路径，让用户知道结果来自模型、修复后的输出、缓存，还是确定性兜底。

## 预算策略

预算是运行时策略，不应写死在业务逻辑里。

推荐预算控制：

- 单工作流最大成本。
- 单角色最大模型调用次数。
- retry budget。
- 本地模型 fallback 开关。
- 按任务类型配置 cache TTL。
- 接近预算耗尽时停止或降级。

## 重试策略

只重试可能是瞬时问题的失败：

- provider timeout。
- rate limit。
- network error。
- 某个通常有内容的 provider 返回空响应。

不要无限重试无效计划。经过有限修复后仍失败，就使用 fallback output。
