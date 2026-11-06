# 文档管理套件

把项目里零散、重复、过期的文档整理成可导航、可持续维护的体系。本仓库本身就是一个可安装的 Skill，入口是 `SKILL.md`。

## 安装

把仓库根目录作为 skill 目录放进 Agent 的 skills 路径，或从市场获取：

- 华为云 Skills 市场：`docs-management-kit`
- 小红书 Skill 市场：`文档管理套件`（skill_id `104183`）

## 使用

直接对 Agent 说“整理这个项目的文档”或“文档太乱了”，`SKILL.md` 的 description 会触发。Agent 先看现场，再建议快速版、标准版或深度版，按任务卡执行。

## 结构

```text
SKILL.md                  Agent 入口：触发、档位、六阶段、边界、派发
cards/                    00–05 任务卡
reference/                分层、决策记录、双语、检查规则
state-template.md         进度表模板
```

## 开发

任务卡与 `reference/` 是方法论正文。修改后保持 `SKILL.md` 的引导型写法，不把流程锁死。根目录就是唯一真值，不再维护分发副本。

## 发布记录

- 华为云 Skills 市场：`submit_id 3417`
- 小红书 Skill 市场：`skill_id 104183`，`version_id 111191`，审核中