# 模拟：Agent 迁移一个老项目的文档

这份文档按迁移套件的六阶段，完整推演一次存量文档整理：从看现场、给方案，到留回退点、分批迁移、接检查、做审计。重点是展示每个阶段 Agent 实际读什么、判断什么、留下什么产物，而不是复述任务卡。

样例项目叫 `legacy-shop`，一个跑了三年的 Node 单仓电商后端。图表单独放在 `agent-migrates-docs-flow.md`。

## 一、场景固定

迁移前的文档现状：

```text
legacy-shop/
  README.md                    很长，安装、部署、API、历史、未来计划混在一起
  docs/
    api.md                     REST API 参考，缺错误码
    deploy.md                  部署步骤里夹着设计论证
    guide.md                   教程，和 README 重复
    decisions.md               决策和教程混写
    notes/
      old-readme.md            2024 年的旧入口，仍被 README 链接
      migration-2024.md        上一次迁移的临时记录
  packages/
    auth/README.md             七节齐全，但 API 片段和 docs/api.md 重复
    orders/README.md           过时，还在写 v1 接口，代码已是 v2
    payments/README.md         缺配置表
    shipping/                  有对外契约，但没有 README
```

Agent 先不读全文，只扫入口和目录，得到一张问题清单：

| 问题 | 证据 | 影响 |
|---|---|---|
| 同一事实多处定义 | API 说明同时出现在根 README、`docs/api.md`、`auth/README.md` | 改一处必留两处过期 |
| 主文档有过期状态 | `orders/README.md` 写 v1；根 README 有「未来将支持」 | 新人和 Agent 会按旧行为开发 |
| 入口不唯一 | 根 README 链到 6 个位置，其中 `notes/old-readme.md` 是旧入口 | 读者不知道从哪进 |
| 决策与教程混写 | `decisions.md` 把取舍和操作步骤放一起 | 决策无法按生命周期检索 |
| 缺模块契约 | `shipping/` 有对外接口但没有 README | 集成方只能读代码 |
| 无维护规则 | 改配置项不知道要同步哪份文档 | 下次改动继续欠账 |

## 二、00 现状评估：先看现场

Agent 读的东西很少：根 README、`docs/` 目录树、四个包的入口文件、构建与测试入口。不读每个文件全文，避免一上来陷进去。

产出 `docs-plan/recon.md`，结论是：

- 读者：内部 5 人，加 3 家外部集成方。
- 代码边界：`auth`、`orders`、`payments`、`shipping` 四个包，边界清楚，适合按模块建契约。
- 存量质量：内容不少，但重复和过期集中在 API 与模块 README，适合标准版。
- 档位建议：**标准版**。当前不需要双语，也暂时没有稳定的事故复盘需求，L6、L7 不建。
- 自动检查：只做两条最容易生效的，死链和模块 README 章节结构。其余留评审。

这一步的关键是**先给结论，不先改文件**。Agent 没有在第 0 阶段删任何东西。

## 三、01 架构提案：先给方案，不动手

Agent 产出 `docs-plan/proposal.md`，把「事实该住哪」先定下来，再排批次。

目标层级：

| 层 | 位置 | 这次建不建 |
|---|---|---|
| L0 根指令 | `AGENTS.md` | 建 |
| L1 项目地图 | `docs/architecture.md` | 建 |
| L2 模块契约 | 四个模块 `README.md` | 建 |
| L3 参考手册 | `docs/reference/api.md` | 建，由 `docs/api.md` 升级 |
| L4 操作手册 | `docs/cookbook/deploy.md` | 建，由 `docs/deploy.md` 拆出 |
| L5 决策记录 | `docs/decisions/` | 建，由 `docs/decisions.md` 拆分 |
| L6 事故复盘 | `docs/postmortems/` | 不建，没有真实事故 |
| L7 评审规则 | `docs/patterns/` | 不建，团队还没有固定评审流程 |
| 双语 | 按语言方案 | 不建 |

事实归属表是提案的核心：

| 事实 | 归属页 | 其他位置 |
|---|---|---|
| API 路径、字段、错误码 | `docs/reference/api.md` | 只放链接 |
| 配置项与默认值 | 各模块 README 配置表 | 只放链接 |
| 部署步骤 | `docs/cookbook/deploy.md` | 只放链接 |
| 长期取舍 | `docs/decisions/` | 主文档不写历史 |
| 模块边界与调用链 | `docs/architecture.md` | 模块 README 不复述别的模块 |

批次划分：

| 批次 | 范围 | 验收 |
|---|---|---|
| B1 | 根 README、`AGENTS.md`、`docs/architecture.md` | 从 README 能到架构页和四个模块 |
| B2 | `docs/api.md` → `docs/reference/api.md`，清三处重复 | API 只有一个归属页，链接全通 |
| B3 | 四个模块 README | 契约齐全，配置表有默认值 |
| B4 | `decisions.md` 拆分、`deploy.md` 拆分、`guide.md` 合并 | 决策可按状态检索，教程不重复 |
| B5 | 检查脚本与审计 | 两条检查能拦住真实错误 |

Agent 在这里停下来向用户确认了两件事：`notes/old-readme.md` 和 `guide.md` 是否删除，以及 `orders` README 以代码的 v2 为准还是以文档的 v1 为准。没有自行拍板。

## 四、迁移前：先留回退点

用户确认后，Agent 先打回退点，再动第一个文件：

```sh
git tag docs-pre-migration-2026-11-07
```

同时写了一张迁移映射，放进 `docs-plan/migration-map.md`：

| 旧位置 | 动作 | 新位置 |
|---|---|---|
| 根 README 的 API 段 | 移出 | `docs/reference/api.md` |
| 根 README 的部署段 | 移出 | `docs/cookbook/deploy.md` |
| 根 README 的历史段 | 移出 | `docs/decisions/` |
| `docs/api.md` | 升级 | `docs/reference/api.md` |
| `docs/deploy.md` | 拆分 | 步骤进 cookbook，理由进 decisions |
| `docs/decisions.md` | 拆分 | `docs/decisions/` 三条记录 |
| `docs/guide.md` | 合并后删除 | 根 README 快速开始 |
| `docs/notes/old-readme.md` | 删除 | 根 README（内容已并入） |

没有复制整个 `docs/` 做备份。旧文档在 tag 里，`git show` 随时能取；工作区保持干净，Agent 下次不会扫到第二份事实源。

## 五、02 建层级与规则

Agent 只建当前有内容的层，不预建空目录：

- `docs/reference/`、`docs/cookbook/`、`docs/decisions/` 先建目录和说明页，不放空记录。
- `docs/architecture.md` 写一页：四个包的职责、边界、主调用链。
- `AGENTS.md` 文档段写一屏：入口、硬规则、维护规则表、触发点。
- 模板只落这次真正会用的三个：模块 README、参考页、决策记录。

导航验证：从根 README 出发，能到架构页和四个模块 README，链接全部可点。此时内容还没迁完，但骨架已经能走通。

这一步最容易犯的错是「顺手把完整分层补齐」。Agent 没有建 `postmortems/`、`patterns/`、双语目录，因为项目里没有对应对象。

## 六、03 逐批迁移

每批只改本批文件，批结束写 `docs-plan/migrate/<批次>.md`，包含范围、删除项、链接修复、检查结果、回滚点。

### B1：入口与架构

- 根 README 压到一屏：项目是什么、快速开始、四条文档链接。
- API、部署、历史三段移出，原位置留一句链接。
- `docs/notes/old-readme.md` 的内容并入 README 后删除，删前已问过用户。
- 新建 `docs/architecture.md`，只写职责和调用关系，不写字段。
- 修所有指向旧入口的链接。

验收：从 README 能到架构页和四个模块，旧入口不再被引用。

### B2：API 去重

这一批暴露了迁移最典型的判断点。Agent 准备删 `auth/README.md` 里的 API 片段时，发现它比 `docs/api.md` 多一个 `rateLimit` 字段的说明，而代码里确实有这个字段。

处理顺序不是「先删旧的」，而是：

1. 以 `docs/api.md` 为归属页；
2. 把 `auth/README.md` 里独有的 `rateLimit` 说明补进归属页，从代码确认语义；
3. 补完再删 `auth/README.md` 的重复段，改成一句链接；
4. 全仓搜 `docs/api.md` 和 `auth/README.md#api`，修入站链接。

验收：API 事实只在一个地方，`rateLimit` 没有丢。

### B3：模块契约

- `orders/README.md` 按代码的 v2 重写，旧 v1 行为进决策记录，不留在主文档。
- `payments/README.md` 补配置表，默认值从 config schema 读，不凭印象写。
- `shipping/README.md` 新建，只写 Summary、快速开始、配置与限制三节；已知限制里有一条写「待确认」。
- `auth/README.md` 保留七节，API 段改成链接。

每改一个配置项，都对照维护规则表确认只改一个归属页。

### B4：决策与手册

- `docs/decisions.md` 拆成三条记录：存储选型、token TTL、支付重试。每条带 Status 和 Alternatives。
- `docs/deploy.md` 拆开：编号步骤进 `docs/cookbook/deploy.md`，设计理由链接到对应决策记录。
- `docs/guide.md` 与 README 快速开始合并，删除前问过用户。
- 删除 `docs/notes/migration-2024.md`，内容已过期。

验收：决策记录能按路径和状态找到，部署页只有步骤，不夹论证。

## 七、04 自动检查

Agent 没有一上来写检查器，而是先写两个应当失败的夹具：

- 一个页面链接到不存在的 `../reference/old-api.md`；
- 一个模块 README 缺 `Known Limitations` 一节。

然后实现两条检查：

1. 相对链接与锚点：目标文件存在，fragment 能命中标题。
2. 模块 README 章节结构：前五节齐且顺序正确，中英文标题都能识别。

先跑夹具，确认两条都变红，再修掉夹具里的问题，确认变绿。最后接进项目已有的 `npm test`，不新增命令。

Agent 没有把「Summary 是否写清读者能做什么」做成脚本，这类判断留给评审。

## 八、05 审计与交付

审计产出 `docs-plan/audit-report.md`，五项交付物都有路径：

1. 文档架构说明：`docs/architecture.md`
2. 迁移报告：`docs-plan/migrate/` 四个批次报告汇总
3. 检查使用说明：`docs/checks.md`
4. 维护规则表：`AGENTS.md` 文档段
5. 遗留问题清单：审计报告第 4 节

新成员测试只读三页：根 README、`docs/architecture.md`、`docs/reference/api.md`。记录的实际结果是：能跑起来，能找到 API，能按 cookbook 部署；卡点是 `shipping` 的已知限制还没定，已标「待确认」。

遗留问题：双语不做、L7 不做、`shipping` 的失败模式待确认。每条都写了不做的原因和重新评估的条件。

规则结算：迁移期间记了两条摩擦，一条是模块 README 中英文标题别名怎么判定，一条是决策记录路径里的日期格式。第一条出现两次，升级成检查项的允许别名；第二条只出现一次，保留观察。

## 九、一次真实取证：tag 怎么救回一条遗漏事实

B4 删除 `docs/notes/migration-2024.md` 后，审计时发现部署页少了一个环境变量 `SHIPPING_WEBHOOK_SECRET`，而它只在被删的那份旧记录里出现过。

Agent 没有恢复整个旧目录，也没有把旧文件重新放回工作区，而是从 tag 取证：

```sh
git show docs-pre-migration-2026-11-07:docs/notes/migration-2024.md
```

确认语义后，把这条环境变量补进 `docs/cookbook/deploy.md` 的环境变量表，并在批次报告的回滚节记一行：

> 回退点：`docs-pre-migration-2026-11-07`
> 取证：`docs/notes/migration-2024.md`
> 处置：补进 `docs/cookbook/deploy.md`，旧文件不恢复

这就是回退点的正确用法：**它是取证工具，不是第二份文档。** 找到遗漏事实后，事实进新家，旧文件继续留在 tag 里。

## 十、结论

迁移和上次「Agent 读文档」的模拟是一组对照：

| | 读文档 | 迁移文档 |
|---|---|---|
| 核心动作 | 按任务找事实 | 判断事实该住哪 |
| 最大风险 | 读到过期事实 | 删掉唯一副本 |
| 关键约束 | 入口可达 | 一个事实一个家 |
| 回退点作用 | 不需要 | 取证，不是回滚整批 |

三条判断：

1. **迁移的难点不是写新文档，是判断旧位置有没有新家没有的细节。** B2 的 `rateLimit` 和第九节的 `SHIPPING_WEBHOOK_SECRET` 都是这一类。所以顺序必须是「先补新家，再删旧家」。
2. **批次小、每批可验收，比一次性重写安全。** 每个批次都有范围、删除项、链接修复和回滚点，出问题只影响一批。
3. **回退点的价值在于让人敢删。** 没有 tag，Agent 会倾向于保守保留旧文件，结果迁移做不干净；有了 tag，删除变成可逆动作，反而更彻底。

迁移完成的标志不是「文档都改完了」，是这三件事成立：从少数入口能找到需要的事实；同一事实只有一个归属页；下一个人改代码时知道该同步哪份文档。