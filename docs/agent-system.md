# Agent 系统

## MVP 角色

Forge MVP 使用 5 个角色。这个选择和当前 Python demo 对齐，可以让第一版实现保持聚焦。

| 角色 | ID | 主要产出 |
| --- | --- | --- |
| 架构师 | `architect` | 计划、任务图、接口、依赖关系 |
| 前端开发 | `frontend_dev` | UI 代码、客户端状态、交互行为 |
| 后端开发 | `backend_dev` | API、持久化、服务、集成逻辑 |
| 测试工程师 | `tester` | 检查、测试说明、自修复、警告报告 |
| DevOps 工程师 | `devops` | 脚本、环境、运行手册、打包 |

## 后续角色

| 角色 | 何时加入 |
| --- | --- |
| Research Agent | 当项目/源码调研成为瓶颈时 |
| Product Analyst | 当需求需要结构化产品分析时 |
| Supervisor | 当多个 Agent 冲突需要仲裁时 |
| Security Reviewer | 当涉及敏感数据、认证、部署或 prompt 注入风险时 |

这些角色属于 v2+ 扩展，不应成为第一版 MVP demo 的必要条件。

## 分配规则

- 非平凡任务先由 Architect 执行。
- 合约清晰时，frontend 和 backend 可以并行。
- Tester 至少等到一个实现产物出现后再执行。
- DevOps 可以在早期负责脚手架，也可以在后期负责打包。
- 如果角色分配失败，Forge 使用确定性的 fallback 任务图。

## Agent 输出契约

每个角色必须产出：

- 状态：`completed`、`completed_with_warnings`、`blocked` 或 `failed`。
- 写入或修改的产物。
- 做出的假设。
- 警告。
- 下一步建议。

只有模型散文式回答，不算有效角色输出。

## 协作模式

| 模式 | 使用场景 |
| --- | --- |
| Pipeline | plan -> implement -> check -> package |
| Fan-out / fan-in | frontend/backend/devops 独立工作后汇总 |
| Router | 为小任务选择合适角色 |
| Supervisor | 后续用于冲突仲裁和多 Agent 评估 |

## 工具边界

- MVP Agent 只写入当前任务输出工作区或生成项目目录。
- git commit / push 由主工作流控制，不交给单个 Agent。
- Check Agent 可以编辑生成产物来修复问题。
- Research-only Agent 不允许修改文件。

## 冲突处理

如果 Agent 产物互相不兼容：

1. Integration 阶段记录冲突。
2. Tester 尝试做机械性检测。
3. Architect 给出合并决策。
4. 如果置信度低，工作流进入 `blocked`，并给用户明确选项。

