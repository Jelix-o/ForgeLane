# 系统架构

## 架构原则

1. **容错优先，不表演正确性**：模型输出一定会有格式错误、缺失和空响应，Forge 必须继续推进。
2. **文件是源事实**：任务状态、工作流状态、上下文清单、spec、journal 都必须能在磁盘上直接查看。
3. **SQLite 是索引，不是真相本身**：它用于搜索、排序、统计和缓存元数据。
4. **从一开始就是混合系统**：独立模型编排和 Trellis 风格平台 Harness 不是两个产品。
5. **执行过程必须可见**：每个 Agent 的状态变化都应该能被 TUI 观察到。

## 系统分层

```text
TypeScript CLI / Ink TUI
  -> 本地 HTTP/WebSocket API
Go core daemon
  -> workflow engine
  -> agent runtime
  -> model gateway
  -> file-state manager
  -> memory index
  -> harness adapter boundary
文件系统 + SQLite
AI providers 和本地模型
外部 AI 编辑器（通过 harness adapter）
```

## 主要模块

| 模块 | 运行时 | 职责 |
| --- | --- | --- |
| CLI | TypeScript | init、task 命令、配置命令、daemon 控制 |
| TUI | TypeScript Ink | 实时工作流面板、日志、警告、成本、产物 |
| Core daemon | Go | 本地服务、调度、编排、事件流 |
| Workflow engine | Go | 阶段流转、依赖图、重试和 fallback 策略 |
| Agent runtime | Go | 角色执行、上下文组装、产物捕获 |
| Model gateway | Go | Provider 抽象、路由、降级、预算、缓存 |
| File-state manager | Go | 读写 `.forge/` 下的 workflow、task、spec、workspace、runtime |
| Memory index | Go + SQLite | 索引决策、模式、警告、输出和摘要 |
| Harness adapters | Go + TS scripts | 对接 Claude Code、Cursor、Codex 等平台 |
| Plugin layer | TypeScript-first | 扩展 provider、检查器、导出器和外部集成 |

## 项目状态目录

```text
.forge/
├── workflow.md
├── config.yaml
├── tasks/
│   └── <task-id>/
│       ├── task.json
│       ├── prd.md
│       ├── info.md
│       ├── implement.jsonl
│       ├── check.jsonl
│       ├── artifacts/
│       └── research/
├── spec/
├── workspace/
│   └── <developer>/
│       ├── index.md
│       └── journal-1.md
├── memory.db
└── .runtime/
    ├── sessions/
    ├── events/
    └── locks/
```

这套结构有意借鉴 Trellis，但 Forge 额外增加了直接模型编排运行时。

## 数据流

```text
用户在 CLI/TUI 提交需求
  -> CLI 发送请求到 Go daemon
  -> daemon 创建任务目录
  -> workflow engine 进入 intake/planning
  -> architect 模型产出计划，失败则使用 fallback plan
  -> 整理 JSONL 上下文清单
  -> agent runtime 按角色扇出执行
  -> model gateway 路由模型并执行降级链
  -> 产物写入任务目录
  -> check 角色审查并自修复
  -> 生成 final package
  -> TUI 全程接收事件流
```

## 边界决策

- Go daemon 负责执行状态和事件顺序。
- TypeScript CLI/TUI 负责用户交互。
- 模型永远不直接拥有源事实状态；模型输出必须被解析、验证、规范化，必要时替换为兜底输出。
- 独立编排 MVP 不依赖任何外部 AI 编辑器。
- 平台 Harness 文件从 Forge 状态生成，不作为手写源事实维护。

## 部署模式

| 模式 | 作用 |
| --- | --- |
| 本地 daemon | MVP 默认模式，在开发者本机运行并写入本地 `.forge/` 状态 |
| Server 模式 | 后续多用户或远程执行模式，复用同一套 API |
| Docker 模式 | 用于可复现的长流程和 demo 环境 |

