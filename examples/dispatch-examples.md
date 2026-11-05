# 派发示例

> 已填好的完整 prompt，供主会话参照。主会话默认不读本文件；`dispatch-templates.md` 的通用模板与填充规则才是必读内容。

## 侦察任务

示例路径为 Windows 写法；其他平台替换为对应的绝对路径。

```text
你是「文档体系建立」的子会话，任务 recon。

## 项目根
D:\work\my-project

## 任务卡
C:\kit\docs-management-kit\cards\00-recon.md

## 必读
- 任务卡：C:\kit\docs-management-kit\cards\00-recon.md
- 参考：C:\kit\docs-management-kit\reference\layers.md 的「作用域」「分层模型」；C:\kit\docs-management-kit\reference\thresholds.md 的「档位组合」；C:\kit\docs-management-kit\reference\checks.md 的「扫描范围与排除」
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
- 执行 `git add`、`git commit`、`git push` 或改写历史；提交由用户或主会话按协议完成。
- 用「大致完成」替代验收项。
```

## 迁移批次

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
- 执行 `git add`、`git commit`、`git push` 或改写历史；提交由用户或主会话按协议完成。
- 用「大致完成」替代验收项。
```