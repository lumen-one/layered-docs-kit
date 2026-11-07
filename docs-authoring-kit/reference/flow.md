# 参考：项目各时点的文档动作

把「什么时候写什么」画清楚，避免两种极端：一开始铺满空文档，或者一直不写、最后集中补。

## 项目生命周期

```mermaid
flowchart TD
    A["项目初始化"] --> B["定契约<br/>读者、关键事实、层、语言"]
    B --> C["搭骨架<br/>README、architecture、AGENTS.md"]
    C --> D{"日常开发"}
    D --> E{"这次改动触发文档吗"}
    E -->|否| F["只改代码"]
    E -->|是| G["写或更新对应文档"]
    G --> H["提交前五条自查"]
    F --> H
    H --> D
    D --> I{"出现升级信号"}
    I -->|第二维护者| J["升标准档<br/>模块契约、模板、维护表"]
    I -->|对外发布| K["升完整档<br/>参考页、手册、双语、检查"]
    I -->|反复出同类问题| L["加一条硬规则或检查"]
    J --> D
    K --> D
    L --> D
```

关键点：骨架只建一次，之后进入「改代码 → 判断触发 → 写文档 → 自查」的循环。升级是信号驱动，不是按时间表。

## 一次功能开发的文档时序

```mermaid
sequenceDiagram
    participant Dev as 开发者
    participant Code as 代码
    participant Dec as docs/decisions
    participant Mod as 模块 README
    participant Ref as docs/reference
    participant CI as 提交检查

    Dev->>Dec: 有长期取舍时先写 proposed
    Dev->>Code: 实现功能
    Code-->>Dev: 配置项、行为、接口变化
    Dev->>Mod: 补配置表、语义、限制
    opt 新增对外 API 或协议
        Dev->>Ref: 补一个条目
    end
    opt 决定已落地
        Dev->>Dec: 状态与路径改为 implemented
    end
    Dev->>CI: 提交前自查、链接检查
    CI-->>Dev: 通过或指出缺文档
```

只有对外契约和长期决策值得先写；模块 README 与首个可用版本同批提交。

## 触发点速查

```mermaid
flowchart LR
    A["新模块"] --> A1["模块 README"]
    B["配置项"] --> B1["模块 README 配置表"]
    C["API / 协议 / 错误码"] --> C1["docs/reference"]
    D["有取舍的决定"] --> D1["docs/decisions"]
    E["对外行为变化"] --> E1["模块 README"]
    F["目录或包移动"] --> F1["全仓链接"]
    G["重复被问的步骤"] --> G1["docs/cookbook"]
    H["事故"] --> H1["docs/postmortems"]
    I["版本发布"] --> I1["changelog"]
```

## 档位升级路径

```mermaid
flowchart TD
    A["极简<br/>四层：入口、地图、模块、决策"] --> B["标准<br/>加模板、维护表、模块契约"]
    B --> C["完整<br/>加参考页、手册、双语、检查"]
    A -.->|第二维护者| B
    B -.->|对外发布 / 外部贡献者| C
    C -.->|文档开始重复、过期| D["用迁移套件做一次整理"]
    D -.->|整理后回到当前档位| C
```

极简档不是未完成状态。停在极简档，比建一堆没人维护的层更健康。

## 时间线视角

```text
项目开始          第 1 个模块        第 2 个维护者       对外发布
   |                 |                  |                  |
   README           模块 README        标准档             完整档
   architecture     第一个决策记录     维护规则表         参考页 / 双语
   AGENTS.md                                              自动检查
```

每个节点只加当时需要的东西。没有发生节点事件，就不加对应层。