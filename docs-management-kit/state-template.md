# 进度表模板

复制到 `<project>/docs-plan/state.md`，每阶段一行。

| 阶段 | 产物 | 备注 |
|---|---|---|
| 现状评估 | `docs-plan/recon.md` | |
| 架构提案 | `docs-plan/proposal.md`、`docs-plan/batches.json` | 需用户确认 |
| 层级与规则 | `docs-plan/skeleton-report.md` | |
| 迁移 | `docs-plan/migrate/<批次>.md` | 一批一行 |
| 自动检查 | `docs-plan/checks-report.md` | |
| 审计与交付 | `docs-plan/audit-report.md` | |

迁移阶段按批展开，例如 `迁移：core-architecture`，每批一行。备注写跳过理由、阻塞原因或用户决定。

不规定状态值，也不规定什么时候更新。做完就把产物路径写上，需要提醒自己的事写进备注。
