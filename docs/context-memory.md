# 上下文与记忆

## 核心规则

Forge 继承 Trellis 的关键经验：持久状态应该能从文件系统直接读取。SQLite 很有用，但不能成为唯一保存项目真相的地方。

```text
文件 = 源事实
SQLite = 索引、搜索、排序、统计、缓存元数据
模型上下文 = 基于文件和索引结果生成的视图
```

## 文件源事实

| 领域 | 文件来源 |
| --- | --- |
| 工作流 | `.forge/workflow.md` |
| 任务状态 | `.forge/tasks/<task-id>/task.json` |
| 需求 | `.forge/tasks/<task-id>/prd.md` |
| 技术计划 | `.forge/tasks/<task-id>/info.md` |
| 上下文清单 | `.forge/tasks/<task-id>/*.jsonl` |
| 工程规范 | `.forge/spec/` |
| 会话记忆 | `.forge/workspace/<developer>/journal-N.md` |
| 运行时指针 | `.forge/.runtime/sessions/` |

## SQLite 职责

SQLite 保存派生和可查询数据：

- 从任务文件和 journal 中提取出的记忆。
- 决策索引。
- 警告和失败历史。
- 产物元数据。
- 缓存 key 和 provider 响应元数据。
- 成本和 token 统计。

如果 SQLite 被删除，Forge 应该能从文件重建主要索引。部分缓存和历史细节可以丢失，但任务真相不能丢。

## 记忆类型

| 类型 | 含义 |
| --- | --- |
| `decision` | 为什么做出某个技术或产品选择 |
| `pattern` | 可复用的实现模式或工作流模式 |
| `warning` | 已完成但有风险的结果 |
| `error` | 失败以及解决方式 |
| `artifact` | 生成产物的元数据 |
| `requirement` | 重要用户约束 |
| `handoff` | 其他 Agent 或未来会话需要的信息 |

## 上下文加载

Forge 应使用渐进式上下文加载：

| 时机 | 上下文 |
| --- | --- |
| Session start | 当前项目、活动任务、最近 journal、工作流摘要 |
| User request | 当前 workflow breadcrumb 和活动任务状态 |
| Role execution | PRD、info、角色相关 JSONL 条目、相关 spec |
| Check phase | diff / artifacts、check JSONL、警告、验收标准 |
| Final package | 任务摘要、决策、警告、产物 |

## 同步边界

第一版可靠同步格式应该基于文件。SQLite 同步可以在文件同步稳定后再做。

推荐顺序：

1. Git 同步 `.forge/spec/`、`.forge/tasks/` 和 `.forge/workspace/`。
2. 导出/导入 memory index 快照。
3. 为实时状态增加 LAN 或 network sync。

## 隐私

记忆提取必须支持在发送给外部模型前脱敏。API key、内部域名、个人身份信息等，在没有明确允许时都应该被 redaction。

