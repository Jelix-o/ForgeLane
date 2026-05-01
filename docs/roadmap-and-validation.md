# 路线图与验证

## 当前基线

`D:\Great Plan\demo\` 下的 Python demo 是当前验证基线。它包含：

- `forge_demo.py` 作为主编排器。
- `gateway.py` 通过 Anthropic-compatible 代理调用 `mimo-v2.5`。
- 5 个角色：architect、frontend developer、backend developer、tester、devops。
- 对 planning failure、empty model output、missing files、non-blocking checks 的 fallback 行为。

结论很明确：在文档和 demo 都让工作流变得“自然且不可避免”之前，不急着进入正式开发。

## 路线图

| 阶段 | 目标 | 验证方式 |
| --- | --- | --- |
| 0.1 Docs v2 | 对齐产品意图、架构、工作流和资料洞察 | 一条需求能在文档中端到端追踪 |
| 0.2 Python demo hardening | 继续验证无人值守多角色执行 | 非平凡任务能运行数小时且不卡死 |
| 0.3 Go core skeleton | 本地 daemon、任务文件、事件流、基础 gateway | 一个任务能跑过 planning 和 fallback |
| 0.4 TypeScript CLI/TUI | 用户能启动、观察、检查工作流 | TUI 展示 Agent、警告、产物、成本 |
| 0.5 Multi-agent MVP | 并行角色执行、集成和检查 | 全栈 demo 产出可审阅 package |
| 0.6 Harness MVP | 使用 Forge task/context 状态接入 Claude Code | 外部 AI 编辑器能继续或检查 Forge 任务 |
| 1.0 Product beta | 稳定混合工作流、文档、demo、安装路径 | 前端开发者不读源码也能使用 |

## Demo 验收关口

正式开发加速前，每个 gate 都应该通过：

1. **Workflow gate**：一条指令能创建任务状态并到达 final package。
2. **Resilience gate**：强制空输出/坏格式输出时，仍能产出有用 fallback artifacts。
3. **Observability gate**：用户能从 TUI 理解进度和警告。
4. **Memory gate**：决策和警告被保存到文件并建立索引。
5. **Harness gate**：至少一个外部 AI 编辑器能正确加载 Forge 上下文。

## MVP 验证任务

使用这些 demo 请求：

- 构建带 auth、API、database、tests、README 的全栈 todo app。
- 在已有小项目中添加功能，并保持现有约定。
- 生成一个前端为主、后端很轻的应用。
- 禁用主模型运行，验证 fallback 行为。
- 强制某个角色失败，验证工作流是否可见地降级。

## Docs v2 完成标准

Docs v2 完成时应满足：

- 正式文档不再把 TUI 写成 Go 技术栈。
- Claude memory 中的关键决策都已覆盖。
- Python demo 的容错经验已经变成正式契约。
- Trellis-derived 机制都标明是借鉴、扩展还是暂缓。
- MVP 角色和后续角色没有混在一起。
