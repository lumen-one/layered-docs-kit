# 文档套件

本仓库包含两个可独立安装的 Skill，覆盖文档生命周期的两端：

- **迁移套件**（`docs-management-kit/`）：已有项目文档零散、重复、过期时，整理成可导航、可维护的体系。
- **撰写套件**（`docs-authoring-kit/`）：项目从零开始时，先定最小文档契约，让文档跟着代码生长。

两个 Skill 各自自包含，互不依赖；共享的只是分层与写作思路，不共享文件。

## 结构

```text
docs-management-kit/
  SKILL.md                    迁移套件入口：触发、档位、六阶段、边界、派发
  cards/                      迁移任务卡 00–05
  reference/                  迁移参考：分层、决策记录、双语、检查规则、规则演化
  state-template.md           迁移进度表模板

docs-authoring-kit/
  SKILL.md                    撰写套件入口：触发、档位、四阶段、边界
  cards/                      定契约、搭骨架、日常写、自查与升级
  reference/                  规则、层级裁剪、模板、时点与规则演化流程图

simulations/
  agent-reads-docs.md         效果演示：Agent 只靠建好的文档完成一次真实改动
  agent-reads-docs-flow.md    配套图表：读取路由、读写时序、断点对比
  agent-migrates-docs.md      效果演示：Agent 迁移一个老项目的存量文档
  agent-migrates-docs-flow.md 配套图表：六阶段、单批动作、回退取证

README.md                     本文件
```

## 安装

把对应 Skill 目录整体作为 skill 目录放进 Agent 的 skills 路径，或从市场获取：

- 迁移套件：`docs-management-kit/`。华为云 Skills 市场 `docs-management-kit`；小红书 Skill 市场 `文档管理套件`（skill_id `104183`）。
- 撰写套件：`docs-authoring-kit/`。

打包时进入对应目录，确保 `SKILL.md` 位于包根。两个目录不要打成一个包。

## 使用

- 对 Agent 说“整理这个项目的文档”“文档太乱了”，触发迁移套件。Agent 先看现场，再建议快速版、标准版或深度版。
- 对 Agent 说“新项目文档规则怎么定”“从零写文档”“README / 架构文档怎么写”，触发撰写套件。Agent 先问四个问题，再给最小骨架。

## 效果演示

想知道这套 skill 建立或迁移之后的文档实际怎么用、怎么整理，直接看这两组：

**从零建立的文档，下一轮怎么被使用**

- [Agent 读文档的完整推演](simulations/agent-reads-docs.md)：固定一个样例项目，让 Agent 在不重新加载 skill 的情况下完成一次“加重试配置 + 改退避策略”的真实改动，逐步记录它读了什么、据此做了什么判断、最后同步了哪几处文档。
- [配套图表](simulations/agent-reads-docs-flow.md)：读取路由、一次功能开发的读写时序、极简档断点对比、目录移动的链接修复范围。

**老项目的存量文档，怎么迁移**

- [Agent 迁移文档的完整推演](simulations/agent-migrates-docs.md)：固定一个重复、过期、缺入口的样例项目，按六阶段走完迁移，包含打 tag 留回退点、分批验收、用旧文档补齐遗漏事实的过程。
- [配套图表](simulations/agent-migrates-docs-flow.md)：六阶段流程、单批迁移动作、tag 取证时序、迁移前后的事实归属对比。

这两组推演也是效果验收的依据：文档建得好不好，不看写得多全，看 Agent 不加载 skill 时能不能走对；迁移做得好不好，不看改了多少文件，看每条事实是不是只有一个家。`simulations/` 只用于效果演示，不参与两个 Skill 的打包。

## 开发

任务卡与 `reference/` 是方法论正文。修改后保持 `SKILL.md` 的引导型写法，不把流程锁死。每个套件在自己目录内是唯一真值，不再维护分发副本。

## 发布记录

- 华为云 Skills 市场：`submit_id 3417`
- 小红书 Skill 市场：`skill_id 104183`，`version_id 111191`，审核中

以上记录对应重构前的根目录包。重新发布时从对应 Skill 子目录打包。