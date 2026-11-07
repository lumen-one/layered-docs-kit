# 参考：决策记录

记录长期决策的理由，让后来的人知道「为什么这样选、放弃了什么」。

## 路径与状态

不建中央索引，状态编码在路径里：

```text
<决策记录根>/{lifecycle}/{class}/yyyy-mm-dd-topic-title.md
```

- `lifecycle`：`proposed`、`implemented`、`rejected`、`archived`。
- `class`：`feature`、`bug-fix`、`simplification`、`architecture`、`process`、`testing`。新增类别时同步更新检查规则。
- 日期取首次提出的日期，取不到时用首个提交日期。
- `archived` 只接收已实现记录，路径不带 `implemented` 层，即 `archived/{class}/yyyy-mm-dd-topic-title.md`；`Status` 仍是 `implemented`，另加 `Archived: YYYY-MM-DD`。

之所以用路径而不是索引：路径本身就是索引，再建一份只会重复信息、制造无关冲突。

## 文件结构

文件头三行：

```markdown
# 决策记录：<标题>

Status: <状态>
```

状态取 `proposed`、`implemented`、`rejected — <一行原因>`。状态行不带日期，日期由文件名承载；`rejected` 带原因，其余不带，因为「为什么被否」正是读者来找的事实。

正文以 `## Problem` 开头，动机要能脱离方案读懂。之后按生命周期取结构：

```markdown
# proposed/
## Problem
## Proposal
## Alternatives considered
## Acceptance criteria
## Risks

# implemented/
## Problem
## Decision
## Alternatives considered
## Consequences

# rejected/
## Problem
## Proposal
## Alternatives considered
```

`implemented` 的 `## Decision` 用现在时写已交付的事实；提案期章节（`## Proposal`、`## Plan`、`## Migration plan`、`## Acceptance criteria`）不应出现在已实现记录里。

`## Alternatives considered` 通常要有：每条真实备选方案一段，加粗开头点明方案，再说明为什么落选。只记录事实，不事后编造；恢复不了的早期内容用一行注释说明缺失，不补写。

## 何时写

值得写的是长期决策理由——代码、测试和现有文档都解释不了的部分。同一决定已有归属记录时更新它，不新建重复记录。

机械性改动、局部 UI 调整、纯重命名或格式化不写。

## 维护与归档

- 已实现记录随代码事实更新：文件移动、包重命名、键名或默认值变化时，同一次变更内更新记录里的路径与名称。只改事实，不改决定。
- 一条记录不改写成另一个决定；被取代时新建记录并双向链接，旧记录保持原样。
- 一条记录被完全取代、不再承载独有理由时，可以并入当前归属记录后删除。删除前保留每条独有理由、备选方案、代价和验证方式，修复入站链接。
- 归档只对已实现记录执行：决定已完成、理由不太可能再指导未来工作、但仍有历史价值。归档时保留 `Status: implemented`，在其下加 `Archived: YYYY-MM-DD`，修复入站链接。启用双语时连同对照文件一起移动。
- 归档后永久冻结：不编辑、不翻译、不重排、不移动、不删除，也不作为当前行为依据。归档区旁维护一份冻结清单（路径到内容哈希），之后只做哈希比对；活跃文档可以为了引用历史链接进去。

## 检查思路

路径里的生命周期与类别是否合法；活跃记录的 `Status` 与目录是否对得上；文件头三行是否完整；对应生命周期的章节是否齐且顺序正确；已实现记录里是否混入提案期章节。归档区只比对冻结清单哈希，不重复校验格式与链接，避免为了「修格式」破坏冻结。