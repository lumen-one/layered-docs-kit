# 文档体系建立：主会话执行协议

> 环境无关。不依赖 Node、Shell 或任何特定 Agent API；范围核对与回退需要 git。主会话读完本文件，用自己的子代理能力派发；本套被选中但不能派发时，按同一顺序自己执行。
> 需要一次性执行时用 `../documentation-management-prompt.md`（单体精简版）。两套同时适用时，以本套为准；本套增加了状态文件、批次串行与子会话边界。

## 选择哪一套

- 能派发子代理，且预计待迁移文档超过 10 个：用本套。
- 能派发子代理，但待迁移文档不超过 10 个：本套仍可用；若最终 `batches.json` 为空，可跳过 `migrate`。
- 只能单会话且不能执行脚本：用单体版，项目很小也适用。
- 只能单会话但能执行脚本：用本套，并按 `dispatch-templates.md` 的「能执行脚本但没有子代理」一节生成状态脚本。

## 为什么拆

单体版一次会话要连续完成侦察、提案、建层级、迁移、写检查、审计，中途还要等用户确认。实际失败点有三个：上下文被前几个阶段的细节占满，迁移批次和检查实现互相挤占，评审时无法判断某段改动属于哪个阶段。

拆分后每个子会话有明确的前置产物、写入范围和验收标准；主会话只保留进度，不读任务卡正文。总量比单体版多，但单次负载可控。

## 会话模型

- **主会话**：读本文件与 `docs-plan/state.md`，按「取下一任务」选任务，把任务卡路径与输入拼成 prompt 派发，验收产物后更新状态。不读任务卡正文与参考文件，不读项目文档；派发与验收所需的写入范围、产物小节见 `dispatch-templates.md` 的「填充规则」，必要时只读任务卡的对应结构节。
- **子会话**：读 1 张任务卡、卡内点名的参考小节、前置产物；只写本任务允许的路径；结束时回报产物路径、自检结果、未决问题。
- **用户**：在三个检查点介入——启动问答（档位与侦察问题）、提案确认、删除类迁移操作确认。

## 上下文预算

| 角色 | 必读 | 预计行数 |
|---|---|---|
| 主会话 | 本文件 + `state.md` + `dispatch-templates.md` 的「通用模板」「填充规则」「主会话验收」 | 300–350 |
| 侦察子会话 | `cards/00-recon.md` + `reference/layers.md` 两节 + 项目文件 | 卡 96 + 参考 35 |
| 提案子会话 | `cards/01-proposal.md` + `recon.md` + 4 份参考的指定小节 | 卡 130 + 参考 380 |
| 建层级子会话 | `cards/02-skeleton.md` + `proposal.md` + `reference/layers.md` 两节 + `reference/decisions.md` 两节 + `reference/checks.md` 三项 | 卡 83 + 参考 65 |
| 迁移子会话（每批） | `cards/03-migrate.md` + 本批次定义 + `reference/layers.md` 三节 + `reference/checks.md` 四项 | 卡 89 + 参考 60 |
| 检查子会话 | `cards/04-checks.md` + `reference/checks.md` + `state.md` 的档位 | 卡 77 + 参考 115 |
| 审计子会话 | `cards/05-audit.md` + 4 份参考的指定小节 + 全部产物 | 卡 76 + 参考 180 |

## 开始

1. 建立 `docs-plan/`，把 `state-template.md` 复制成 `docs-plan/state.md`，填项目根；向用户确认档位（默认 `medium`）和 `cards/00-recon.md` 的 7 个问题，把档位写入状态文件头部，答案写入「待用户确认」表；问完后把启动问答行状态改为 `已确认 YYYY-MM-DD`，未答项仍由 recon 第 8 节跟踪。
2. 首次派发前确认工作区干净：`git status --short` 必须为空。不干净时请用户提交或 stash，主会话不自行处理。
3. 读 `state.md`，按「取下一任务」确定当前任务。派发所需的写入范围与产物小节从 `dispatch-templates.md` 的「填充规则」取；必要时只读任务卡的「产物结构」「报告结构」或「提案结构」一节。
4. 按 `dispatch-templates.md` 的通用模板拼 prompt；`recon` 任务把用户答案填入「用户答案」节，其他任务填「无」，然后派发给子会话。
5. 子会话返回后按「验收」核对；通过则更新 `state.md`，不通过则退回。
6. 每个任务（迁移为每批）验收通过后，必须由用户提交该任务产物（用户明确授权时主会话提交）；工作区干净后才派发下一个任务。
7. 重复直到状态表全部为 `done` 或 `skipped`。

主会话不需要预读整个项目，也不需要通读任务卡；任务卡正文由子会话自己读。

## 状态文件

`docs-plan/state.md` 是唯一进度来源与唯一阻塞来源，格式见 `state-template.md`。四个区：

- **阶段表**：六个阶段的 `pending` / `in-progress` / `blocked` / `done` / `skipped`。
- **批次表**：`migrate` 展开后的批次，状态为 `pending` / `in-progress` / `blocked` / `done`；一次只有一行是 `in-progress`。
- **待用户确认表**：需要用户决定的事项、默认选择、状态与答复；状态为 `待确认` / `待修订` / `已确认 YYYY-MM-DD`。
- **阻塞表**：任务、原因、解除条件、提出时间。

更新规则：

- 派发前把任务改为 `in-progress`，并写开始日期；`migrate` 阶段在派发第一个批次时改为 `in-progress`。
- 普通任务验收通过后改为 `done`，填产物路径与完成时间。
- **`proposal` 例外**：子会话产物验收通过后，阶段改回 `pending`，在「待用户确认」表的「提案确认」行写 `待确认`；用户确认后才改为 `done`，该行写 `已确认 YYYY-MM-DD`。用户否决时该行写 `待修订`，阶段改为 `in-progress`，派发修订任务。
- 验收不通过改为 `blocked`，在阻塞表写原因与解除条件；退回重派时改回 `in-progress`。
- 批次验收不通过把该批改为 `blocked` 并写阻塞表；重派时改回 `in-progress`，通过后改为 `done`。
- 状态只能由主会话改。子会话不写 `state.md`。

## 取下一任务

按固定顺序扫描阶段表：`recon` → `proposal` → `skeleton` → `migrate` → `checks` → `audit`。

1. 跳过 `done` 与 `skipped`。
2. 遇到阶段级 `blocked`：停止派发，把阻塞原因交给用户，不要自行绕过。
3. 遇到 `proposal`：
   - 「提案确认」行状态为 `待修订`：派发提案修订任务，输入含现有 `proposal.md`、`batches.json` 与用户否决意见。
   - `docs-plan/proposal.md` 不存在：派发新建提案任务。
   - `proposal.md` 存在且「提案确认」行状态不是 `已确认 YYYY-MM-DD`：停止并请用户确认，不重新派发。
   - 「提案确认」行状态已确认：把阶段改为 `done`，继续扫描。
4. 遇到 `migrate`：
   - 若存在 `in-progress` 批次：视为上次中断，先按「验收」核对；通过则改为 `done`，否则回退该批并改为 `pending`。
   - 若批次表为空且 `batches.json` 的 `batches` 为空数组，把阶段改为 `skipped`，备注写理由，继续扫描。
   - 若第一个非 `done` 批次是 `blocked`：停止派发，把原因交给用户。
   - 取第一个 `pending` 的批次；没有 `pending` 且没有 `in-progress` 时，把阶段改为 `done`，继续扫描。
   - 若该批 `acceptance` 含「需用户确认」或 `batches.json` 的 `deletion` 为 `true`，且批次表没有对应确认记录：在备注写 `待用户确认`，停止并请用户确认；确认后写 `已确认 YYYY-MM-DD`，再把该批改为 `in-progress` 并派发。
5. 第一个非 `done` / `skipped` 的阶段就是当前任务。

迁移批次禁止合并。一个子会话只处理一个批次，批次之间串行。

## 派发

用 `dispatch-templates.md` 的通用模板。六条约束：

- 一个 prompt 只含一个任务，不给子会话看后续阶段。
- 必读清单精确到任务卡路径和参考小节，不用「相关文档」这类模糊说法。
- 写入范围逐条列出；迁移任务额外要求子会话执行 `git status --short`、`git diff --name-only`、`git ls-files --others --exclude-standard`，并把逐行核对结果写入报告第 5 节。
- 迁移任务说明入站链接的例外范围：允许在本批 `files` 之外修改文件，但只能改指向本批文件的链接目标，且必须在报告第 3 节逐条列出。
- 前置产物给绝对路径或项目内相对路径，不粘贴正文。
- 子会话回报格式由任务卡规定，主会话不另立格式。

不同环境的派发方式、已填示例、单会话降级方式，见 `dispatch-templates.md`。

## 验收

一个产物只有同时满足四条才算完成：

1. **存在且完整**：任务卡声明的产物存在，包含卡内「产物结构」「报告结构」或「提案结构」列出的全部必需小节。
2. **范围未越界**：`git status --short` 的改动全部落在任务写入范围内（含未跟踪的新建文件）。迁移批次的范围是：本批 `files`、`docs-plan/migrate/<batchId>.md`、报告第 3 节列出的入站链接源文件（这些文件只允许改链接目标）。迁移批次核对 `git diff --name-only` 只涉及上述范围，并用 `git ls-files --others --exclude-standard` 补查新建文件。
3. **基线干净**：派发前已确认 `git status --short` 为空（主会话在派发前检查）。派发前不干净时停止，不判通过，也不得跳过该检查。
4. **未决问题已处置**：子会话回报中的未决问题为空，或已由主会话写入 `state.md` 的「阻塞」表并标记 `blocked`。

任一不满足就退回，不要用 `done` 跳过。连续两次失败时把失败原因交给用户，由用户决定缩小批次或调整任务卡。

`recon` 验收通过后，若产物第 8 节仍有未答复的待确认项，主会话逐条询问用户，把答复追加到该项下；全部有答复后才更新状态并进入 `proposal`。

## 并行

| 阶段 | 并行 | 原因 |
|---|---|---|
| `recon` | 否 | 需要全局视图 |
| `proposal` | 否 | 依赖完整现状 |
| `skeleton` | 否 | 会创建同一批层级与模板文件 |
| `migrate` | 否 | 同仓写冲突；死链检查需要完整批次 |
| `checks` | 是 | 每个检查器独立成文件，可分别实现后再统一接入 |
| `audit` | 否 | 需要检查全部完成 |

并行实现检查器时，每个子会话只写自己的脚本、夹具与 `docs-plan/checks/<checkId>.md` 分项报告，不改入口文件；某个分项失败时只把该分项和 `checks-report.md` 的对应行标为失败并单独重试，不影响其他分项。最后必须有一个串行会话汇总 `docs-plan/checks-report.md`，统一接入 `package.json` / `Makefile` / CI，并由主会话合并验收。

## 失败与回退

- **子会话无产出或回报缺项**：同一 prompt 重试一次；仍失败则把任务拆小或换实现路径，不重复第三次。
- **产物越界**：已跟踪文件用 `git restore -- <路径>` 回退；本任务新建的未跟踪文件直接删除。不要手工保留半成品。
- **迁移批次出错**：`git restore -- <本批已跟踪文件与报告第 3 节列出的入站链接源文件>`，删除本批新建文件，重新派发。批次报告一并删除。
- **用户否决提案**：主会话把否决意见写入「待用户确认」表的「提案确认」行，把 `proposal` 阶段改为 `in-progress`，派发提案修订任务；修订验收通过后回到确认点。
- **检查发现已完成批次失败**：把对应 `migrate` 批次改回 `pending`、`migrate` 阶段改为 `in-progress`，同时把 `checks` 阶段改回 `pending`；回退并重派该批，修复后重新运行 `checks`。不得直接在 `checks` 阶段改被检查文档。
- **检查无法通过且规则本身有误**：记录到 `state.md` 的阻塞表，由用户决定改规则还是改内容。不得私自删除检查项。

## 裁剪

档位只影响检查默认组合与提案建议层级，不改变阶段顺序：

- `basic`：单人小项目。建议 L0a + L1 + L2，不建决策记录；检查只上 1 死链、2 锚点、3 模块 README 章节结构。
- `medium`：默认值。增加 L5、L7；检查增加 4 决策记录格式、5 段落内不硬换行、6 字数预算、7 引用。
- `multilingual`：在 `medium` 上增加 8 双语一致性、9 语言切换器。
- 有生成物时统一增加 10 生成物新鲜度；11、12、13 保持可选。

只有两种情况可以跳过阶段，其余阶段不可跳过：

- `migrate`：`batches.json` 的 `batches` 为空数组（没有存量文档需要迁移）。
- `checks`：用户明确接受不建自动检查；此时 `audit` 交付物第 3 项改为评审清单，并在阶段表备注记录用户决定。

跳过时把阶段改为 `skipped`，并在阶段表「备注」列记录理由。跳过不属于阻塞，不写入阻塞表。

## 任务表

| 任务 ID | 任务卡 | 产出 | 用户确认 | 并行 |
|---|---|---|---|---|
| `recon` | `cards/00-recon.md` | `docs-plan/recon.md` | 否（问答在第 8 节闭环） | 否 |
| `proposal` | `cards/01-proposal.md` | `docs-plan/proposal.md`、`docs-plan/batches.json` | **是** | 否 |
| `skeleton` | `cards/02-skeleton.md` | 项目内层级与模板、`docs-plan/skeleton-report.md` | 否 | 否 |
| `migrate` | `cards/03-migrate.md` | 批次内文档、`docs-plan/migrate/<batchId>.md` | 文件级删除时是 | 否 |
| `checks` | `cards/04-checks.md` | 检查脚本、`docs-plan/checks/<checkId>.md`、CI 入口、`docs-plan/checks-report.md` | 否 | 是（独立检查器） |
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