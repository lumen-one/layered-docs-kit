# 进度表模板

> 复制到 `<project>/docs-plan/state.md` 后填写。主会话每次只读这一份就能判断进度。
> 状态值：`pending`、`in-progress`、`blocked`、`done`、`skipped`。

```markdown
# 文档体系建设状态

- 项目根：<绝对路径>
- 档位：basic | medium | multilingual（默认 medium，用户确认后调整）
- 文档所有者：<姓名或角色>
- 更新于：YYYY-MM-DD HH:mm

## 阶段

| 阶段 | 状态 | 产物 | 开始 | 完成 | 备注 |
|---|---|---|---|---|---|
| recon | pending | docs-plan/recon.md | | | |
| proposal | pending | docs-plan/proposal.md、docs-plan/batches.json | | | |
| skeleton | pending | docs-plan/skeleton-report.md | | | |
| migrate | pending | docs-plan/migrate/<batchId>.md | | | |
| checks | pending | docs-plan/checks-report.md | | | |
| audit | pending | docs-plan/audit-report.md | | | |

## 迁移批次

| 序号 | 批次 ID | 状态 | 报告 | 备注 |
|---|---|---|---|---|
| 1 | <batchId> | pending | docs-plan/migrate/<batchId>.md | 提案确认后替换本行 |

## 待用户确认

| 事项 | 默认选择 | 状态 |
|---|---|---|
| 提案确认（层级、批次、删除清单） | 按提案第 1–2 节的层级、第 7 节的批次、第 8 节的删除清单 | 待确认 |

## 阻塞

| 任务 | 原因 | 解除条件 | 提出时间 |
|---|---|---|---|
```

## 使用规则

- 阶段表六行不增不减；`basic` 档位按实际需要把 `migrate` 或 `audit` 置为 `skipped`，不删除行。
- 批次表在 `proposal` 完成后按 `batches.json` 逐行展开，一行一个批次；提案确认前只有占位行，不代表真实批次。
- 同一时间只有一个阶段是 `in-progress`，`migrate` 阶段只有一个批次是 `in-progress`。
- 子会话只读 `state.md`，不写；所有状态变更由主会话完成。
- 阻塞只写在本文件的「阻塞」表，不要另建独立文件。跳过某阶段时把理由写进阶段表「备注」，不写阻塞表。
- 删除类批次派发前，在批次表「备注」写 `待用户确认`；确认后改为 `已确认 YYYY-MM-DD` 再派发。
- 提案确认后，把「待用户确认」行的状态改为 `已确认 YYYY-MM-DD`，并把 `proposal` 阶段改为 `done`。
- 阶段与批次都完成后再进入 `audit`。
