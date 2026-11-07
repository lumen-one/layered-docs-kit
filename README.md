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
  reference/                  迁移参考：分层、决策记录、双语、检查规则
  state-template.md           迁移进度表模板

docs-authoring-kit/
  SKILL.md                    撰写套件入口：触发、档位、四阶段、边界
  cards/                      定契约、搭骨架、日常写、自查与升级
  reference/                  规则、层级裁剪、模板、时点流程图

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

## 开发

任务卡与 `reference/` 是方法论正文。修改后保持 `SKILL.md` 的引导型写法，不把流程锁死。每个套件在自己目录内是唯一真值，不再维护分发副本。

## 发布记录

- 华为云 Skills 市场：`submit_id 3417`
- 小红书 Skill 市场：`skill_id 104183`，`version_id 111191`，审核中

以上记录对应重构前的根目录包。重新发布时从对应 Skill 子目录打包。