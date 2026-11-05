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

## 写入范围
- <逐条列出允许写入的路径>

## 本批次
<仅 migrate 填写：ID、标题、files、acceptance；其他任务写「无」>

## 完成
1. 按任务卡步骤执行。
2. 对照任务卡「验收」逐条自检，未通过的不要隐藏。
3. 回报：产物路径、自检结果、未决问题。

## 禁止
- 读取或修改本任务之外的阶段产物。
- 修改写入范围之外的项目文件。
- 用「大致完成」替代验收项。
```

## 填充规则

| 任务 | 任务卡 | 必读参考 | 前置产物 |
|---|---|---|---|
| `recon` | `cards/00-recon.md` | `reference/layers.md`（作用域、分层模型） | 无 |
| `proposal` | `cards/01-proposal.md` | `reference/layers.md`（全部）、`decisions.md`（路径与状态、文件结构）、`bilingual.md`（三件套、范围、排除项）、`checks.md`（默认组合） | `docs-plan/recon.md` |
| `skeleton` | `cards/02-skeleton.md` | `reference/layers.md`（作用域、指令文件分层、模块 README 契约） | `proposal.md`、`batches.json` |
| `migrate:<id>` | `cards/03-migrate.md` | `reference/layers.md`（一个事实一个家、写作规则、维护规则表） | `proposal.md` 的事实归属表、本批 `files` |
| `checks` | `cards/04-checks.md` | `reference/checks.md`（全部） | `skeleton-report.md`、提案第 5 节 |
| `audit` | `cards/05-audit.md` | 四份参考的指定小节 | 全部 `docs-plan/` 产物 |

## 示例：侦察任务

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
- 参考：C:\kit\docs-management-kit\reference\layers.md 的「一个事实一个家」「写作规则」「维护规则表」
- 前置产物：docs-plan/proposal.md 的事实归属表

## 写入范围
- docs/architecture.md
- packages/core/README.md
- docs-plan/migrate/core-architecture.md

## 本批次
- ID：core-architecture
- 标题：迁移架构页与核心模块 README
- 文件：docs/architecture.md、packages/core/README.md
- 批次验收：死链检查通过；每个删除项有替代位置

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

用工具本身的任务调用方式，把上面的通用模板作为 prompt。能给子代理独立上下文时，不要把它塞进主会话继续做。一次只发一个任务；`checks` 阶段可以并发发多个独立检查器。

### 只能开新会话

新开一个会话，第一句话就是通用模板。会话结束前要求它回报产物路径与自检结果，主会话据此更新 `state.md`。不要在新会话里追加「顺便把下一步也做了」。

### 只能单会话

主会话按 `state.md` 的顺序自己执行任务卡。每到任务边界，先按「验收」自检并更新状态，再进入下一个任务。上下文接近上限时，把当前任务的产物与未决问题写进 `docs-plan/`，新会话只读 `state.md` 与对应产物继续。

### 能执行脚本但没有子代理

可以让主会话生成一个状态读写脚本，但脚本不得承担派发。最低命令集：

```text
status            打印阶段表与批次表
next              输出当前任务的卡路径、必读、写入范围、批次定义
done <taskId>     更新状态，校验产物存在
block <taskId>    标记阻塞并记录原因
unblock <taskId>  解除阻塞
skip <taskId>     标记跳过
approve proposal  记录用户确认
validate          检查已完成阶段的产物是否存在
```

生成规则：

- 只读写 `docs-plan/state.md` 或同目录的 json，不调用任何 Agent API。
- 语言按项目现有工具链选，Node、Python、Bash 均可；不要引入新运行时。
- `next` 只输出结构化信息，不拼自然语言 prompt；prompt 仍由主会话按通用模板生成。
- 生成后先跑 `status` 确认能读到项目根与阶段表，再使用。

## 主会话验收

子会话返回后按顺序检查：

1. 产物路径存在，且包含任务卡「产物结构」列出的必需小节。缺一节即退回。
2. `git status --short` 的改动都在写入范围内。迁移批次额外执行 `git diff --name-only`，逐行核对 `files`。
3. 回报中的未决问题为空，或已进入 `docs-plan/blockers.md` 并标记 `blocked`。

三项都通过才更新 `state.md`。不要凭子会话的「已完成」直接改状态。
