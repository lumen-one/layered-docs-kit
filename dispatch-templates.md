# 派发模板

> 主会话读本文件；子会话不需要读。

## 启动问答

主会话在首次派发前向用户问齐以下 7 个问题，答案填入通用模板的「用户答案」节；档位默认 `medium`，只能取 `basic` / `medium` / `multilingual` 之一。

1. 项目规模与模块边界（单包、monorepo、多服务）。
2. 文档主要读者：内部开发者、外部用户、AI Agent，还是都要。
3. 是否需要多语言；需要哪些语言。
4. 是否已有对外文档站。
5. 可接入的 CI / pre-commit 体系与语言。
6. 本次覆盖范围：全项目，还是先做一个核心模块试点。
7. 谁是文档所有者，谁批例外与上限调整；子树是否各有所有者。

未答项写入 `docs-plan/state.md` 的「待用户确认」表，由 `00-recon` 第 8 节跟踪。

## 通用模板

把占位符替换后整段交给子会话。结构固定，不要增删小节。

```text
你是「文档体系建立」的子会话，任务 <taskId>。

## 项目根
<绝对路径>

## 任务卡
<任务卡绝对路径>

## 必读
- 任务卡：<同上>
- 参考：<reference/xxx.md 的「小节」；多项时逐条列出>
- 前置产物：<docs-plan/xxx.md，绝对路径或项目内相对路径>

## 用户答案
<recon 填写档位与 7 个问题的用户答案；proposal-revise 填写用户否决意见或 checks 补项要求；其他任务写「无」>

## 写入范围
- <按「填充规则」表的写入范围列逐条展开>

## 本批次
<仅 migrate 填写：ID、标题、文件、批次验收、deletion（true/false）、确认记录（已确认日期 / 不适用）；其他任务写「无」>

## 完成
1. 按任务卡步骤执行。
2. 对照任务卡的「验收」逐条自检，未通过的不要隐藏。
3. 回报：产物路径、自检结果、未决问题。

## 禁止
- 读取或修改本任务之外的阶段产物。
- 修改写入范围之外的项目文件。
- 执行 `git add`、`git commit`、`git push` 或改写历史；提交由用户或主会话按协议完成。
- 用「大致完成」替代验收项。
```

## 填充规则

| 任务 | 任务卡 | 必读参考 | 前置产物 | 写入范围 |
|---|---|---|---|---|
| `recon` | `cards/00-recon.md` | `reference/layers.md`（作用域、分层模型）、`reference/thresholds.md`（档位组合）、`reference/checks.md`（扫描范围与排除） | 无 | `docs-plan/recon.md` |
| `proposal` / `proposal-revise` | `cards/01-proposal.md` | `reference/layers.md`（全部）、`reference/thresholds.md`（档位组合、数量阈值）、`reference/decisions.md`（路径与状态、文件结构、检查要点）、`reference/bilingual.md`（三件套、配对范围、排除项、检查要点）、`reference/checks.md`（检查项） | `docs-plan/recon.md`；修订时加现有 `proposal.md`、`batches.json` | `docs-plan/proposal.md`、`docs-plan/batches.json` |
| `skeleton` | `cards/02-skeleton.md` | `reference/layers.md`（作用域、分层模型、L7 评审规则、模块 README 契约）、`reference/decisions.md`（路径与状态、文件结构、检查要点）、`reference/checks.md`（检查项 3、4、5） | `docs-plan/proposal.md`、`docs-plan/batches.json` | 提案第 1 节列出的 `docs/architecture.md`、层级索引、模板、决策记录目录、L7 评审规则页、每份 `AGENTS.md` 及其 `CLAUDE.md`；`docs-plan/skeleton-report.md` |
| `migrate:<id>` | `cards/03-migrate.md` | `reference/layers.md`（一个事实一个家、写作规则、维护规则表）、`reference/checks.md`（检查项 1、2、3、5）；新建决策记录时加 `reference/decisions.md`（路径与状态、文件结构）；启用双语时加 `reference/bilingual.md`（三件套、配对范围、检查要点） | `docs-plan/proposal.md` 第 4 节事实归属表、本批 `files` | 本批 `files`、`docs-plan/migrate/<batchId>.md`、报告第 3 节列出的入站链接源文件（这些文件只允许改链接目标）；新建决策记录或双语配对文件须在本批 `files` 内，未列入时停止回报，由主会话追加到 `batches.json` 并请用户确认后重派 |
| `checks`（并行分项） | `cards/04-checks.md` | `reference/checks.md`（全部）、`reference/thresholds.md`（档位组合）、`reference/decisions.md`（检查要点）、`reference/bilingual.md`（检查要点） | `state.md` 的档位、`skeleton-report.md`、`proposal.md` 第 5 节 | 提案第 5 节列出的检查脚本与夹具路径、`docs-plan/checks/<checkId>.md`、启用检查 7 时的 `docs/references-allowlist.yaml`；不改入口文件，不写 `checks-report.md` 与 `docs/checking.md` |
| `checks`（串行汇总） | `cards/04-checks.md` | `reference/checks.md`（全部）、`reference/thresholds.md`（档位组合）、`reference/decisions.md`（检查要点）、`reference/bilingual.md`（检查要点）、`proposal.md` 第 5 节 | 全部分项报告 | `docs-plan/checks-report.md`、`docs/checking.md`、`docs-plan/checks/<checkId>.md`（仅改汇总所需的失败标记）、项目现有测试或 lint 入口（按工具链，如 `package.json` scripts、`Makefile`、CI 配置） |
| `audit` | `cards/05-audit.md` | `reference/layers.md`（写作规则、维护规则表、审计清单）、`reference/decisions.md`（维护与归档）、`reference/bilingual.md`（检查要点）、`reference/checks.md`（扫描范围与排除、检查项） | 全部已产生的 `docs-plan/` 产物 | `docs-plan/audit-report.md`、交付物五项、维护规则表所在指令文件 |

`checks` 的两行同属一个阶段：阶段状态只记录一次；并行分项完成后由串行汇总会话收口，并行子会话不写汇总报告、`docs/checking.md` 与入口文件。

`proposal-revise` 复用 `proposal` 的任务卡与写入范围，不新建文件；任务卡收到「用户答案」中的否决意见或 `checks` 补项要求时进入修订模式。

`checks` 的复跑模式：派发时在任务卡后追加「复跑」说明，必读收窄为 `checks.md` 的对应检查项、`state.md` 的阶段备注与受影响批次报告，写入范围只含分项报告与 `checks-report.md` 第 5 节；不得重建检查器或修改入口。

## 示例

已填示例见 `examples/dispatch-examples.md`；主会话默认不读。

## 不同环境怎么派发

### 有子代理工具

用工具本身的任务调用方式，把上面的通用模板作为 prompt。能给子代理独立上下文时，不要把它塞进主会话继续做。一次只发一个任务；`checks` 阶段可以并发发多个独立检查器，并发时每个子会话只写自己的脚本、夹具与 `docs-plan/checks/<checkId>.md` 分项报告，不改入口文件，不写 `checks-report.md` 与 `docs/checking.md`；入口由最后的串行会话统一接入，并汇总 `checks-report.md` 与 `docs/checking.md`。

### 只能开新会话

新开一个会话，第一句话就是通用模板。会话结束前要求它回报产物路径与自检结果，主会话据此更新 `state.md`。不要在新会话里追加「顺便把下一步也做了」。

### 只能单会话

主会话按 `state.md` 的顺序自己执行任务卡。每到任务边界，先按 `reference/acceptance.md` 自检并更新状态，再进入下一个任务。每完成一个任务，请用户提交该任务产物；用户明确授权时主会话提交。上下文接近上限时，把当前任务的产物与未决问题写进 `docs-plan/`，新会话只读 `state.md` 与对应产物继续。

### 能执行脚本但没有子代理

可以让主会话生成一个状态读写脚本，但脚本不得承担派发。最低命令集：

```text
status                打印阶段表与批次表，以及当前档位
next                  输出当前任务的卡路径、必读、写入范围、批次定义
start <taskId>        标记任务 in-progress 并写开始日期
begin-batch <id>      标记批次 in-progress；migrate 阶段同时置 in-progress
complete-batch <id>   标记批次 done，校验批次报告存在；全部批次 done 时 migrate 阶段置 done
done <taskId>         更新状态，校验产物存在；不接受 proposal
confirm-batch <id>    记录删除类批次的用户确认
block <taskId>        标记阶段阻塞并记录原因
unblock <taskId>      解除阶段阻塞
block-batch <id>      标记批次阻塞并记录原因
unblock-batch <id>    解除批次阻塞
skip <taskId> <reason> 标记跳过（仅 migrate 与 checks 可用），原因写入备注
approve proposal      记录用户确认
reject proposal       记录否决并标记待修订
reopen <taskId>       把阶段从 done 改回 in-progress（仅复跑/修复用）
reopen-batch <id>     把批次从 done 改回 pending（仅修复用）
validate              检查已完成阶段的产物是否存在
```

生成规则：

- 只读写 `docs-plan/state.md` 或同目录的 json，不调用任何 Agent API。
- `start` 只接受阶段任务；批次用 `begin-batch`，不要用 `start <batchId>`。
- `block` / `unblock` 只改阶段表；`block-batch` / `unblock-batch` 只改批次表，两者不得互相替代。
- `unblock` / `unblock-batch` 把目标改回 `in-progress`，等待重派；需要回到 `pending` 时由主会话按验收结果手动改。
- `reopen` / `reopen-batch` 仅用于复跑或修复，必须先在 `state.md` 备注写原因。
- `next` 遇到 `proposal` 阶段为 `pending`：确认行为 `待修订` 时输出 `proposal-revise`；确认行为其他未确认值时输出「等待用户确认提案」，不输出派发指令。
- `start` 把阶段任务改为 `in-progress` 并写开始日期；`migrate` 阶段在 `begin-batch` 时同时改为 `in-progress`。
- `complete-batch` 只接受迁移批次，必须校验 `docs-plan/migrate/<batchId>.md` 存在；全部批次为 `done` 时把 `migrate` 阶段置为 `done`。
- `done` 必须校验任务卡声明的产物存在；`proposal` 只能由 `approve proposal` 置为 `done`，`done proposal` 直接报错。
- `confirm-batch` 只把批次表对应行的备注追加为 `已确认 YYYY-MM-DD`。
- `approve proposal` 把待用户确认表对应行改为 `已确认 YYYY-MM-DD`，把 `proposal` 阶段改为 `done`，并按 `docs-plan/batches.json` 展开批次表；`batches` 为空数组时删除占位行并把 `migrate` 置为 `skipped`。
- `reject proposal` 把该行改为 `待修订`，并把 `proposal` 阶段改为 `in-progress`。
- `validate` 只校验 `state.md` 阶段表「产物」列中已 `done` 的路径存在，不读任务卡。
- `skip` 必须带原因参数，写入阶段表备注。
- 语言按项目现有工具链选，Node、Python、Bash 均可；不要引入新运行时。
- `next` 只输出结构化信息，不拼自然语言 prompt；prompt 仍由主会话按通用模板生成。
- 生成后先跑 `status` 确认能读到项目根与阶段表，再使用。

## 主会话验收

验收标准、派发前基线、范围核对与回退见 `reference/acceptance.md`。主会话按该文件执行「派发前基线」「产物验收」「范围核对命令」三节，不另立标准。