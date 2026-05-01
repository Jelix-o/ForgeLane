# 资料洞察

这份文档记录 Forge 从每类资料中借鉴了什么，避免后续工作忘记这些设计来自哪里。

## 资料优先级

1. 用户意图。
2. Claude 项目记忆。
3. 当前 Python demo。
4. Trellis 源码。
5. Trellis 文档。
6. 前端前辈文章。
7. 原始 `docs/` 草稿。

## Claude 记忆

Claude 记忆中的关键决策：

- Forge 是一个多 AI 软件开发工作流系统。
- 核心技术栈：Go engine + TypeScript CLI / TUI / plugins。
- 云端模型优先，本地模型作为 fallback。
- 第一版 UI 是终端 TUI；Web 和桌面应用后续再做。
- 同步模式：LAN、network、Git。
- 工作模式：自动多 AI 协作 + 手动 AI 编辑器管理。
- 设计应服务前端开发者，让前端在 AI 时代保留和扩大杠杆。

后续记忆里最重要的修正是：容错必须优先于追求模型输出完美。

## Python Demo

当前 demo 更重要的是验证产品方向，而不是证明最终架构。

可复用经验：

- Architect 规划会失败；确定性任务图能让流程继续。
- 空模型输出应该生成 fallback files，而不是停止。
- 模型漏掉的目标文件应该补齐。
- 环境依赖型检查在产物存在时可以降级成警告。
- 长时间无人值守执行需要 `completed_with_warnings` 作为有效状态。

## Trellis

借鉴：

- 文件系统即状态。
- workflow 是可编辑 Markdown。
- task 目录包含 PRD、info、JSONL context、metadata。
- session-scoped active task。
- 渐进式上下文加载。
- workspace journal。
- hooks、skills、subagents、platform adapters。
- check / self-fix loop。

扩展：

- 直接模型编排。
- 模型网关。
- TUI 事件面板。
- fallback-first runtime。
- SQLite memory index。

不照搬：

- Trellis 只作为 AI coding tool wrapper 的产品形态。
- 平台特定文件作为主要源事实。
- 不符合 Forge 混合运行时目标的实现细节。

## 前端前辈文章

高价值经验：

| 资料方向 | Forge 用法 |
| --- | --- |
| Harness Engineering | 用 Rules、Skills、Hooks、Subagents 作为控制框架 |
| AI 开发规范 | AGENTS 式入职文件、记忆、ADR、边界、benchmark、same-day audit |
| 多 Agent 架构 | pipeline、fan-out/fan-in、router、supervisor、Thinking UI |
| AI Gateway | 模型路由、fallback、限流、预算控制 |
| AI Memory | 把聊天和会话历史提取成可复用项目知识 |
| Token / Context 管理 | 渐进式上下文和预算裁剪 |
| 结构化输出 | parse、repair、validate，然后 fallback |

## 原始 docs 修正点

原始 `docs/` 是有价值的宽泛草稿，但 docs v2 修正了这些问题：

- TUI 统一为 TypeScript Ink；不采用之前的另一个终端技术口径。
- demo 的容错经验是核心架构，而不是边角说明。
- SQLite memory 是文件源事实之上的索引，不是唯一记忆。
- MVP 角色是和 demo 对齐的 5 个角色；product / supervisor / research 是后续角色。
- 平台 Harness 是 Forge 的一种执行路径，不是整个产品。

