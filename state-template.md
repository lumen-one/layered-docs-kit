# 进度表模板

> 复制到 `<project>/docs-plan/state.md` 后填写。主会话每次只读这一份就能判断进度。
> 阶段状态值：`pending`、`in-progress`、`blocked`、`done`、`skipped`。
> 批次状态值：`pending`、`in-progress`、`blocked`、`done`。

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

| 序号 | 批次 ID | 状态 | 报告 | 开始 | 完成 | 备注 |
|---|---|---|---|---|---|---|
| 1 | <batchId> | pending | docs-plan/migrate/<batchId>.md | | | 提案确认后替换本行 |

## 待用户确认

| 事项 | 默认选择 | 状态 | 答复 |
|---|---|---|---|
| 启动问答（档位与 00-recon 的 7 个问题） | 档位 medium；其余未答项由 recon 第 8 节列出 | 待确认 | |
| 提案确认（层级、批次、删除清单） | 按提案第 1–2 节的层级、第 7 节的批次、第 8 节的删除清单，以及第 10 节的待确认事项 | 待确认 | |

## 阻塞

| 任务 | 原因 | 解除条件 | 提出时间 |
|---|---|---|---|
```

## 使用规则

- 建立本文件后，先请用户提交（用户明确授权时主会话提交），再确认 `git status --short` 为空并开始首次派发；未提交不得派发。
- 阶段表六行不增不减。只有 `migrate`（`batches.json` 的 `batches` 为空数组）与 `checks`（用户明确接受不建自动检查）可以置为 `skipped`，其余阶段不可跳过；跳过的理由写阶段表「备注」。
- 批次表在 `proposal` 确认后按 `batches.json` 逐行展开，一行一个批次；`batches` 为空数组时删除占位行，并把 `migrate` 阶段置为 `skipped`。提案确认后不得保留 `<batchId>` 占位行。
- 同一时间只有一个阶段是 `in-progress`，`migrate` 阶段只有一个批次是 `in-progress`。
- 子会话只读 `state.md`，不写；所有状态变更由主会话完成。
- 阻塞只写在本文件的「阻塞」表，不要另建独立文件。跳过某阶段时把理由写进阶段表「备注」，不写阻塞表。
- `proposal` 子会话验收通过后，阶段改回 `pending`，「提案确认」行状态写 `待确认`；用户确认后阶段才改 `done`，该行写 `已确认 YYYY-MM-DD`。用户否决时该行写 `待修订`，阶段改 `in-progress`。
- 删除类批次派发前，在批次表「备注」追加 `待用户确认`；确认后改为 `已确认 YYYY-MM-DD` 再派发。备注已有多项内容时用 `；` 分隔，确认记录必须是完整的 `已确认 YYYY-MM-DD`。这里的删除指删除文件或目录；移动文件按删除旧路径处理，同样需要确认；批次内段落级内容删除不受此限制，但要写进批次报告。
- 每个任务（迁移为每批）验收通过后，必须由用户提交（用户明确授权时由主会话提交）；工作区干净后才派发下一个任务。
- 每次派发前工作区必须干净：`git status --short` 为空。不干净时请用户提交或 stash，主会话不自行处理，也不得跳过该检查。
- 阶段与批次都完成，且 `checks` 完成或经用户明确同意跳过，才进入 `audit`。