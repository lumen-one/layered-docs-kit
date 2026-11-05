# 文档体系建立：主会话执行协议

> 环境无关。不依赖 Node 或特定脚本运行时，但范围核对与回退需要 git CLI。主会话读完本文件，用自己的子代理能力派发；本套被选中但不能派发时，按同一顺序自己执行。
> 需要一次性执行时用单体版 `documentation-management-prompt.md`；两套的适用关系、路径约定与文件清单见 `README.md`。

> 选型、为什么拆、文件清单与维护约定见 `README.md`。本文件只保留执行协议。

## 会话模型

- **主会话**：读本文件、`docs-plan/state.md`、`reference/acceptance.md`，按「取下一任务」选任务，把任务卡路径与输入拼成 prompt 派发，验收产物后更新状态。不读任务卡的步骤、禁止与回报正文，不读实现参考与项目文档；派发与验收所需的写入范围、产物小节见 `dispatch-templates.md` 的「填充规则」，必要时只读任务卡的「产物结构」「报告结构」或「提案结构」一节。
- **子会话**：读 1 张任务卡、卡内点名的参考小节、前置产物；只写本任务允许的路径；结束时回报产物路径、自检结果、未决问题。
- **用户**：在三个决策检查点介入——启动问答（档位与侦察问题）、提案确认、删除类迁移操作确认；每个任务完成后的提交由用户完成，或由用户明确授权主会话完成。

## 上下文预算

| 角色 | 必读 | 负载 |
|---|---|---|
| 主会话 | 本文件 + `state.md` + `dispatch-templates.md` 的「启动问答」「通用模板」「填充规则」 + `reference/acceptance.md` | 中 |
| 侦察子会话 | `cards/00-recon.md` + `reference/layers.md` 两节 + `reference/thresholds.md` 的档位组合 + `reference/checks.md` 的「扫描范围与排除」+ 项目文件 | 低 |
| 提案子会话 | `cards/01-proposal.md` + `recon.md` + 5 份参考的指定小节 | 高 |
| 建层级子会话 | `cards/02-skeleton.md` + `proposal.md` + `reference/layers.md` 四节 + `reference/decisions.md` 三节 + `reference/checks.md` 三项 | 中 |
| 迁移子会话（每批） | `cards/03-migrate.md` + 本批次定义 + `reference/layers.md` 三节 + `reference/checks.md` 四项；新建决策记录时加 `decisions.md` 两节 | 中 |
| 检查子会话 | `cards/04-checks.md` + `reference/checks.md` + `reference/thresholds.md` 的档位组合 + `reference/decisions.md` 的「检查要点」+ `reference/bilingual.md` 的「检查要点」+ `proposal.md` 第 5 节 + `state.md` 的档位与阶段备注 | 中高 |
| 审计子会话 | `cards/05-audit.md` + 4 份参考的指定小节 + 全部产物 + 项目文档与相关源码 | 高 |

负载按必读范围判断，不按固定行数；文件增删后不需要同步本表。

## 开始

1. 建立 `docs-plan/`，把 `state-template.md` 复制成 `docs-plan/state.md`，填项目根；向用户确认档位（默认 `medium`）和 `dispatch-templates.md` 的「启动问答」7 个问题，把档位写入状态文件头部，答案写入「待用户确认」表；问完后把启动问答行状态改为 `已确认 YYYY-MM-DD`，未答项仍由 recon 第 8 节跟踪。
2. 首次派发前按 `reference/acceptance.md` 的「派发前基线」建立干净基线。
3. 读 `state.md`，按「取下一任务」确定当前任务。派发所需的写入范围与产物小节从 `dispatch-templates.md` 的「填充规则」取；必要时只读任务卡的「产物结构」「报告结构」或「提案结构」一节。
4. 按 `dispatch-templates.md` 的通用模板拼 prompt；`recon` 任务把用户答案填入「用户答案」节，`proposal-revise` 任务把用户否决意见填入该节，其他任务填「无」，然后派发给子会话。
5. 子会话返回后按 `reference/acceptance.md` 的「产物验收」核对；通过则更新 `state.md`，不通过则退回。
6. 每个任务（迁移为每批）验收通过后，必须由用户提交该任务产物（用户明确授权时主会话提交）；除 `docs-plan/state.md` 外工作区干净后才派发下一个任务。
7. 重复直到状态表全部为 `done` 或 `skipped`。

主会话不需要预读整个项目，也不需要通读任务卡；任务卡的步骤与回报由子会话自己读。

## 状态文件

`docs-plan/state.md` 是唯一进度来源与唯一阻塞来源，格式见 `state-template.md`。四个区：

- **阶段表**：六个阶段的 `pending` / `in-progress` / `blocked` / `done` / `skipped`。
- **批次表**：`migrate` 展开后的批次，状态为 `pending` / `in-progress` / `blocked` / `done`；一次只有一行是 `in-progress`。
- **待用户确认表**：需要用户决定的事项、默认选择、状态与答复；状态为 `待确认` / `待修订` / `已确认 YYYY-MM-DD`。
- **阻塞表**：任务、原因、解除条件、提出时间。

更新规则：

- 派发前把任务改为 `in-progress`，并写开始日期；`migrate` 阶段在派发第一个批次时改为 `in-progress`。
- 普通任务验收通过后改为 `done`，填产物路径与完成时间。
- **`proposal` 例外**：子会话产物验收通过后，阶段改回 `pending`，在「待用户确认」表的「提案确认」行写 `待确认`；用户确认后才改为 `done`，该行写 `已确认 YYYY-MM-DD`。用户否决时该行写 `待修订`，阶段改为 `in-progress`，派发提案修订任务。
- 验收不通过改为 `blocked`，在阻塞表写原因与解除条件；退回重派时改回 `in-progress`。
- 批次验收不通过把该批改为 `blocked` 并写阻塞表；重派时改回 `in-progress`，通过后改为 `done`。
- 状态只能由主会话改。子会话不写 `state.md`。

## 取下一任务

按固定顺序扫描阶段表：`recon` → `proposal` → `skeleton` → `migrate` → `checks` → `audit`。

1. 跳过 `done` 与 `skipped`。
2. 遇到阶段级 `blocked`：停止派发，把阻塞原因交给用户，不要自行绕过。
3. 遇到 `proposal`：
   - 「提案确认」行状态为 `待修订`：按 `dispatch-templates.md` 的 `proposal-revise` 行派发提案修订任务，输入含现有 `proposal.md`、`batches.json` 与用户否决意见。
   - `docs-plan/proposal.md` 不存在：派发新建提案任务。
   - `proposal.md` 存在且「提案确认」行状态不是 `已确认 YYYY-MM-DD`：停止并请用户确认，不重新派发。
   - 「提案确认」行状态已确认：把阶段改为 `done`，继续扫描。
4. 遇到 `migrate`：
   - 若存在 `in-progress` 批次：视为上次中断，先按 `reference/acceptance.md` 核对；通过则改为 `done`，否则回退该批并改为 `pending`。
   - 若批次表为空且 `batches.json` 的 `batches` 为空数组，把阶段改为 `skipped`，备注写理由，继续扫描。
   - 若第一个非 `done` 批次是 `blocked`：停止派发，把原因交给用户。
   - 取第一个 `pending` 的批次；没有 `pending` 且没有 `in-progress` 时，把阶段改为 `done`，继续扫描。
   - 若该批 `acceptance` 含「需用户确认」或 `batches.json` 的 `deletion` 为 `true`，且批次表备注中没有 `已确认 YYYY-MM-DD`：在备注追加 `待用户确认`，停止并请用户确认；确认后改为 `已确认 YYYY-MM-DD`，再把该批改为 `in-progress` 并派发。
5. 第一个非 `done` / `skipped` 的阶段就是当前任务。

迁移批次禁止合并。一个子会话只处理一个批次，批次之间串行。

## 派发

用 `dispatch-templates.md` 的通用模板。五条约束：

- 一个 prompt 只含一个任务，不给子会话看后续阶段。
- 必读清单精确到任务卡路径和参考小节，不用「相关文档」这类模糊说法。
- 写入范围逐条列出；迁移任务的范围核对命令、入站链接例外与结果记录位置见 `reference/acceptance.md`。
- 前置产物给绝对路径或项目内相对路径，不粘贴正文。
- 子会话回报格式由任务卡规定，主会话不另立格式。

不同环境的派发方式、单会话降级方式见 `dispatch-templates.md`；已填示例见 `examples/dispatch-examples.md`。

## 验收

验收标准、派发前基线、范围核对命令与例外见 `reference/acceptance.md`。主会话派发前检查基线，子会话返回后按「产物验收」三项核对；通过后更新 `state.md`，不通过退回。

`recon` 与 `proposal` 的确认点也由该文件的「例外与确认点」约束。

## 并行

| 阶段 | 并行 | 原因 |
|---|---|---|
| `recon` | 否 | 需要全局视图 |
| `proposal` | 否 | 依赖完整现状 |
| `skeleton` | 否 | 会创建同一批层级与模板文件 |
| `migrate` | 否 | 同仓写冲突；死链检查需要完整批次 |
| `checks` | 是 | 每个检查器独立成文件，可分别实现后再统一接入 |
| `audit` | 否 | 需要检查全部完成 |

并行实现检查器时，每个子会话只写自己的脚本、夹具与 `docs-plan/checks/<checkId>.md` 分项报告，不改入口文件，也不写 `checks-report.md` 与 `docs/checking.md`。某个分项失败时，由最后的串行汇总会话把该分项和 `checks-report.md` 的对应行标为失败并单独重试，不影响其他分项。串行会话还必须汇总 `docs-plan/checks-report.md`、写 `docs/checking.md`，统一接入项目现有的测试或 lint 入口，并由主会话合并验收。

## 失败与回退

按 `reference/acceptance.md` 的「失败与回退」处理；状态变更仍由主会话写入 `state.md`。

## 裁剪

档位只影响建议层级与检查默认组合，不改变阶段顺序；档位表见 `reference/thresholds.md` 的「档位组合」。

只有两种情况可以跳过阶段，其余阶段不可跳过：

- `migrate`：`batches.json` 的 `batches` 为空数组（没有存量文档需要迁移）。
- `checks`：用户明确接受不建自动检查；此时 `audit` 交付物第 3 项改为评审清单，并在阶段表备注记录用户决定。评审清单的结构见 `cards/05-audit.md` 第 3 节。

跳过时把阶段改为 `skipped`，并在阶段表「备注」列记录理由。跳过不属于阻塞，不写入阻塞表。

## 任务表

| 任务 ID | 任务卡 | 产出 | 用户确认 | 并行 |
|---|---|---|---|---|
| `recon` | `cards/00-recon.md` | `docs-plan/recon.md` | 否（问答在第 8 节闭环） | 否 |
| `proposal` | `cards/01-proposal.md` | `docs-plan/proposal.md`、`docs-plan/batches.json` | **是** | 否 |
| `proposal-revise` | `cards/01-proposal.md` | 同上（覆盖修订） | **是** | 否 |
| `skeleton` | `cards/02-skeleton.md` | 项目内层级与模板、`docs-plan/skeleton-report.md` | 否 | 否 |
| `migrate` | `cards/03-migrate.md` | 批次内文档、`docs-plan/migrate/<batchId>.md` | 文件级删除时是 | 否 |
| `checks` | `cards/04-checks.md` | 检查脚本、`docs-plan/checks/<checkId>.md`、`docs/checking.md`、现有测试或 lint 入口、`docs-plan/checks-report.md` | 否 | 是（独立检查器；汇总串行） |
| `audit` | `cards/05-audit.md` | `docs-plan/audit-report.md`、交付物五项 | 否 | 否 |

## 文件清单

见 `README.md`。