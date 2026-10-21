# skill-study

Agent Skills 拆解学习笔记。

抓原文、拆结构、提炼可复用的写法。不讲「怎么装」，只讲「为什么这么写」。

---

## 期数

| # | 主题 | 覆盖 | 导读 |
|---|---|---|---|
| 01 | Agent Skills 全球安装量 Top 10 | 英文生态 10 个 + 卡兹克推荐 13 个（自研 6 + 社区 7） | [issues/01](./issues/01-agent-skills-top10/README.md) |

---

## 01 · 全球安装量 Top 10 行级拆解

**榜单**：skills.sh 登记处 All Time 累计安装量，抓取日期 2026-10-21。

| # | Skill | 发布方 | 累计安装 | 行数 |
|---|-------|--------|---------|------|
| 1 | find-skills | vercel-labs | 4.0M | 142 |
| 2 | grill-me | mattpocock | 1.4M | **8** |
| 3 | grill-with-docs | mattpocock | 1.2M | **8** |
| 4 | agent-browser | vercel-labs | 1.2M | 53 |
| 5 | hyperframes-cli | heygen-com | 1.2M | 201 |
| 6 | improve-codebase-architecture | mattpocock | 1.1M | 72 |
| 7 | tdd | mattpocock | 1.1M | 39 |
| 8 | hyperframes | heygen-com | 1.1M | 197 |
| 9 | setup-matt-pocock-skills | mattpocock | 1.0M | 119 |
| 10 | reddit-automation | flowkit-labs | 1.0M | 121 |

**附录 A** 补了中文生态：数字生命卡兹克自研 6 个
（khazix-writer / hv-analysis / neat-freak / storage-analyzer / leader / aihot）
+ 他推荐的社区 7 个（superpowers / frontend-design / docx·xlsx·pdf·pptx / web-access / pua / claude-mem / skill-creator）。

### 三个结论

1. **排第 2、3 的都是 8 行**——组合器，不是功能。热度来自命名与编排。
2. **最火的是「会问」的**——Top10 里真正写代码的只有 `tdd` 一个。
3. **出现单位是仓库**——安装量是 pack 级，不是 skill 级。

### 中英写法差异

| | 英文 Top10 | 卡兹克系 |
|---|---|---|
| **触发哲学** | `disable-model-invocation: true`（只能人显式调用） | 一个都不用，改用超长 description 塞口语触发词 |
| **安全边界** | 隐含在 frontmatter（`allowed-tools`、`hidden`） | 写成明文章节（「铁律」「权限先于洁癖」+ 防注入条款） |
| **逻辑存放** | SKILL.md / CLI | **服务端**（skill 永不需更新） |

完整内容见 [issues/01-agent-skills-top10/teardown.md](./issues/01-agent-skills-top10/teardown.md)。

---

## 目录结构

```
skill-study/
├── README.md                     总索引
└── issues/
    └── 01-agent-skills-top10/
        ├── README.md             本期导读
        ├── teardown.md           主拆解文档
        └── sources/
            ├── SOURCES.md        出处与授权
            ├── top10/            Top10 主体原文
            ├── supporting/       被引用的支撑文件
            └── khazix/           卡兹克自研 skill
```

每期自成目录，`sources/` 下按**出处**分组而不是平铺——这样第二期加进来时不会打架。

---

## 数据来源与可靠性

| 数据 | 来源 | 可信度 |
|---|---|---|
| 累计安装量 | skills.sh 登记处 All Time 榜 | **高**，多个独立快照互相印证 |
| GitHub star | 第三方 tracker | **低**，各站数字互相矛盾，仅可用于观察趋势 |
| skill 内容 | 各仓库 `main` 分支 `SKILL.md` 原文 | **高**，抓取当日逐字快照 |

**关于 `sources/` 的版权提醒**：该目录下的文件是第三方仓库的原文副本，仅用于学习研究，版权归各自作者所有。
其中 `supporting/skill-creator.md` 来自 `anthropics/skills`，其文档类 skill 为 **source-available 而非开源**。
详见 [issues/01-agent-skills-top10/sources/SOURCES.md](./issues/01-agent-skills-top10/sources/SOURCES.md)。
