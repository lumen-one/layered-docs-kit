# 文档体系建立：主会话执行协议

> 环境无关。不依赖 Node、Shell 或任何特定 Agent API。主会话读完本文件，用自己的子代理能力派发；不能派发时，按同一顺序自己执行。
> 需要一次性执行时用 `../documentation-management-prompt.md`（单体版）。本套文档与它内容等价，只是按会话边界切开。

## 选择哪一套

- 能派发子代理，且待迁移文档超过 10 个：用本套。
- 只能单会话，或项目很小：用单体版。
- 能执行脚本但没有子代理：用本套，并让主会话按 `dispatch-templates.md` 最后一节生成状态脚本。

## 为什么拆

单体版一次会话要连续完成侦察、提案、建层级、迁移、写检查、审计，中途还要等用户确认。实际失败点有三个：上下文被前几个阶段的细节占满，迁移批次和检查实现互相挤占，评审时无法判断某段改动属于哪个阶段。

拆分后每个子会话有明确的前置产物、写入范围和验收标准；主会话只保留进度，不读任务卡正文。总量比单体版多，但单次负载可控。

## 会话模型

- **主会话**：读本文件与 `docs-plan/state.md`，按「取下一任务」选任务，把任务卡路径与输入拼成 prompt 派发，验收产物后更新状态。不读任务卡，不读参考文件，不读项目文档。
- **子会话**：读 1 张任务卡、卡内点名的参考小节、前置产物；只写本任务允许的路径；结束时回报产物路径、自检结果、未决问题。
- **用户**：在两个检查点介入——提案确认、删除类迁移操作确认。

## 上下文预算

| 角色 | 必读 | 预计行数 |
|---|---|---|
| 主会话 | 本文件 + `state.md` + `dispatch-templates.md` 的「通用模板」 | 260–300 |
| 侦察子会话 | `cards/00-recon.md` + `reference/layers.md` 两节 + 项目文件 | 卡 95 + 参考 40 |
| 提案子会话 | `cards/01-proposal.md` + `recon.md` + 4 份参考 | 卡 122 + 参考 300 |
| 建层级子会话 | `cards/02-skeleton.md` + `proposal.md` + `reference/layers.md` 两节 | 卡 82 + 参考 60 |
| 迁移子会话（每批） | `cards/03-migrate.md` + 本批次定义 + `reference/layers.md` 三节 | 卡 86 + 参考 40 |
| 检查子会话 | `cards/04-checks.md` + `reference/checks.md` | 卡 74 + 参考 97 |
| 审计子会话 | `cards/05-audit.md` + 4 份参考 + 全部产物 | 卡 73 + 参考 300 |

## 开始

1. 建立 `docs-plan/`，把 `state-template.md` 复制成 `docs-plan/state.md`，填项目根与档位。
2. 读 `state.md`，按「取下一任务」确定当前任务。
3. 按 `dispatch-templates.md` 的通用模板拼 prompt，派发给子会话。
4. 子会话返回后按「验收」核对；通过则更新 `state.md`，不通过则退回。
5. 重复直到状态表全部为 `done` 或 `skipped`。

主会话不需要预读整个项目，也不需要先读任务卡。任务卡由子会话自己读。

## 状态文件

`docs-plan/state.md` 是唯一进度来源，格式见 `state-template.md`。四个区：

- **阶段表**：六个阶段的 `pending` / `in-progress` / `blocked` / `done` / `skipped`。
- **批次表**：`migrate` 展开后的批次，一次只有一行是 `in-progress`。
- **待确认表**：需要用户决定的事项与默认选择。
- **阻塞表**：任务、原因、解除条件。

更新规则：

- 派发前把任务改为 `in-progress`，并写开始日期。
- 验收通过后改为 `done`，填产物路径与完成时间。
- 验收不通过改为 `blocked`，在阻塞表写原因与解除条件；退回时改回 `in-progress`。
- 状态只能由主会话改。子会话不写 `state.md`。

## 取下一任务

按固定顺序扫描阶段表：`recon` → `proposal` → `skeleton` → `migrate` → `checks` → `audit`。

1. 跳过 `done` 与 `skipped`。
2. 遇到 `blocked`：停止派发，把阻塞原因交给用户，不要自行绕过。
3. 遇到 `proposal` 且阶段为 `pending`：如果 `docs-plan/proposal.md` 不存在，派发提案任务；如果存在但「待确认表」里没有确认记录，停止并请用户确认；确认后把阶段改为 `done` 再继续。
4. 遇到 `migrate`：取批次表中第一个 `pending` 的批次派发；没有剩余批次时把阶段改为 `done`。
5. 第一个非 `done` / `skipped` 的阶段就是当前任务。

迁移批次禁止合并。一个子会话只处理一个批次，批次之间串行。

## 派发

用 `dispatch-templates.md` 的通用模板。五条约束：

- 一个 prompt 只含一个任务，不给子会话看后续阶段。
- 必读清单精确到任务卡路径和参考小节，不用「相关文档」这类模糊说法。
- 写入范围逐条列出；迁移任务额外给出 `git diff --name-only` 的自检命令。
- 前置产物给绝对路径或项目内相对路径，不粘贴正文。
- 子会话回报格式由任务卡规定，主会话不另立格式。

不同环境的派发方式、已填示例、单会话降级方式，见 `dispatch-templates.md`。

## 验收

一个产物只有同时满足三条才算完成：

1. **存在且完整**：任务卡声明的产物存在，包含卡内「产物结构」列出的全部必需小节。
2. **范围未越界**：`git status --short` 的改动全部落在任务写入范围内；迁移批次核对 `git diff --name-only` 只涉及本批 `files`。
3. **未决问题已处置**：子会话回报中的未决问题为空，或已写入 `docs-plan/blockers.md` 并标记 `blocked`。

任一不满足就退回，不要用 `done` 跳过。连续两次失败时把失败原因交给用户，由用户决定缩小批次或调整任务卡。

## 并行

| 阶段 | 并行 | 原因 |
|---|---|---|
| `recon` | 否 | 需要全局视图 |
| `proposal` | 否 | 依赖完整现状 |
| `skeleton` | 否 | 会改写同一批入口文件 |
| `migrate` | 否 | 同仓写冲突；死链检查需要完整批次 |
| `checks` | 是 | 每个检查器独立成文件，可分别实现后再统一接入 |
| `audit` | 否 | 需要检查全部完成 |

并行实现检查器时，每个子会话只写自己的脚本文件；最后必须有一个串行会话统一接入 `package.json` 或 CI，并由主会话合并验收。

## 失败与回退

- **子会话无产出或回报缺项**：同一 prompt 重试一次；仍失败则把任务拆小或换实现路径，不重复第三次。
- **产物越界**：让子会话用 `git checkout` 回退本任务文件后重做；不要手工保留半成品。
- **迁移批次出错**：`git checkout -- <本批 files>` 回退整批，重新派发。批次报告一并删除。
- **用户否决提案**：更新 `proposal.md` 与 `batches.json`，把 `proposal` 改回 `pending`，重走确认点。
- **检查无法通过且规则本身有误**：记录到 `docs-plan/blockers.md`，由用户决定改规则还是改内容。不得私自删除检查项。

## 裁剪

档位只影响检查默认组合与提案建议层级，不改变阶段顺序：

- `basic`：单人小项目。建议 L0a + L1 + L2，不建决策记录；检查只上死链、锚点、README 章节结构。
- `medium`：默认值。增加决策记录与字数预算。
- `multilingual`：在 `medium` 上增加双语配对与一致性检查。

用户决定不做某个阶段时，把阶段改为 `skipped`，并在 `blockers.md` 记录理由。

## 任务表

| 任务 ID | 任务卡 | 产出 | 用户确认 | 并行 |
|---|---|---|---|---|
| `recon` | `cards/00-recon.md` | `docs-plan/recon.md` | 否 | 否 |
| `proposal` | `cards/01-proposal.md` | `docs-plan/proposal.md`、`docs-plan/batches.json` | **是** | 否 |
| `skeleton` | `cards/02-skeleton.md` | 项目内层级与模板、`docs-plan/skeleton-report.md` | 否 | 否 |
| `migrate` | `cards/03-migrate.md` | 批次内文档、`docs-plan/migrate/<batchId>.md` | 删除类操作时是 | 否 |
| `checks` | `cards/04-checks.md` | 检查脚本、CI 入口、`docs-plan/checks-report.md` | 否 | 是（独立检查器） |
| `audit` | `cards/05-audit.md` | `docs-plan/audit-report.md`、交付物五项 | 否 | 否 |

## 文件清单

```text
docs-management-kit/
  INDEX.md                 本文件，主会话每次必读
  state-template.md        复制到项目后作为进度表
  dispatch-templates.md    派发模板与各环境做法
  cards/
    00-recon.md
    01-proposal.md
    02-skeleton.md
    03-migrate.md
    04-checks.md
    05-audit.md
  reference/
    layers.md              作用域、分层、README 契约、写作规则、维护表
    decisions.md           决策记录全生命周期
    bilingual.md           双语配对
    checks.md              自动检查目录与接入规则
```
