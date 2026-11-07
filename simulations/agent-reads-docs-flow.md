# 图表：Agent 读文档的路径

配套正文见 `agent-reads-docs.md`。这里只放图，方便单独查看。

## 一、读取路由：任务关键词决定读哪层

```mermaid
flowchart TD
    A["会话启动"] --> A1["AGENTS.md 文档段<br/>入口 + 硬规则 + 触发点"]
    A1 --> B["根 README<br/>是什么、怎么跑、文档地图"]
    B --> C{"任务落在哪个模块"}
    C -->|"queue / 重试 / 配置"| D["src/queue/README.md<br/>配置表、语义、限制"]
    C -->|"CLI 参数"| E["src/cli/README.md"]
    D --> F{"要动调用链或跨模块吗"}
    F -->|否| G["直接改代码"]
    F -->|是| H["docs/architecture.md<br/>职责与边界"]
    G --> I{"有长期取舍吗"}
    H --> I
    I -->|否| J["只改代码 + 模块文档"]
    I -->|是| K["docs/decisions/README.md<br/>路径与状态"]
    K --> L["翻一条已有记录<br/>确认章节格式"]
    L --> M["新建决策记录"]
    J --> N["提交前五条自查"]
    M --> N
    N --> O{"有规则摩擦吗"}
    O -->|有| P["追加 docs-notes/rule-signals.md"]
    O -->|没有| Q["结束"]
    P --> Q
```

关键点：`architecture` 和 `decisions` 都是条件分支，不是必经节点。层级是按需展开的。

## 二、一次功能开发的读写时序

```mermaid
sequenceDiagram
    participant A as Agent
    participant G as AGENTS.md
    participant R as 根 README
    participant M as queue README
    participant Arch as architecture
    participant Dec as docs/decisions
    participant Code as 代码

    Note over A,G: 会话启动即注入，零成本
    G-->>A: 入口 + 硬规则 + 触发点
    A->>R: 读入口
    R-->>A: 文档地图
    A->>M: 按任务定位模块
    M-->>A: 已有 retry_limit，避免造同义配置
    A->>Arch: 需要确认边界时
    Arch-->>A: 退避属于 queue，不进 store
    A->>Dec: 有长期取舍
    Dec-->>A: 路径格式与状态
    A->>Code: 改配置、重试逻辑、CLI 优先级
    Code-->>A: 三处变更
    A->>M: 回写配置表、语义段、限制段
    A->>Dec: 新增 implemented 记录
    Note over A: 五条自查 → 摩擦记账
```

只有改代码之前需要「先读」，改完之后的回写是触发点驱动的，不需要再检索一遍全文。

## 三、极简档的断点：示例骨架缺触发点

```mermaid
flowchart LR
    subgraph B["B 版：按做法补了触发点"]
        B1["AGENTS.md<br/>5 硬规则 + 触发点"] --> B2["改代码前就知道<br/>配置表 / 语义段 / 决策记录"]
        B2 --> B3["三处同步"]
    end

    subgraph A["A 版：照示例骨架写"]
        A1["AGENTS.md<br/>只有 5 硬规则"] --> A2["知道要同步文档<br/>但不知道具体写哪"]
        A2 --> A3["只改一部分<br/>可能漏决策记录"]
    end
```

A 版不是没有规则，是规则停在「要同步」这一层，没有落到可执行的触发点。极简档又没有维护规则表兜底，断点就出现在这里。

## 四、移动目录时的链接修复范围

```mermaid
flowchart TD
    A["重命名 src/queue → src/worker"] --> B["全仓搜旧路径"]
    B --> C["根 README 文档地图"]
    B --> D["architecture 组成表"]
    B --> E["模块 README 自身位置"]
    B --> F["已实现决策记录里的路径"]
    C --> G["改"]
    D --> G
    E --> G
    F --> H{"是链接还是历史叙述"}
    H -->|链接| I["改，保证可点通"]
    H -->|正文叙述| J["保留或注明现路径<br/>不改写历史"]
```

现在的 skill 把这两类都写成「修路径」，实际执行时需要在「可点通」和「不改历史」之间做一个很小的判断。