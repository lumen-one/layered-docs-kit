# 派发模板

> 主会话读本文件；子会话不需要读。

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
- 参考：<reference/xxx.md 的「小节」>
- 前置产物：<docs-plan/xxx.md，绝对路径或项目内相对路径>

## 用户答案
<仅 recon 填写：档位与 7 个问题的用户答案；其他任务写「无」>

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
- 用「大致完成」替代验收项。
```

## 填充规则

| 任务 | 任务卡 | 必读参考 | 前置产物 | 写入范围 |
|---|---|---|---|---|
| `recon` | `cards/00-recon.md` | `reference/layers.md`（作用域、分层模型） | 无 | `docs-plan/recon.md` |
| `proposal` | `cards/01-proposal.md` | `reference/layers.md`（全部）、`decisions.md`（路径与状态、文件结构、检查要点）、`bilingual.md`（三件套、配对范围、排除项、检查要点）、`checks.md`（默认组合、检查项） | `docs-plan/recon.md` | `docs-plan/proposal.md`、`docs-plan/batches.json` |
| `skeleton` | `cards/02-skeleton.md` | `reference/layers.md`（作用域、模块 README 契约）、`decisions.md`（路径与状态、文件结构）、`checks.md`（检查项 3、4、5） | `proposal.md`、`batches.json` | 提案第 1、3 节列出的指令文件、层级目录、模板、决策记录目录；`docs-plan/skeleton-report.md` |
| `migrate:<id>` | `cards/03-migrate.md` | `reference/layers.md`（一个事实一个家、写作规则、维护规则表）、`checks.md`（检查项 1、2、3、5） | `proposal.md` 第 4 节事实归属表、本批 `files` | 本批 `files`、`docs-plan/migrate/<batchId>.md`、报告第 3 节列出的入站链接源文件（这些文件只允许改链接目标） |
| `checks` | `cards/04-checks.md` | `reference/checks.md`（全部） | `state.md` 的档位、`skeleton-report.md`、`proposal.md` 第 5 节 | 提案第 5 节列出的检查脚本与夹具目录、现有入口文件、`docs-plan/checks/<checkId>.md`、`docs-plan/checks-report.md` |
| `audit` | `cards/05-audit.md` | `layers.md`（写作规则、维护规则表、审计清单）、`decisions.md`（维护与归档）、`bilingual.md`（检查要点）、`checks.md`（扫描范围与排除、检查项） | 全部已产生的 `docs-plan/` 产物 | `docs-plan/audit-report.md`、交付物五项、维护规则表所在指令文件 |

## 示例：侦察任务

示例路径为 Windows 写法；其他平台替换为对应的绝对路径。

```text
你是「文档体系建立」的子会话，任务 recon。

## 项目根
D:\work\my-project

## 任务卡
C:\kit\docs-management-kit\cards\00-recon.md

## 必读
- 任务卡：C:\kit\docs-management-kit\cards\00-recon.md
- 参考：C:\kit\docs-management-kit\reference\layers.md 的「作用域」「分层模型」
- 前置产物：无

## 用户答案
- 档位：medium
- 项目规模：单包
- 读者：内部开发者
- 多语言：否
- 对外文档站：无
- CI：GitHub Actions + pnpm
- 覆盖范围：全项目
- 文档所有者：架构组；子树无单独所有者

## 写入范围
- docs-plan/recon.md

## 本批次
无

## 完成
1. 按任务卡步骤执行。
2. 对照任务卡「验收」逐条自检，未通过的不要隐藏。
3. 回报：产物路径、自检结果、未决问题。

## 禁止
- 读取或修改本任务之外的阶段产物。
- 修改写入范围之外的项目文件。
- 用「大致完成」替代验收项。
```

## 示例：迁移批次

```text
你是「文档体系建立」的子会话，任务 migrate:core-architecture。

## 项目根
D:\work\my-project

## 任务卡
C:\kit\docs-management-kit\cards\03-migrate.md

## 必读
- 任务卡：C:\kit\docs-management-kit\cards\03-migrate.md
- 参考：C:\kit\docs-management-kit\reference\layers.md 的「一个事实一个家」「写作规则」「维护规则表」；C:\kit\docs-management-kit\reference\checks.md 的检查项 1、2、3、5
- 前置产物：docs-plan/proposal.md 的事实归属表

## 用户答案
无

## 写入范围
- docs/architecture.md
- packages/core/README.md
- README.md（仅修复指向本批文件的入站链接）
- docs-plan/migrate/core-architecture.md

## 本批次
- ID：core-architecture
- 标题：迁移架构页与核心模块 README
- 文件：docs/architecture.md、packages/core/README.md
- 批次验收：死链按判定方式人工核对通过（checks 阶段复跑）；每个删除项有替代位置
- 删除类（deletion）：false
- 确认记录：不适用（deletion=false）

## 完成
1. 按任务卡步骤执行。
2. 对照任务卡「验收」逐条自检，未通过的不要隐藏。
3. 回报：产物路径、自检结果、未决问题。

## 禁止
- 读取或修改本任务之外的阶段产物。
- 修改写入范围之外的项目文件。
- 用「大致完成」替代验收项。
```

## 不同环境怎么派发

### 有子代理工具

用工具本身的任务调用方式，把上面的通用模板作为 prompt。能给子代理独立上下文时，不要把它塞进主会话继续做。一次只发一个任务；`checks` 阶段可以并发发多个独立检查器，并发时每个子会话只写自己的脚本、夹具与 `docs-plan/checks/<checkId>.md` 分项报告，不改入口文件，入口由最后的串行会话统一接入并汇总 `checks-report.md`。

### 只能开新会话

新开一个会话，第一句话就是通用模板。会话结束前要求它回报产物路径与自检结果，主会话据此更新 `state.md`。不要在新会话里追加「顺便把下一步也做了」。

### 只能单会话

主会话按 `state.md` 的顺序自己执行任务卡。每到任务边界，先按「验收」自检并更新状态，再进入下一个任务。每完成一个任务，请用户提交该任务产物；用户明确授权时主会话提交。上下文接近上限时，把当前任务的产物与未决问题写进 `docs-plan/`，新会话只读 `state.md` 与对应产物继续。

### 能执行脚本但没有子代理

可以让主会话生成一个状态读写脚本，但脚本不得承担派发。最低命令集：

```text
status                打印阶段表与批次表
next                  输出当前任务的卡路径、必读、写入范围、批次定义
done <taskId>         更新状态，校验产物存在
confirm-batch <id>    记录删除类批次的用户确认
block <taskId>        标记阻塞并记录原因
unblock <taskId>      解除阻塞
skip <taskId>         标记跳过（仅 migrate 与 checks 可用）
approve proposal      记录用户确认
reject proposal       记录否决并标记待修订
validate              检查已完成阶段的产物是否存在
```

生成规则：

- 只读写 `docs-plan/state.md` 或同目录的 json，不调用任何 Agent API。
- `confirm-batch` 只把批次表对应行的备注改为 `已确认 YYYY-MM-DD`。
- `approve proposal` 把待用户确认表对应行改为 `已确认 YYYY-MM-DD`，并把 `proposal` 阶段改为 `done`。
- `reject proposal` 把该行改为 `待修订`，并把 `proposal` 阶段改为 `in-progress`。
- 语言按项目现有工具链选，Node、Python、Bash 均可；不要引入新运行时。
- `next` 只输出结构化信息，不拼自然语言 prompt；prompt 仍由主会话按通用模板生成。
- 生成后先跑 `status` 确认能读到项目根与阶段表，再使用。

## 主会话验收

派发前先确认 `git status --short` 为空；不干净时不派发。子会话返回后按顺序检查：

1. 产物路径存在，且包含任务卡「产物结构」「报告结构」或「提案结构」列出的必需小节。缺一节即退回。
2. `git status --short` 的改动都在写入范围内（含未跟踪的新建文件）。迁移批次的范围是：本批 `files`、`docs-plan/migrate/<batchId>.md`、报告第 3 节列出的入站链接源文件（仅允许改链接目标）。先执行 `git diff --name-only` 核对已跟踪文件，再用 `git ls-files --others --exclude-standard` 补查新建文件。
3. 回报中的未决问题为空，或已由主会话写入 `docs-plan/state.md` 的「阻塞」表并标记 `blocked`。

三项都通过才更新 `state.md`。不要凭子会话的「已完成」直接改状态。验收通过后请用户提交本任务产物（用户明确授权时主会话提交）；未提交不得派发下一个任务。