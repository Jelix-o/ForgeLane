# Forge v2 - AI 工作流系统

Forge 是一个混合型 AI 软件开发工作流系统。它要把用户的一条产品需求，转成一个可观察、可追踪、可恢复的多角色工作流：规划、调研、并行实现、集成、检查、自修复，最后交付可审阅的产物。

这个产品优先服务前端开发者：你不应该变成多个 AI 聊天窗口的人工调度员，而应该像指挥一个小型 AI 开发团队一样工作。

## 产品定位

| 模式 | 做什么 | MVP 优先级 |
| --- | --- | --- |
| 独立编排模式 | Forge 直接调用模型 API，协调 architect、frontend、backend、tester、devops 等角色执行任务。 | 第一阶段重点验证 |
| 平台 Harness 模式 | Forge 借鉴 Trellis，用 workflow、task、spec、context、hook、skill、command 管理 Claude Code、Cursor、Codex 等工具。 | 架构上提前设计，编排能力验证后接入 |

长期目标是把两种模式合成一个完整工作流。用户不需要关心某个任务应该走“模型 API Agent”还是“编辑器 Agent”，Forge 应该自动路由。

## 已锁定决策

| 领域 | 决策 |
| --- | --- |
| 核心引擎 | Go daemon，负责工作流执行、调度、本地 API、持久运行时 |
| CLI 和 TUI | TypeScript CLI + TypeScript Ink TUI |
| 插件 | TypeScript-first 的插件编写体验 |
| 模型 | Claude、OpenAI、Gemini 作为云端主力；Ollama 作为本地兜底 |
| 状态 | 文件系统是源事实；SQLite 是索引和查询层 |
| 同步 | 规划 LAN、Network、Git 三种同步；第一阶段以 Git/文件同步作为可靠基线 |
| 运行原则 | 容错优先，不追求模型输出完美 |

## 资料优先级

当资料互相冲突时，按以下顺序判断：

1. 你在本项目中表达的真实需求。
2. Claude 记忆文件：`C:\Users\骚人文客\.claude\projects\D--Great-Plan\memory\`。
3. 当前 demo 行为：`D:\Great Plan\demo\`。
4. Trellis 源码：`D:\Great Plan\Trellis\`。
5. Trellis 文档：`D:\Great Plan\trellis_docs\`。
6. 前端前辈文章：`D:\Great Plan\blog_articles\`。
7. 旧版 Claude 初稿：`D:\Great Plan\docs\`。

## MVP 边界

Forge MVP 不是通用 AI IDE，不是低代码平台，也不是所有 AI 编程工具的替代品。

MVP 要做到：

- 一条需求可以创建任务、拆解工作、分配角色、执行多模型调用，并产出项目成果。
- 失败可以降级为确定性计划、本地兜底文件、带警告完成，或明确的人类介入状态。
- TUI 能显示每个 Agent 正在做什么、产出了什么、失败在哪里、还剩哪些警告。
- 所有重要状态都能直接在磁盘上查看。

## 文档索引

| 文档 | 作用 |
| --- | --- |
| [产品需求](product-requirements.md) | 产品承诺、MVP 范围、成功标准 |
| [系统架构](architecture.md) | 系统分层、模块边界、数据流 |
| [工作流引擎](workflow-engine.md) | 从需求到交付的工作流和文件契约 |
| [Agent 系统](agent-system.md) | MVP 角色、分工规则、协作方式 |
| [上下文与记忆](context-memory.md) | 文件状态、SQLite 索引、上下文加载 |
| [模型网关与容错](model-gateway-resilience.md) | 模型路由、降级链、带警告完成 |
| [Harness 集成](harness-integrations.md) | Trellis 风格的平台集成方案 |
| [UI 与可观测性](ui-observability.md) | TypeScript Ink TUI、事件流、Agent Dashboard |
| [路线图与验证](roadmap-and-validation.md) | Demo 优先的开发路线 |
| [资料洞察](source-insights.md) | 从 Claude 记忆、demo、Trellis、文章中提取出的经验 |

## 文档质量标准

这套文档可进入实现阶段的标准是：任意一条用户需求，都能沿着文档追踪到输入、任务拆解、Agent 执行、失败兜底、验证、产物输出和用户可见状态。

