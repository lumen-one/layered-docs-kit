# 图表：Agent 迁移老项目文档的路径

配套正文见 `agent-migrates-docs.md`。这里只放图，方便单独查看。

## 一、六阶段流程与回退点

```mermaid
flowchart TD
    A["00 现状评估<br/>看入口、目录、模块边界"] --> B["01 架构提案<br/>层级、事实归属、批次"]
    B --> C{"用户确认删除项与冲突事实"}
    C -->|"未确认"| C1["停下来问<br/>不自行拍板"]
    C1 --> C
    C -->|"已确认"| D["留回退点<br/>git tag / 冻结归档 / 映射表"]
    D --> E["02 建层级与规则<br/>只建有内容的层"]
    E --> F["03 分批迁移<br/>B1 入口 → B2 去重 → B3 契约 → B4 决策"]
    F --> G["04 自动检查<br/>先让夹具变红"]
    G --> H["05 审计与交付<br/>五项产物 + 新成员测试"]
    H --> I{"审计发现问题"}
    I -->|"遗漏事实"| J["从 tag 取证<br/>补进新归属页"]
    J --> H
    I -->|"通过"| K["交付"]
```

关键是回退点在迁移之前，不是出问题之后才补。

## 二、单批迁移的内部动作

```mermaid
sequenceDiagram
    participant A as Agent
    participant Old as 旧文档
    participant New as 归属页
    participant Repo as 全仓链接
    participant Report as 批次报告

    A->>Old: 找出本批的重复与过期内容
    A->>New: 确认归属页已有事实
    alt 旧位置有独有细节
        A->>New: 先补细节，从代码核对语义
    end
    A->>Old: 删除重复段，改成一句链接
    A->>Repo: 搜索旧路径，修全部入站链接
    A->>Report: 写范围、删除项、链接修复、回滚点
    Note over A,Report: 先补新家，再删旧家，顺序不能反
```

## 三、回退取证：tag 的正确用法

```mermaid
sequenceDiagram
    participant A as Agent
    participant Tag as tag: docs-pre-migration
    participant New as docs/cookbook/deploy.md
    participant Old as 已删除的旧文件

    Note over A: 审计发现遗漏一条环境变量
    A->>Tag: git show tag:旧文件路径
    Tag-->>A: 返回旧内容
    A->>A: 从代码确认语义
    A->>New: 补进新归属页
    Note over Old: 旧文件不恢复，不双写
    A->>A: 批次报告记一行取证
```

回退点用来查证，不用来把旧目录重新搬回工作区。

## 四、迁移前后的事实归属

```mermaid
flowchart LR
    subgraph Before["迁移前：一个事实多个家"]
        B1["根 README"] --> API1["API 说明"]
        B2["docs/api.md"] --> API1
        B3["auth/README.md"] --> API1
        B4["根 README"] --> DEP1["部署步骤"]
        B5["docs/deploy.md"] --> DEP1
        B6["docs/decisions.md"] --> DEC1["决策与教程混写"]
    end

    subgraph After["迁移后：一个事实一个家"]
        A1["docs/reference/api.md"] --> API2["API 唯一归属"]
        A2["docs/cookbook/deploy.md"] --> DEP2["部署步骤唯一归属"]
        A3["docs/decisions/"] --> DEC2["决策按状态检索"]
        A4["各模块 README"] --> MOD["配置与限制"]
        A5["docs/architecture.md"] --> ARCH["职责与边界"]
    end

    Before -.->|"分批迁移 + 修链接"| After
```

迁移不是把内容搬一遍，是把每条事实的归属重新定一次。