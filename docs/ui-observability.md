# UI 与可观测性

## UI 目标

Forge TUI 应该让多 Agent 工作变得可检查，而不是魔法黑盒。用户应该看到正在发生什么、哪里阻塞、哪里失败、生成了什么、哪些东西花钱。

TUI 使用 TypeScript Ink 构建。

## 主要视图

| 视图 | 作用 |
| --- | --- |
| Dashboard | 活动工作流、Agent 泳道、当前阶段、警告 |
| Task detail | PRD、计划、任务图、产物、检查 |
| Agent detail | prompt 摘要、模型、状态、日志、输出 |
| Cost view | 按角色汇总 token 和成本 |
| Memory view | 决策、模式、警告、journal 记录 |
| Settings view | providers、预算、fallback 策略、harness adapters |

## Agent 状态

```text
idle
planning
waiting_for_context
calling_model
writing_artifacts
checking
self_fixing
completed
completed_with_warnings
blocked
failed
```

每次状态变化都应该发事件给 TUI。

## 事件流

Go daemon 暴露 WebSocket event stream。TypeScript TUI 订阅并渲染：

- workflow 阶段变化。
- Agent 状态变化。
- 模型调用开始/结束。
- retry / fallback / cache 事件。
- 文件和产物写入。
- 检查结果。
- 警告和 blocked 状态。
- 成本和 token 更新。

## Thinking UI

Forge 应展示过程，而不是展示私有 chain-of-thought。

可见的 thinking 指：

- Agent 意图。
- 当前动作。
- 正在处理的文件或产物。
- 决策摘要。
- 警告或不确定性。
- 下一步计划。

不要展示模型私有推理。展示的是操作轨迹和摘要。

## 警告设计

警告必须是一等 UI 对象：

- 使用了 fallback。
- 必需文件被合成。
- 测试作为非阻塞项跳过。
- 预算接近上限。
- provider 降级。
- 建议人工审查。

最终结果带警告时，视觉上应该明显区别于完全成功和失败。

## 用户操作

TUI 应支持：

- 启动任务。
- 暂停工作流。
- 恢复工作流。
- 批准 blocked 决策。
- 打开产物路径。
- 重试失败角色。
- 接受 `completed_with_warnings`。
- 导出 final package。

## MVP 验收

第一版 TUI demo 合格标准：用户离开后再回来，仍然能看懂：

- 哪些 Agent 跑过。
- 每个 Agent 用了哪个模型。
- 每个 Agent 产出了什么。
- 哪里用了 fallback。
- 还剩哪些警告。
- 最终产物在哪里。

