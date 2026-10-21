# skill-study

Agent Skills 拆解学习笔记。每期抓一个主题，读原文、拆结构、提炼可复用的写法。

不讲「怎么装」，只讲「为什么这么写」。

---

## 目录

| 期数 | 主题 | 内容 |
|---|---|---|
| 01 | [全球安装量 Top 10 行级拆解](./agent-skills-top10/top10-agent-skills-teardown.md) | 十个最火 skill 的 SKILL.md 全文拆解 + 六种架构范式 + 12 条写作手法 + 卡兹克推荐清单（自研 6 + 社区 7）与中文生态写法差异 |

---

## 01 · Agent Skills 全球安装量 Top 10

数据源：skills.sh 登记处（All Time 累计安装量榜），抓取日期 2026-10-21。

| # | Skill | 发布方 | 累计安装 | 行数 | 范式 |
|---|-------|--------|---------|------|------|
| 1 | find-skills | vercel-labs | 4.0M | 142 | 元技能 / 发现器 |
| 2 | grill-me | mattpocock | 1.4M | **8** | 组合器 stub |
| 3 | grill-with-docs | mattpocock | 1.2M | **8** | 组合器 stub |
| 4 | agent-browser | vercel-labs | 1.2M | 53 | 能力封装 stub |
| 5 | hyperframes-cli | heygen-com | 1.2M | 201 | 外部工具循环 |
| 6 | improve-codebase-architecture | mattpocock | 1.1M | 72 | 交互式协议 |
| 7 | tdd | mattpocock | 1.1M | 39 | 规范 / 反模式 |
| 8 | hyperframes | heygen-com | 1.1M | 197 | 入口路由 |
| 9 | setup-matt-pocock-skills | mattpocock | 1.0M | 119 | 安装器 / 契约 |
| 10 | reddit-automation | flowkit-labs | 1.0M | 121 | 垂直业务流 |

**另含附录 A：数字生命卡兹克推荐清单** —— 自研 6 个（khazix-writer / hv-analysis / neat-freak / storage-analyzer / leader / aihot）+ 社区精选 7 个，以及中文 skill 与英文 Top10 的三个写法差异。

### 三个核心结论

1. **排第 2 和第 3 的都是 8 行。** `grill-me` 正文只有 `Call the Skill tool with "grilling".` —— 热度来自命名与编排，不是代码量。
2. **最火的是「会问」的，不是「会做」的。** Top 10 里真正写代码的只有 `tdd` 一个，其余全是交互式流程。
3. **安装量是 pack 级，不是 skill 级。** 同一 pack 内数字高度齐平，说明 `npx skills add <repo>` 是整包安装。

完整拆解见 [top10-agent-skills-teardown.md](./agent-skills-top10/top10-agent-skills-teardown.md)，
原文快照见 [`raw/`](./agent-skills-top10/raw/)（含 [SOURCES.md](./agent-skills-top10/raw/SOURCES.md) 出处与授权说明）。
