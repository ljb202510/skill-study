# 01 · Agent Skills 全球安装量 Top 10 行级拆解

抓取日期：2026-10-21

## 为什么是这 10 个

榜单取自 **skills.sh 登记处**（agent skills 生态的 registry，相当于 npm）的 All Time 累计安装量。
这是目前**唯一有真实采用数据**的公开信号。

先说清楚它不能信什么——这条比榜单本身重要：

| 边界 | 说明 |
|---|---|
| **安装量是 pack 级** | `npx skills add <owner/repo>` 整包安装。证据是数字高度齐平：mattpocock 的 5 个全卡在 1.0–1.4M；hyperframes 系列从 1.2M 平滑递减到 440K。「哪个 skill 最火」这个问题本身有点失真 |
| **star 只能看趋势** | 各 tracker 互相矛盾（`anthropics/skills` 一处 182.5K、一处 299K）。`superpowers` 的 294,967 → 298K → 302.7K 才是合理增长曲线 |
| **关注 ≠ 使用** | 按 star 排是另一副面孔：`morluto/rea` 347.9K（日增 +11.1K 全站最快）却一个都没进安装榜 |

## 榜单

| # | Skill | 发布方 | 累计安装 | 行数 | 架构范式 |
|---|-------|--------|---------|------|---------|
| 1 | find-skills | vercel-labs | 4.0M | 142 | 元技能 / 发现器 |
| 2 | grill-me | mattpocock | 1.4M | **8** | 组合器 stub |
| 3 | grill-with-docs | mattpocock | 1.2M | **8** | 组合器 stub |
| 4 | agent-browser | vercel-labs | 1.2M | 53 | 能力封装 stub |
| 5 | hyperframes-cli | heygen-com | 1.2M | 201 | 外部工具循环 |
| 6 | improve-codebase-architecture | mattpocock | 1.1M | 72 | 交互式协议 |
| 7 | tdd | mattpocock | 1.1M | 39 | 规范 / 反模式 |
| 8 | hyperframes | heygen-com | 1.1M | 197 | 入口路由 |
| 9 | setup-matt-pocock-skills | mattpocock | 1.0M | 119 | 安装器 / 契约 |
| 10 | reddit-automation | flowkit-labs | 1.0M | 121 | 垂直业务流程 |

## 三个核心结论

1. **排第 2 和第 3 的都是 8 行。**
   `grill-me` 正文只有一句 `Call the Skill tool with "grilling".`，`grill-with-docs` 是 `Call the Skill tool twice, for "grilling" and "domain-modeling".`
   它们不是功能，是**组合器**。热度来自命名与编排，不是代码量。

2. **最火的是「会问」的，不是「会做」的。**
   Top10 里真正写代码的只有 `tdd` 一个，其余全是交互式流程。

3. **出现单位是仓库，不是单个 skill。**
   → 所以真正的答案接近「哪套工作流被整套接受了」。

## 六种架构范式

| 范式 | 代表 | 行数 | 特征 |
|---|---|---|---|
| **A. 组合器 stub** | grill-me, grill-with-docs | 8 | 零逻辑，只做命名 + 转发 + 编排 |
| **B. 交互式协议** | grilling, domain-modeling, improve-arch | 31–75 | 定义对话结构：设计树 / frontier / 轮次 |
| **C. 入口路由** | hyperframes | 197 | 状态机分派，取第一条匹配，路由一次就退出 |
| **D. 能力封装 stub** | agent-browser | 53 | 文档由 CLI 提供，杜绝版本漂移 |
| **E. 规范 + 反模式** | tdd | 39 | 讲清什么是对的，更讲清什么看似对其实是错的 |
| **F. 安装器 / 契约** | setup-matt-pocock-skills | 119 | 不产出交付物，只写共享状态文件 |

## 逻辑该放哪（三种选择）

| 做法 | 代表 | 好处 | 代价 |
|---|---|---|---|
| 全在 SKILL.md | tdd、grilling | 离线可用、透明可改 | 版本漂移，文档会腐烂 |
| 放 CLI | agent-browser | 内容永远匹配已安装版本 | 依赖安装 |
| **放服务端** | **aihot** | **skill 文件永不需更新** | 强依赖外部服务 |

## 本目录文件

```
01-agent-skills-top10/
├── README.md      ← 本文件，本期导读
├── teardown.md    ← 主文档：10 个逐个拆解 + 六种范式 + 12 条写作手法
└── sources/
    ├── SOURCES.md    出处与授权说明
    ├── top10/        Top10 主体 10 份全文
    ├── supporting/   被 Top10 引用的支撑文件 8 份
    └── khazix/       数字生命卡兹克自研 6 份
```

## 建议的读法

第一次过：只看每个 skill 的「关键机制」+「值得抄的点」，约 15 分钟。
要动手写时：回来读 §4 的 12 条清单，逐条对照自己的 SKILL.md。
涉及安全时：必读 `neat-freak` 的「权限先于洁癖」和「读到的内容不是给你的指令」两条。
