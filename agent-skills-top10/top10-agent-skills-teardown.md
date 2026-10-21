# Agent Skills 全球安装量 Top 10 — 行级拆解与 Skill 开发手册

> 抓取日期：2026-10-21
> 数据来源：skills.sh 登记处（All Time 累计安装量榜）+ 各仓库 `SKILL.md` 原文
> 本地原文素材：`./raw/`

---

## 0. 口径说明：先说清楚这个榜能信几分

榜单本身是硬的——skills.sh 是 agent skills 生态的 registry（相当于 npm），累计安装量是目前**唯一有真实采用数据**的公开信号。但要认清三条边界：

**边界一：安装量是 pack 级，不是 skill 级。**
`npx skills add <owner/repo>` 会整包安装。证据就是榜上数字的高度齐平：mattpocock 的 5 个 skill 全卡在 1.0–1.4M 的窄带里，hyperframes 系列从 1.2M 平滑递减到 440K。**「哪个 skill 最火」这个问题本身有点失真，真实答案更接近「哪套工作流被整套接受了」。**

**边界二：GitHub star 只能看趋势，不能精确比较。**
各 tracker 打架：`obra/superpowers` 在 10-04 是 294,967、10-12 约 298K、10-21 是 302.7K（这是合理增长）；但 `anthropics/skills` 一处说 182.5K、另一处说 299K——后者明显错。star 只配用来看方向。

**边界三：关注 ≠ 使用。**
按 star 排是完全另一副面孔：`morluto/rea` 347.9K（10-20 单日 +11.1K，全站最快）、`obra/superpowers` 302.7K、`mattpocock/skills` 299K、`affaan-m/ECC` 284.9K、`anthropics/skills` 182.5K。rea 日增最猛，但它的 skill 一个都没进安装榜 Top10。

---

## 1. 榜单总览

| # | Skill | 发布方 | 累计安装 | SKILL.md 行数 | 架构范式 |
|---|-------|--------|---------|--------------|---------|
| 1 | find-skills | vercel-labs | 4.0M | 142 | 元技能 / 发现器 |
| 2 | grill-me | mattpocock | 1.4M | **8** | 组合器 stub |
| 3 | grill-with-docs | mattpocock | 1.2M | **8** | 组合器 stub |
| 4 | agent-browser | vercel-labs | 1.2M | 53 | 能力封装 stub + CLI 文档 |
| 5 | hyperframes-cli | heygen-com | 1.2M | 201 | 外部工具循环 |
| 6 | improve-codebase-architecture | mattpocock | 1.1M | 72 | 交互式协议 |
| 7 | tdd | mattpocock | 1.1M | 39 | 规范 / 反模式 |
| 8 | hyperframes | heygen-com | 1.1M | 197 | 入口路由（状态机） |
| 9 | setup-matt-pocock-skills | mattpocock | 1.0M | 119 | 安装器 / 共享契约 |
| 10 | reddit-automation | flowkit-labs | 1.0M | 121 | 垂直业务流程 |

---

## 2. 逐个拆解

### 1. find-skills · vercel-labs · 4.0M · 142 行

**做什么**：帮 agent 找 skill 的 skill。这是榜首，而且有点套娃。

**触发条件**（写在 description 里）：用户说 "how do I do X" / "find a skill for X" / "is there a skill that can..." / "can you do X" / 表达想扩展 agent 能力 / 提到希望某个领域有帮助。

**六步流程**：
1. 拆出「领域 → 具体任务 → 这事是否常见到该有现成 skill」
2. **先查 skills.sh 排行榜**，没有再搜索——优先成熟方案，省一次调用
3. `npx skills find [query] [--owner <owner>]`
4. **三档质检**：安装量 > 1K 优先、< 100 谨慎；来源信誉（`vercel-labs`/`anthropics`/`microsoft` > 未知作者）；仓库 star < 100 存疑
5. 呈现四件套：名字+功能、安装量+来源、安装命令、skills.sh 链接
6. `npx skills add <owner/repo@skill> -g -y`（`-g` 全局，`-y` 免确认）

**值得抄的**：
- **「先看榜再搜索」的短路设计**——把最省力的路径写进流程第一步
- **明确的三档阈值**（1K / 100 / <100 star）替代含糊的「检查质量」，模型可执行
- **输出模板是给出来的，不是让模型自己编**（文档里直接写了示例回复）

**值得警惕的**：Vercel 既运营这个目录，又写了这个默认推荐器。它没有在文档里声明这个利益关系。

---

### 2. grill-me · mattpocock · 1.4M · **8 行**

**全部正文**：`Call the Skill tool with "grilling".`

**frontmatter**：
```yaml
name: grill-me
description: A relentless interview to sharpen a plan or design.
disable-model-invocation: true
```

**这是整份榜单里最重要的一课**：排第 2 的 skill 只有 8 行。它不是功能，是**组合器**——一个语义清晰的名字 + 一行转发。热度来自命名和编排，不是代码量。

`disable-model-invocation: true` 意味着模型不能自动调用它，**只能人显式触发**。这是刻意的：追问流程很贵，不该被自动触发。

真正的逻辑在 `grilling`（31 行），见下方 §3 的协议型拆解。

---

### 3. grill-with-docs · mattpocock · 1.2M · **8 行**

**全部正文**：`Call the Skill tool twice, for "grilling" and "domain-modeling".`

同样是组合器，但组合的是两个原子能力：**追问** + **沉淀领域文档**。这就是「grill-me」和「grill-with-docs」的差别——后者在追问过程中持续把结论写进 `GLOSSARY.md` 和 ADR，前者只是问完。

`domain-modeling`（75 行）的核心规则：
- 术语冲突时**当场打断**："你的词汇表把 cancellation 定义为 X，但你说的似乎是 Y。哪个？"
- 模糊词当场锐化："你说的 account 是指 Customer 还是 User？这是两个东西。"
- 发明边界场景反逼精确
- 用户说「系统怎么运作」时**去查代码验证**，发现矛盾就摊出来
- GLOSSARY.md **随用随写，不批量补记**
- GLOSSARY.md **零实现细节**——它是词汇表，不是规格、不是草稿纸
- ADR 只在「难逆转」时才提议写，且措辞是："要不要记成 ADR，免得以后架构评审又提一遍？"

---

### 4. agent-browser · vercel-labs · 1.2M · 53 行

**做什么**：浏览器自动化 CLI。但这个 skill 本身**是个 discovery stub（发现桩）**。

**最关键的设计**（文档原话）：
> This file is a discovery stub, not the usage guide. Before running any `agent-browser` command, load the actual workflow content from the CLI... The CLI serves skill content that always matches the installed version, so instructions never go stale. **The content in this stub cannot change between releases**, which is why it just points at `skills get core`.

即：**把使用文档从 SKILL.md 里搬出去，由 CLI 现场提供**。理由是「指令永远匹配已安装版本，不会过期」。这是解决 skill 版本漂移最干净的方案——SKILL.md 会随仓库更新，但用户装的 CLI 可能是旧版，文档塞在 SKILL.md 里必然对不上。

**frontmatter 里的权限收敛**：
```yaml
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
hidden: true
```
只允许跑 `agent-browser` 相关命令，其余一律不授权。`hidden: true` 让它在自动发现里隐身。

**技术栈**：Rust 原生 CLI（不是 Node 包装）、Chrome/Chromium over CDP、不依赖 Playwright/Puppeteer、accessibility-tree 快照 + 紧凑的 `@eN` 元素引用、sessions + 认证保险库 + 状态持久化 + 录像。

**子 skill 按需加载**：`electron`（VS Code/Slack/Discord/Figma/Notion/Spotify 等桌面应用）、`slack`、`dogfood`（探索性测试/QA）、`derive-client`（录 HAR 反推 API client）、`vercel-sandbox`、`protected-vercel-deployments`、`agentcore`（AWS Bedrock 云浏览器）。

---

### 5. hyperframes-cli · heygen-com · 1.2M · 201 行

**做什么**：HeyGen 出品的「用 HTML 写视频」框架，CLI 部分管开发循环。

**核心主张**：composition 就是一个 HTML 文件——DOM 用 `data-*` 属性声明时序，动画 runtime 可 seek，媒体播放由框架接管，最后**确定性渲染成 MP4**（不是逐帧 AI 生成）。

**十步开发循环**：
1. `init <project>` 脚手架
2. **先在 catalog 里找现成动效**：`npx hyperframes catalog --query "reveal a headline one line at a time"`，装现成的（`hyperframes add <name>`），**找不到才手写**
3. 按 `/hyperframes-core` 契约写；想知道时间线上有什么，跑 `timeline --json`，**不要去读 index.html 和每个子 composition 文件**
4. `lint` 快速反馈
5. `check` 最终关卡（内含 lint，别重复跑 lint）
6. 检查子 composition 的中点快照
7. `preview --background` 开 Studio 预览，确认返回 HTTP 200
8. **批准后才 render**：迭代用 `--quality draft`，首次真编码 `--quality looks`，交付 `--quality delivery`
9. 可选：交接给桌面应用
10. 验证产出：确认文件存在且非空，读 render summary 第二行，用 ffprobe 对比时长

**三个非常值得抄的机制**：

1. **history 按「轮次」归属改动**：`history begin --who <name> --label "<要做什么>"` … `history end`。作用是「把你（agent）的改动识别为你的，人的改动识别为人的」。`history undo --who <name>` 能撤销 agent 自己最新的改动，**而不动人的编辑**。这解决了多轮人机协作里最难的问题：agent 要能回滚自己，但不能覆盖人。
2. **catalog 搜索明确承诺不外发**：「Search is entirely local: there is no hosted tier, no account, and the query text is never sent anywhere.」且 `feedback --search-miss` 被显式标注为**唯一会外发数据的命令**，所以刻意做成独立命令——「这样普通的 `catalog --query` 才能守住它不外发的承诺」。
3. **JSON 路径把 home 目录 redact 成 `$HOME`**，并明确要求「不要试图反推脱敏内容」。

---

### 6. improve-codebase-architecture · mattpocock · 1.1M · 72 行

**做什么**：扫 codebase，找「**加深机会**」(deepening opportunities)——把 shallow module 变成 deep module 的重构点。目标是**可测试性**和**AI 可导航性**。

**流程**：
1. **先用 `git log --oneline` 找热点文件**（原文强调 YAGNI：加深一个模块的价值在于让它未来改起来更容易，所以**只关注最近在变的地方**）
2. 读 `GLOSSARY.md` 和该区域的 ADR
3. 派 **sub-agent 走查**，明确要求「别用死板启发式，有机地探索，记录你在哪里感到摩擦」
4. 用 **deletion test** 判断：删掉它，复杂度是消失了，还是只是被搬到 N 个调用方？
5. 产出：写到 **OS temp 目录**（`$TMPDIR` → `/tmp` → Windows `%TEMP%`）的自包含 HTML 报告，文件名 `architecture-review-<timestamp>.html`
6. 打开报告，问「想先探索哪个？」
7. 选定后进 grilling 循环，同时用 domain-modeling 维护领域模型

**词汇强约束（这个最狠）**：
```
Use exactly: module, interface, implementation, depth, deep, shallow,
             seam, adapter, leverage, locality

Never substitute: component, service, unit (for module)
                  API, signature (for interface)
                  boundary (for seam)
                  layer, wrapper (for module)
```
并且给了示范句式和禁句：
- ✅ "Order intake module is shallow: interface nearly matches the implementation."
- ❌ 不许写 "easier to maintain"、"cleaner code"——**「因为这两个词不在词汇表里，它们不配占位」**。

这是我在这些 skill 里见过最激进的一致性手段：**给模型一份封闭词表，并禁止发明新词**。目的很实际：多个 skill 协作时，如果各自用 component / service / boundary 混说，人和模型都无法对齐。

**配套：codebase-design（115 行）共享词汇表**
八个术语：Module（任何有接口和实现的东西，刻意 scale-agnostic）/ Interface（调用方正确使用时**必须知道的一切**：类型签名 + 不变量 + 顺序约束 + 错误模式 + 必需配置 + 性能特征）/ Implementation / Depth / Seam（Michael Feathers 的定义）/ Adapter / Leverage（调用方从深度得到什么）/ Locality（维护者从深度得到什么）。

四条原则：
- **Depth 是 interface 的属性，不是 implementation 的。** 深模块内部可以由小的、可 mock 的、可替换的部件组成，只是它们不属于接口。模块可以有 **internal seam**（实现私有，自己的测试用）和 **external seam**（在接口处）。
- **Deletion test**
- **接口就是测试面**：调用方和测试跨越同一个 seam。想「越过接口去测试」，说明模块形状错了。
- **One adapter = 假想的 seam；two adapters = 真的 seam。** 没有东西在变，就别引入 seam。

可测试性三法：接受依赖而非创建依赖 / 返回结果而非产生副作用 / 小表面积。

**「Rejected framings」这一节尤其值得学**——它明确列出了被拒绝的框架和原因（比如不用 Ousterhout 的「实现行数/接口行数」比，因为它「奖励虚胖实现」）。把决策边界写下来，比只写结论更能防止漂移。

---

### 7. tdd · mattpocock · 1.1M · 39 行

**做什么**：测试驱动开发。但只讲一件事——**红 → 绿**。

**一个反直觉的断言**：文档明说**重构不属于这个 loop**（归 review 阶段）。大多数 TDD 描述都把「红-绿-重构」当成一体，这里刻意切开了。

**三条反模式**（写得相当狠）：

1. **实现耦合**：mock 内部协作者、测私有方法、断言调用次数/顺序。**判据：你重构了但行为没变，测试却崩了。**
2. **同义反复**：期望值用和代码一样的方式重算一遍，`expect(add(a,b)).toBe(a+b)`。**判据：它在构造上就永远通过，永远不可能与代码不一致。** 期望值必须来自独立真值源：一个已知正确的字面量、一个演算过的例子、规格说明。
3. **横向切片**：先把所有测试写完，再写所有实现。批量测试验证的是**想象中的行为**——你测的是事物的形状而不是面向用户的行为，测试对真实变化逐渐失去敏感度，而且你在理解实现之前就把测试结构定死了。要**垂直切片**：一个测试 → 一个实现 → 重复，每个测试都是响应上一轮教训的**曳光弹**。

**seam 规则**：测试只写在 seam 上，**且 seam 必须先跟用户确认**——「未确认的 seam 上不写测试」。理由：你不可能测所有东西，事先对齐 seam 才能让测试投入落在关键路径和复杂逻辑上，而不是散落在每个边界情况。

**配套 reference**：
- `tests.md`（78 行）——好测试 vs 坏测试的正反例代码
- `mocking.md`（60 行）——**只在系统边界 mock**：外部 API、数据库（优先测试库代替）、时间/随机数、文件系统。不 mock 自己的类/模块、内部协作者、任何你掌控的东西。两条设计建议：依赖注入；**SDK 风格接口优于通用 fetcher**（每个外部操作一个具体函数，而不是一个带条件分支的通用函数——因为这样每个 mock 只返回一种形状，测试 setup 里没有条件逻辑）。

---

### 8. hyperframes · heygen-com · 1.1M · 197 行

**做什么**：HyperFrames 的**强制入口点**。description 第一句就是 `Mandatory entry point: read this first`。

**状态机分派**（取第一条匹配行，不评估后面的）：

| 状态 | 动作 |
|---|---|
| 明确要求把现有 Remotion 源码移植过来 | 读 `references/routes/remotion-to-hyperframes.md`，直接路由，跳过意图层 |
| 对已有项目做某个具体操作（inspect/diagnose/render/publish/batch-render） | 只做那一个操作，加载 `/hyperframes-cli` |
| 在已有项目里提问、暂停、只有想法没有具体改动 | 走 `/hyperframes-studio` § 0 |
| 在已有项目里要一部新片 | 走 `/hyperframes-studio` § 5 |
| 对已有项目的具体编辑 | 直接改，不跑意图层 |
| 存在 `BRIEF.md` | 读 `workflow` 和 `flow` 直接执行，**不再问 brief 问题** |
| 无 brief 但有 `hyperframes.json` 或 `STORYBOARD.md` | 从项目文件恢复，推断所属 workflow；无法唯一确定时只问一个路由问题 |
| 全新创作 | 跑意图层 `references/intent-interview.md`，然后路由一次 |

**额度检查的诚实要求**：
> At the start of creation, run `npx hyperframes usage --json`... **a previous read does not reserve allowance**. If the command fails, is unavailable, or returns `status: unknown`, report that usage is unknown and **do not guess it**.

**按需加载域 skill 表**：列了 10 类需求到具体 skill 的映射（core / animation / keyframes / creative / media-use / audio / cli / registry / figma）。还有一张 **creator edit 跨域组合表**：不同剪辑诉求需要**同时加载多个域 skill**（例：切镜头 = `/general-video` + `/hyperframes-core`；推拉镜头 = 再加 `/hyperframes-keyframes`）。

**一句收尾原则**：`Domain skills never take ownership of the end-to-end deliverable. Load only what the active workflow needs.` ——防止域 skill 之间互相抢控制权。

---

### 9. setup-matt-pocock-skills · mattpocock · 1.0M · 119 行

**做什么**：**安装器**。它自己不产出任何交付物，只做「每仓库一次性配置」，为其他 engineering skill 铺好共享状态。

**配置三件事**：
1. **Issue tracker** — 选 GitHub（用 `gh`） / GitLab（用 `glab`） / 本地 markdown（`.scratch/<feature>/`） / 其他（Jira、Linear 等，让用一段话描述，记录为自由文本）
2. **Triage 标签词表** — 五个 canonical 角色，标签字符串等于名字：`needs-triage` / `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`
3. **领域文档布局** — 默认单 context（根 `GLOSSARY.md` + `docs/adr/`），**直接写，不用问**；只有探索到 monorepo 信号（`pnpm-workspace.yaml`、`package.json` 的 `workspaces`、`packages/*`）才提供 multi-context 选项

**先探索再写**：明确规定要读 `git remote -v`、`.git/config`、根 `AGENTS.md` / `CLAUDE.md`（是否已有 `## Agent skills` 段）、`GLOSSARY.md`、`GLOSSARY-MAP.md`、`docs/adr/`、`docs/agents/`、`.scratch/`、以及 triage skill 是否已安装。

**写文件的选择规则（很细致）**：
```
- 有 CLAUDE.md → 编辑它
- 否则有 AGENTS.md → 编辑它
- 都没有 → 问用户创建哪个，别替他们选
- 绝不创建 AGENTS.md 当 CLAUDE.md 已存在（反之亦然）
- 若已存在 ## Agent skills 块 → 原地更新，不要追加重复块
- 不要覆盖用户对周围章节的编辑
```

**交互节奏**：一个 section 一个问题，逐段确认。**推荐答案放最前面**，让人一个词就能接受。section 已由探索确定的（比如没装 triage 就跳过 B，不是 monorepo 就跳过 C）**整段跳过**。

**这是「共享状态契约」范式**：多个 skill 之间通过**文件系统上的约定**（`docs/agents/*.md`、`GLOSSARY.md`、`docs/adr/`）协作，而不是靠上下文传递。

---

### 10. reddit-automation · flowkit-labs · 1.0M · 121 行

**做什么**：在 Reddit 上找「真的在问你要卖的东西」的帖子，然后起草诚实的回复。出品方 doany.ai。

**两阶段**：

**Phase 1 — 发现机会**：扫目标 subreddit 的近期帖子（能搜索就用高意图短语，**绝不用宽泛关键词**），只保留你真能帮上的需求：
- 推荐询问（"what do you use for…"、"alternatives to X?"）
- 产品能消除的**表达出来的痛点**
- 竞品提及（尤其是对竞品的抱怨）
- 紧迫性 / 具体的当下工作流或上下文

**排除**：跑题闲聊、已有解答的帖子、锁定/归档的帖子、以及只是发泄不是在问的。

**三因子排序取 Top 3**：`OP 信号`（多明确地现在就需要）× `产品契合度` × `时机`（新帖、对的 sub、有些互动、回复还没饱和）。每个候选写一句大白话：*你为什么真的能帮到这个人*。

**Phase 2 — 起草回复**：从真实经验出发写、尊重每个社区的自我推广规则、**只在产品真能回答问题时才提它**、保留人工审核再发。

**frontmatter 用了扩展字段**（这是唯一一个）：
```yaml
displayName: "👽 Reddit Automation — find high-intent threads, reply with honest help"
emoji: "👽"
homepage: https://doany.ai
license: MIT
```
`displayName` 带情绪钩子 + emoji，明显是为目录页的点击率设计的。它在 description 里直接写了 **"never astroturf"**——把「不许伪装成普通用户刷口碑」写进技能定义，这在商业营销 skill 里不多见。

---

## 3. 从这 10 个里抽象出的六种架构范式

| 范式 | 代表 | 行数 | 核心特征 |
|---|---|---|---|
| **A. 组合器 stub** | grill-me, grill-with-docs | 8 | 零逻辑，只做「命名 + 转发 + 编排」 |
| **B. 交互式协议** | grilling, domain-modeling, improve-arch | 31–75 | 定义对话结构（设计树/frontier/轮次），规定何时问、何时停 |
| **C. 入口路由** | hyperframes | 197 | 状态机分派，取第一条匹配，路由一次就退出 |
| **D. 能力封装 stub** | agent-browser | 53 | SKILL.md 只当发现桩，真文档由 CLI 提供，杜绝版本漂移 |
| **E. 规范 + 反模式** | tdd | 39 | 讲清什么是对的，更讲清什么看似对其实是错的 |
| **F. 安装器 / 契约** | setup-matt-pocock-skills | 119 | 不产出交付物，只写其他 skill 依赖的共享状态文件 |

外加两类非架构但值得注意的：**外部工具循环型**（hyperframes-cli，201 行，本质是 CLI 的人类可读工作流说明）和**垂直业务型**（reddit-automation，121 行，把一套运营 SOP 编码成两个阶段）。

---

## 4. 可复用写作手法清单（附出处）

**① 8 行能拿 140 万安装——先想名字，再想代码。**
`grill-me` 的全部价值是一个语义精确的名字 + 一行转发。值得单独花时间打磨 name 和 description。

**② description 是唯一触发器，要写得「pushy」。**
官方 `skill-creator` 原话：模型有 **undertrigger** 倾向（该用的时候不用），所以 description 要写得强势一点。它给的改造示例：
> ❌ "How to build a simple fast dashboard to display internal Anthropic data."
> ✅ "...Make sure to use this skill whenever the user mentions dashboards, data visualization, internal metrics, or wants to display any kind of company data, **even if they don't explicitly ask for a 'dashboard.'**"

`hyperframes` 更进一步，description 第一句就是 "Mandatory entry point"。

**③ 把「何时用」全部塞进 description，不要留在正文。**
`skill-creator` 明确要求：All "when to use" info goes here, not in the body.

**④ 用 frontmatter 收敛权限和调用方式。**
- `disable-model-invocation: true` → 只能人显式调用（grill-me / grill-with-docs / improve-arch / setup 全用了）
- `allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)` → 白名单到具体命令前缀
- `hidden: true` → 从自动发现里隐身

**⑤ 给模型一份封闭词表，并禁止发明新词。**
mattpocock 整套 skill 共用 codebase-design 的八个术语，连「easier to maintain」这种话都明令禁止。这是多 skill 协作能对齐的前提。

**⑥ 把决策边界写下来，而不只是结论。**
`codebase-design` 的 "Rejected framings" 一节列出了被拒方案及原因。防止模型自作聪明换框架。

**⑦ 输出物不要落进用户 repo。**
`improve-codebase-architecture` 明确要求报告写到 OS temp 目录（`$TMPDIR` → `/tmp` → Windows `%TEMP%`）。

**⑧ 让文档跟着安装版本走。**
`agent-browser` 的 stub + `skills get core`。SKILL.md 会更新，用户装的 CLI 不会。

**⑨ 用阈值代替形容词。**
不写「检查质量」，写「> 1K 安装优先，< 100 谨慎，仓库 < 100 star 存疑」。

**⑩ 允许失败，但不许瞎猜。**
hyperframes 的 `usage --json` 返回 `status: unknown` 时，明确要求「报告额度未知，不要猜」。这条比「一定要成功」的指令有用得多。

**⑪ 把「先找现成的」写进流程第一步。**
hyperframes-cli 的 catalog 搜索、find-skills 的先查榜——都把短路路径放在最前面。

**⑫ 显式声明数据外发边界。**
hyperframes-cli 标注 `feedback --search-miss` 是唯一外发命令，并解释为什么它被刻意拆成独立命令：「这样普通的 `catalog --query` 才能守住它不外发的承诺」。

---

## 5. 安全提醒

NVIDIA 分析过 3.1 万个公开 skill，结果：**26.1% 存在漏洞，5.2% 疑似有恶意意图**。

对做 Skill 开发的人，这既是风险也是机会。安装前：
- 完整读 `SKILL.md`
- 读每一个脚本，找网络调用和项目外的写入
- 检查 `allowed-tools` 预授权了什么
- 固定到具体 commit 或 release，让「你审过的版本」就是「跑起来的版本`

---

## 6. 附录：本地原始素材索引

`./raw/` 下 18 个文件，均为抓取当日的仓库原文：

**Top 10 主体**
- `01-find-skills.md` (142)
- `02-grill-me.md` (8)
- `03-grill-with-docs.md` (8)
- `04-agent-browser.md` (53)
- `05-hyperframes-cli.md` (201)
- `06-improve-codebase-architecture.md` (72)
- `07-tdd.md` (39)
- `08-hyperframes.md` (197)
- `09-setup-matt-pocock-skills.md` (119)
- `10-reddit-automation.md` (121)

**被引用的支撑文件**
- `ref-grilling.md` (31)
- `ref-domain-modeling.md` (75)
- `ref-codebase-design.md` (115)
- `ref-tdd-tests.md` (78)
- `ref-tdd-mocking.md` (60)
- `ref-improve-html-report.md` (124)
- `ref-glossary-format.md` (61)

**官方元技能**
- `ref-skill-creator.md` (486)
