# 参考：决策记录

> 被 `01-proposal`、`03-migrate`、`05-audit` 引用。子会话只读任务卡点名的小节。

## 路径与状态

不建中央索引，状态编码在路径里：

```text
<决策记录根>/{lifecycle}/{class}/yyyy-mm-dd-topic-title.md
```

- `lifecycle`：`proposed` / `implemented` / `rejected` / `archived`。
- `class` 取闭集：`feature` / `bug-fix` / `simplification` / `architecture` / `process` / `testing`。新增类别必须同时更新检查器与本规范。
- 日期取「首次提出」日期，取不到时以首个提交为准。
- `archived` 只接收已实现记录，路径不带 `implemented` 层，即 `archived/{class}/yyyy-mm-dd-topic-title.md`；其 `Status` 仍为 `implemented`，另带 `Archived: YYYY-MM-DD`。
- 不建 `INDEX.md`：路径已是索引，再建一份只会重复路径信息并制造无关冲突。

状态与目录的映射固定为：`proposed/` → `proposed`，`implemented/` → `implemented`，`rejected/` → `rejected`，`archived/` → `implemented` 加 `Archived:` 行。检查器按这张映射表交叉核对，不要求目录名等于状态名。

## 文件结构

文件头三行固定：

```markdown
# 决策记录：<标题>

Status: <状态>
```

状态取值：`proposed`、`implemented`、`rejected — <一行原因>`。状态行不带日期，日期由文件名承载。只有 `rejected` 的状态行带原因，因为「为什么被否」是读者来这里要找的事实。

正文以 `## Problem` 开头，动机要能脱离方案读懂。之后按生命周期取结构：

```markdown
# proposed/
## Problem
## Proposal
（按需的专题章节）
## Alternatives considered
## Acceptance criteria
## Risks

# implemented/
## Problem
## Decision
（按需的专题章节）
## Alternatives considered
## Consequences

# rejected/
## Problem
## Proposal
## Alternatives considered
```

`implemented` 的 `## Decision` 用现在时描述已交付的事实；`## Proposal`、`## Plan`、`## Migration plan`、`## Acceptance criteria` 出现在 `implemented` 中即违规。

`## Alternatives considered` 是必填项：每条真实备选方案一段，加粗开头点明方案，随后说明它为什么落选。备选方案只能记录，不能事后编造。无法从记录中恢复的早期文件，用一行注释说明缺失，不补写。

## 何时写

只有长期决策理由——代码、测试与现有文档都解释不了的部分——才写。同一决定已有归属记录时更新它，不新建重复记录。

机械性改动、局部 UI 或交互调整不写决策记录。纯粹的重命名、移动、格式化不写。

## 维护与归档

- 已实现记录随代码事实更新：代码移动文件、重命名包、改键名或默认值时，同一次变更内更新记录中的路径、名称与结构。只改事实，不改决定。
- 一条记录不改写成另一个决定；被取代时新建记录并双向链接，旧记录保持原样。
- 一条记录被完全取代、不再承载任何独有理由时，可并入当前归属记录后删除。删除前必须保留每一条独有理由、备选方案、代价、验证方式与已知覆盖缺口，修复所有入站链接，并同时删除中文对照与一致性记录。
- 归档只对已实现记录执行，条件是决定已完成、理由不太可能再指导未来工作、但仍有历史价值。归档移动完整三件套，保留 `Status: implemented`，在其下插入 `Archived: YYYY-MM-DD`，重录一致性记录，修复入站链接；这些是归档过程中唯一允许的内容改动。
- 归档后永久冻结：不编辑、不翻译、不重排、不更新、不移动、不删除，也不作为当前行为依据。检查器跳过归档区的格式、链接与双语检查；唯一例外是下面的冻结清单哈希校验。活跃文档可以出于引用历史的目的链接进去。
- 归档时更新冻结清单 `<决策记录根>/archived/.frozen.json`：键为相对归档根的路径，值为该文件内容哈希，三件套各记一条。此后只做哈希比对，不重新生成、不修复链接。

## 检查要点

决策记录格式检查必须覆盖：

- 路径只有合法 `lifecycle` 与 `class`。
- `Status` 与目录按上方映射表一致；`rejected` 带一行原因，其余不带。
- 文件头三行格式正确。
- 对应生命周期的必备章节齐全且顺序正确。
- `implemented` 中不出现提案期章节。
- `archived` 目录下的记录带 `Archived:` 行，且内容哈希与 `.frozen.json` 一致。
