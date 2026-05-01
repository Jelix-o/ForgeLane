# 工作流引擎

## 目标

工作流引擎负责把一条用户需求转成可控的软件交付流水线。即使某个 Agent 或模型调用失败，系统也应该继续向前推进。

## 阶段流

```text
intake
  -> planning
  -> research
  -> context setup
  -> implementation fan-out
  -> integration
  -> check and self-fix
  -> final package
```

## 阶段职责

| 阶段 | 职责 | 必须产出 |
| --- | --- | --- |
| intake | 接收用户需求并创建任务状态 | `task.json`、初始 `prd.md` |
| planning | Architect 拆解工作和依赖关系 | `info.md`、任务图 |
| research | 按需调研项目模式或外部资料 | `research/*.md` |
| context setup | 决定每个角色开始前必须读取什么 | `implement.jsonl`、`check.jsonl` |
| implementation fan-out | 安全地并行运行 frontend/backend/devops 工作 | 角色产物、日志 |
| integration | 合并各角色输出为一个交付物 | 集成后的产物列表 |
| check and self-fix | Tester 验证、修复或标记警告 | 检查报告 |
| final package | 总结结果并保存记忆 | 最终报告、journal 记录 |

## 文件契约

| 文件 | 契约 |
| --- | --- |
| `workflow.md` | 人类可读的阶段模型和路由规则 |
| `task.json` | 机器可读的任务元数据、状态、依赖图和结果状态 |
| `prd.md` | 用户目标、需求和验收标准 |
| `info.md` | 架构计划和实现说明 |
| `implement.jsonl` | 实现 Agent 必须加载的 spec、research、context 文件 |
| `check.jsonl` | 检查 Agent 必须加载的 spec、research、context 文件 |
| `artifacts/` | 生成的代码、报告、manifest 或交付文件 |
| `workspace/<developer>/journal-N.md` | 会话历史和经验沉淀 |

JSONL 行应该引用 spec 和 research 文件。不要把即将被 Agent 重写的代码文件预先注册进去。

## 状态模型

| 状态 | 含义 |
| --- | --- |
| `planning` | 任务已创建，但不能开始实现 |
| `ready` | PRD、计划和上下文清单已准备好 |
| `running` | 一个或多个角色线程正在执行 |
| `checking` | 正在集成或质量审查 |
| `completed` | 交付物存在，且检查通过 |
| `completed_with_warnings` | 交付物存在，但还有警告 |
| `blocked` | 需要用户输入 |
| `failed` | 没能产出有用交付物 |

`completed_with_warnings` 不是失败。它表示 fallback 路径产出了可用内容，但验证还不完整或存在风险。

## 执行规则

- 角色执行前必须先创建任务文件。
- Planning 必须有确定性 fallback。
- 只有依赖关系明确后才能并行执行。
- Check Agent 应尽可能自修复。
- Final package 必须包含警告，不能隐藏警告。
- 如果 check 阶段发现需求问题，工作流可以回滚到 planning。

## 混合模式

独立编排模式下，Forge 直接调用模型 provider。

平台 Harness 模式下，Forge 写入 task/context/spec 状态，然后让 Claude Code、Cursor、Codex 或其他平台执行兼容步骤。

两种模式共享同一份工作流状态，只是执行器不同。

