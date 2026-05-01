# Harness 集成

## 目标

Forge 应该学习 Trellis，而不是复制 Trellis。Trellis 证明了 workflow、task state、spec、JSONL context、hooks、skills、subagents、workspace journals 可以让 AI coding 工具更可靠。

Forge 使用这些经验，把外部 AI 编辑器变成更大工作流系统中的一种执行方式。

## 借鉴概念

| Trellis 概念 | Forge 适配方式 |
| --- | --- |
| `workflow.md` | `.forge/workflow.md`，作为人类可读的工作流状态机 |
| `.trellis/tasks/` | `.forge/tasks/`，作为任务源事实 |
| `implement.jsonl` / `check.jsonl` | 角色相关上下文清单 |
| session-scoped active task | `.forge/.runtime/sessions/<context-key>.json` |
| SessionStart hook | 向 AI 编辑器注入项目、任务和 workflow 状态 |
| per-turn workflow-state breadcrumb | 保持 AI 与当前阶段对齐 |
| PreToolUse / subagent context injection | 在 subagent 工作前加载 PRD、spec 和 JSONL |
| workspace journal | 给人和 Agent 使用的跨会话记忆 |

## 平台目标

| 平台 | MVP 态度 |
| --- | --- |
| Claude Code | 第一参考 harness |
| Cursor | 重要目标，因为核心用户是前端开发者 |
| Codex | 重要目标，适合共享 agent / skill 工作流 |
| Gemini / OpenCode / 其他 | 等第一个 harness 契约稳定后再接入 |

## 平台适配器契约

每个平台 adapter 应提供：

- 平台检测。
- 配置文件生成。
- hook 或等价入口。
- command / skill 定义。
- 支持时生成 agent 定义。
- 上下文注入格式。
- 当平台缺少 hook 时，可以降级运行。

## 钩子事件

| 事件 | 作用 |
| --- | --- |
| session start | 注入项目概览、活动任务、最近 journal 和 workflow |
| prompt submit | 注入轻量阶段 breadcrumb |
| before agent/tool | 从 JSONL 注入角色相关上下文 |
| after agent/check | 记录状态、警告和产物 |

## 技能和命令模型

Skills 是可复用能力，例如 planning、implementation、checking、updating specs、finishing work。

Commands 是显式用户动作，例如：

- 创建任务。
- 继续任务。
- 完成工作。
- 显示当前任务。
- 导出上下文。

在 Forge 中，skills 和 commands 应从 `.forge/workflow.md` 与 adapter 模板生成，避免平台文件和源事实漂移。

## 集成顺序

1. 定义平台无关的 harness 契约。
2. 实现 Claude Code adapter 作为参考。
3. 实现 Cursor adapter。
4. 实现 Codex adapter。
5. 抽取通用 adapter 模板。

## 非目标

- 独立编排模式不依赖外部 AI 编辑器。
- 不盲目复制 Trellis 源码行为。
- 不让平台特定文件变成主要 workflow 源事实。
