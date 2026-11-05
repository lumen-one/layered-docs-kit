# 文档管理套件

两套提示词，按运行环境选择：

- `INDEX.md`：拆解版，主会话 + 子会话，适合能派发子代理、文档较多的项目。
- `documentation-management-prompt.md`：单体版，单会话一次执行；通常位于套件同级目录，未随包分发时不可用；分发到其他位置时以实际路径为准。

两套同时适用时以拆解版为准；拆解版增加了状态文件、批次串行与子会话边界。

## 选择哪一套

- 能派发子代理，待迁移文档较多（默认参考线见 `reference/thresholds.md`，可按项目调整）：用拆解版。
- 能派发子代理，但待迁移文档较少：拆解版仍可用；`batches.json` 为空时跳过 `migrate`。
- 只能单会话且不能执行任何命令（含 git）：用单体版，项目很小也适用。
- 只能单会话但能执行脚本：用拆解版，并按 `dispatch-templates.md` 的「能执行脚本但没有子代理」生成状态脚本。

## 为什么拆

单体版一次会话要连续完成侦察、提案、建层级、迁移、写检查、审计，中途还要等用户确认。实际失败点有三个：上下文被前几个阶段的细节占满，迁移批次和检查实现互相挤占，评审时无法判断某段改动属于哪个阶段。

拆分后每个子会话有明确的前置产物、写入范围和验收标准；主会话只保留进度，不读任务卡正文。总量比单体版多，但单次负载可控。

## 读者

- 执行者：读 `INDEX.md`，按其中引用读 `reference/acceptance.md` 与 `dispatch-templates.md` 的指定小节。
- 分发者与维护者：读本文件。
- 子会话：只读任务卡点名的文件与参考小节。

## 文件清单

同级可选文件：`../documentation-management-prompt.md`（单体版；未随包分发时不可用）。

```text
docs-management-kit/
  README.md                本文件，选型与维护入口
  INDEX.md                 主会话执行协议，每次必读
  state-template.md        复制到项目后作为进度表
  dispatch-templates.md    派发模板、填充规则与环境做法
  cards/
    00-recon.md
    01-proposal.md
    02-skeleton.md
    03-migrate.md
    04-checks.md
    05-audit.md
  reference/
    layers.md              作用域、分层、README 契约、写作规则、维护表
    decisions.md           决策记录全生命周期
    bilingual.md           双语配对
    checks.md              自动检查目录与接入规则
    thresholds.md          档位组合与数量阈值
    acceptance.md          验收、基线、范围核对与回退
  examples/
    dispatch-examples.md   已填好的派发示例
```

## 维护约定

- 同一事实只在一处定义：档位与阈值在 `reference/thresholds.md`，验收与回退在 `reference/acceptance.md`，其余文件引用。
- `state-template.md` 会复制到项目内，使用规则必须自包含；它与 `reference/acceptance.md` 重复的部分，冲突时以 `reference/acceptance.md` 为准。
- 修改单体版与拆解版时，先改拆解版的单一真值，再同步单体版对应段落。