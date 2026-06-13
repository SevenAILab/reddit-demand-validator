# Reddit Demand Validator V2 — 证伪体系 + 证据加权 实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 V1 从"只会找抱怨的单向调研"升级为"先预注册杀死标准、强制证伪、用互动数据加权证据、输出可执行下一步实验"的需求验证系统。

**Architecture:** 纯文档型 skill（无代码运行时），改动集中在 5 个 markdown 文件 + 新增 2 个文件。方法论保持 V1 的核心骨架（三档证据分级、去重、机械结论表、需求与商业模式分离），叠加 6 个 V2 能力：①预注册杀死标准 ②强制证伪轮 ③互动数据加权 ④痛点聚类 ⑤趋势方向与 AI 吞噬检测 ⑥RAT 最危险假设实验 + Mom Test 访谈脚本输出。

**Tech Stack:** Markdown skill 文件（Codex/Claude Code 可读）、Reddit 公开 JSON API（curl，无需鉴权）、search-engine `site:reddit.com` 查询。

---

## 调研来源（本计划借鉴了什么）

| 来源 | 借鉴的设计 |
|---|---|
| `mguozhen/reddit-pain-point-scanner` | 痛点聚类（先聚类再评分，≥2 次提及才成立）；紧迫度语言信号；"对现有产品的抱怨" vs "无解决方案的空白" vs "功能请求" 三分类；解决方案密度（空白/执行差距/饱和） |
| `emanueleielo/skills` (reddit-explorer) | Reddit 公开 JSON API 完整工具箱：站内搜索 `restrict_sr`、评论搜索 `type=comment`、时间过滤 `t=year`、`duplicates` 端点做去重、分页、限速规则；r/forhire 等付费市场的价格情报配方 |
| `MaxKmet/idea-validation-agents` | RAT（最危险假设测试，criticality×uncertainty 排序）+ ≤2周/≤$100 行为实验模板 + 事前定死的 pass/fail 阈值；Kill criteria；Pre-mortem（Klein 2007）；决策备忘录写作纪律（禁止 hedging、风险比优点写得更细、只给一个下一步）；置信度水印（数据不全时强制声明） |
| `elcodabra/validate-idea-skills` | Mom Test 访谈方法（问过去行为不问未来意愿；"你上次遇到是什么时候"替代"你会用吗"）；验证陷阱清单（虚荣指标、引导性问题、确认偏误）；Reddit 调研只是验证漏斗第一步，备忘录要接到下一步实验 |
| `qiwen121/claude-skill-reddit-market-research` | 竞品 subreddit 交叉搜索（行业版块 + 竞品专属版块 + 品牌词提及）；竞品抱怨=机会 / 竞品优势=学习 双向归类 |
| GummySearch（商业标杆）方法论 | 内容四分类（pain points / solution requests / money talk / self-promo）；money talk = 真实付费语言挖掘 |

V1 已经做对、V2 必须保留不动的：三档证据分级及"弱证据不能独立支撑"规则、机械结论表、查询收敛过程、抓取完整性披露、需求强度与商业模式分离、demote 创业者社区。

---

## 文件结构

```
.agents/skills/reddit-demand-validator/
  SKILL.md                 # 修改：工作流加 Step 0 / 2.8 / 6.5，Step 5/8/9 扩充，Core Rules 增补
  query-templates.md       # 修改：新增 证伪(falsification) 与 已付费(money-talk) 两个查询家族 + Reddit 站内搜索写法
  scoring-rubric.md        # 修改：共鸣加权、趋势方向维度、revealed WTP、置信度、证伪门
  report-template.md       # 修改：新增 预注册/证伪结果/证据表/聚类/JTBD原话/RAT/Pre-mortem 章节
  reddit-api-toolkit.md    # 新增：Reddit JSON API 数据层参考（curl 配方）
README.md                  # 修改：V2 特性说明
tasks/
  2026-06-13-v2-falsification-and-evidence-weighting-plan.md   # 本计划
  calibration-v2.md           # 新增：Task 7 校准测试记录
  v2.1-backlog.md             # 新增：多平台适配器蓝图（不在 V2 实施）
```

跨文件一致性约束（自审时必查）：
- SKILL.md Step 9 列出的备忘录章节名，必须与 report-template.md 的中文标题逐一对应
- scoring-rubric.md 的维度名（含新增"趋势方向"），必须与 report-template.md 维度判断小节一致
- "高共鸣线程"、"证伪门"、"显性付费证据" 等术语在三个文件中拼写一致

执行纪律（给 Codex）：
- 每个 Task 一个 commit，commit message 用 `feat(v2): ...` 前缀
- 不要 push；全部完成后由用户审核推送
- skill 正文保持英文（与 V1 一致），备忘录模板内的输出标题保持中文
- 修改时用精确锚点文本定位，禁止整文件重写（防止丢失 V1 内容）

---

### Task 1: 新增 `reddit-api-toolkit.md`（数据层参考）

V1 只靠搜索引擎 `site:reddit.com` + 裸 `.json` 读帖。V2 给 skill 一个完整的 Reddit 公开 API 工具箱，让"站内补搜、评论搜索、去重确认、互动数据采集"都有现成配方。

**Files:**
- Create: `.agents/skills/reddit-demand-validator/reddit-api-toolkit.md`

- [ ] **Step 1: 创建文件，写入以下完整内容**

````markdown
# Reddit JSON API Toolkit

Reference for fetching structured Reddit data without authentication. Use this AFTER search-engine discovery has identified candidate subreddits, for: in-subreddit follow-up search, comment search, metadata collection, dedup confirmation, and trend-window comparison.

## Rules

- Always send a User-Agent: `-A "demand-validator/2.0"`
- Rate limit: ~10 requests/minute without auth. `sleep 2` between batched requests.
- Append `.json` to almost any reddit URL to get JSON.
- If a request returns 403 or a block page, fall back to `old.reddit.com` HTML, then to search snippets (same fallback order as SKILL.md Step 5).

## 1. In-subreddit search (constraint-led second pass)

```bash
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/{subreddit}/search.json?q={query}&restrict_sr=on&sort=relevance&t=year&limit=25"
```

- `t` = hour | day | week | month | year | all — use `t=year` for the main window, `t=all` for the trend comparison window
- `sort` = relevance | hot | top | new | comments

## 2. Global search and comment search

```bash
# All of Reddit
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/search.json?q={query}&sort=relevance&t=year&limit=25"

# Comment-only search — workaround details and "me too" replies live here
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/search.json?q={query}&type=comment&limit=25"
```

## 3. Thread with comments (metadata collection)

```bash
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/{subreddit}/comments/{post_id}.json?sort=top&limit=20&depth=3"
```

For every deep-read thread, record from the JSON:

- `score` (upvotes), `num_comments`, `upvote_ratio`, `created_utc`
- Count of distinct agreement replies in top comments: "same here", "this exactly", "+1", "I have this exact problem", "came here to say this"

These numbers feed the evidence table and the resonance weighting in scoring-rubric.md.

## 4. Subreddit metadata

```bash
curl -s -A "demand-validator/2.0" "https://www.reddit.com/r/{subreddit}/about.json"
```

Extract: `subscribers`, `accounts_active`, `public_description`, `created_utc`.

## 5. Dedup confirmation (crossposts)

```bash
curl -s -A "demand-validator/2.0" -L \
  "https://www.reddit.com/r/{subreddit}/duplicates/{post_id}.json?limit=10"
```

Use this when two threads look like the same evidence cluster — the duplicates endpoint lists crossposts directly. Crossposts always count as ONE evidence point.

## 6. Author check (filtering only)

```bash
curl -s -A "demand-validator/2.0" "https://www.reddit.com/user/{username}/about.json"
```

Use only to downgrade suspicious authors (new, narrow, promotional). Never put author identity in the report.

## 7. Pagination

```bash
# First page returns data.after = "t3_abc123"; pass it back:
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/{subreddit}/new.json?limit=100&after=t3_abc123"
```

Max 100 items per page, ~1000 total. Rarely needed for this skill — prefer targeted search over crawling.

## 8. Money-talk sources (revealed willingness to pay)

Real payment evidence beats stated intent. Search these alongside the niche subreddits:

```bash
# What people pay freelancers/agencies for this job
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/forhire/search.json?q={task}&restrict_sr=on&sort=new&t=year&limit=20"

# Bottom-of-market pricing
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/slavelabour/search.json?q={task}&restrict_sr=on&t=all&limit=20"
```

Grep titles/bodies for `$` amounts, "/mo", "per hour" — concrete prices users already pay for substitutes are the strongest WTP evidence this skill can collect.
````

- [ ] **Step 2: 结构校验**

Run: `grep -c '^## ' .agents/skills/reddit-demand-validator/reddit-api-toolkit.md`
Expected: `9`（Rules + 8 个编号小节）

Run: `grep -n 'demand-validator/2.0' .agents/skills/reddit-demand-validator/reddit-api-toolkit.md | wc -l`
Expected: ≥ 8（每个 curl 配方都带 UA）

- [ ] **Step 3: Commit**

```bash
git add .agents/skills/reddit-demand-validator/reddit-api-toolkit.md
git commit -m "feat(v2): add reddit json api toolkit as data layer reference"
```

---

### Task 2: `query-templates.md` — 新增证伪与已付费查询家族

**Files:**
- Modify: `.agents/skills/reddit-demand-validator/query-templates.md`

- [ ] **Step 1: 在 `## Pain` 标题行上方插入证伪与 Money Talk 两个家族**

````markdown
## Falsification (MANDATORY — run after the constraint-led pass)

Every run MUST include 3+ falsification queries. The goal is to actively search for evidence that the demand is NOT real: satisfied users, "good enough" incumbents, problems already absorbed by general AI tools. A validation that only searches for complaints will find complaints for any topic.

### Satisfaction version

```text
site:reddit.com/r/ ("works great" OR "works fine" OR "no complaints" OR "does everything I need" OR "happy with") ("<current tool>" OR "<category>")
```

### Good-enough version

```text
site:reddit.com/r/ ("just use" OR "is enough" OR "good enough" OR "why not just" OR "solved my problem") ("<task>" OR "<problem>")
```

### AI-absorption version

```text
site:reddit.com/r/ ("just ask chatgpt" OR "I use claude for" OR "AI does this" OR "chatgpt solved") ("<task>" OR "<problem>")
```

Record falsification hits with the same rigor as pain hits. The memo must report the falsification results, and scoring-rubric.md defines how dominant satisfaction caps the conclusion.

## Money Talk (revealed willingness to pay)

Stated intent ("take my money") is cheap. Search for users ALREADY PAYING for substitutes:

### Current-spend version

```text
site:reddit.com/r/ ("I pay" OR "I'm paying" OR "costs me" OR "$ per month" OR "subscription for") ("<task>" OR "<category>")
```

### Service/agency version

```text
site:reddit.com/r/ ("hired" OR "freelancer" OR "agency" OR "outsourced" OR "paid someone to") ("<task>")
```

Also check r/forhire and r/slavelabour via the Reddit API (see [reddit-api-toolkit.md](reddit-api-toolkit.md) section 8) for concrete prices paid for this job.
````

- [ ] **Step 2: 更新建议查询配比**

将 `## Suggested Query Mix` 小节中的列表：

```text
- 4-5 pain queries
- 2-3 request queries
- 2-3 workaround queries
- 1-2 switching queries
- 1-2 willingness-to-pay queries
```

替换为：

```text
- 4-5 pain queries
- 2-3 request queries
- 2-3 workaround queries
- 1-2 switching queries
- 1-2 willingness-to-pay queries
- 1-2 money-talk queries (revealed spend)
- 3+ falsification queries (mandatory, run as the final pass)
```

- [ ] **Step 3: 在 `## Constraint Examples` 小节末尾（`Do not guess blockers...` 段落之后）追加站内搜索提示**

````markdown
## In-Subreddit Follow-Up (Reddit API)

Once a subreddit is promoted into the candidate list, run the constraint-led queries INSIDE it via the Reddit API for higher precision than search engines:

```bash
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/{subreddit}/search.json?q={blocker phrase}&restrict_sr=on&sort=relevance&t=year&limit=25"
```

See [reddit-api-toolkit.md](reddit-api-toolkit.md) for time filters, comment search, and rate limits.
````

- [ ] **Step 4: 结构校验**

Run: `grep -n '## Falsification\|## Money Talk\|## In-Subreddit Follow-Up\|falsification queries (mandatory' .agents/skills/reddit-demand-validator/query-templates.md`
Expected: 4 类锚点全部命中

- [ ] **Step 5: Commit**

```bash
git add .agents/skills/reddit-demand-validator/query-templates.md
git commit -m "feat(v2): add mandatory falsification and money-talk query families"
```

---

### Task 3: `SKILL.md` — 工作流改造

**Files:**
- Modify: `.agents/skills/reddit-demand-validator/SKILL.md`

- [ ] **Step 1: 更新 frontmatter description**

将 description 末尾的 `...and writes a Chinese markdown research memo with English quotes preserved.` 替换为：

```text
...pre-registers kill criteria before searching, runs a mandatory falsification pass, weights evidence by engagement, and writes a Chinese markdown research memo with English quotes, a riskiest-assumption experiment, and Mom-Test interview questions.
```

- [ ] **Step 2: 在 `## Load These Files` 列表中追加一行**

```markdown
- Read [reddit-api-toolkit.md](reddit-api-toolkit.md) before any direct Reddit API call
```

- [ ] **Step 3: 在 Core Rules 第 11 条之后追加 4 条规则**

```markdown
12. Before running any search, pre-register kill criteria: 2-3 written statements of what evidence would prove the demand weak. The memo must evaluate each one explicitly.
13. A falsification pass is mandatory. A run that only searched for pain language is invalid — note it and rerun the missing queries.
14. Record engagement metadata (score, comment count, date, agreement replies) for every deep-read thread. High-resonance threads carry more weight than bare threads; scoring-rubric.md defines the mechanics.
15. Stated willingness to pay ("take my money") alone can never make the WTP dimension `high`. Only revealed payment evidence (users already paying for substitutes, services, or workarounds) can.
```

- [ ] **Step 4: 在 `## Workflow` 标题之后、`### 1. Normalize the idea` 之前插入 Step 0**

````markdown
### 0. Pre-register kill criteria

Before generating any query, write down:

- 2-3 falsification statements: "If I find X, I will conclude demand is weak." Examples: "If most high-engagement threads conclude existing tools are good enough", "If the only complainers are founders, not problem owners", "If recent threads say general AI already solves this acceptably".
- The strongest counter-hypothesis you can think of (e.g., "this pain was real in 2023 but ChatGPT absorbed it").

These go verbatim into the memo's `预注册的杀死标准` section. Step 8 must evaluate each statement against the evidence and say whether it fired. This is the anti-confirmation-bias spine of the whole skill: you are trying to kill the idea, not confirm it.
````

- [ ] **Step 5: 在 `### 2.5. Refine into constraint-led follow-up search` 小节之后插入证伪轮**

````markdown
### 2.8. Run the falsification pass (mandatory)

After the constraint-led pass, run 3+ falsification queries from [query-templates.md](query-templates.md): satisfaction, good-enough, and AI-absorption versions.

Classify what you find:

- `satisfied`: users say existing tools/workflows solve the problem acceptably
- `still-complaining`: threads that start as praise but surface persistent gaps
- `ai-absorbed`: users report that general AI tools (ChatGPT, Claude, etc.) now handle the task acceptably

Record the falsification hit quality the same way as the query log. The memo must include a `证伪轮结果` section. The scoring rubric defines the falsification gate: when satisfied signals dominate among high-engagement threads, the conclusion is capped below `strong`.
````

- [ ] **Step 6: 扩充 Step 5（线程深读）的元数据采集**

在 `When `.json` works:` 列表（`- Read the OP body` 等三行）之后追加：

```markdown
- Record metadata for the evidence table: `score`, `num_comments`, `upvote_ratio`, `created_utc`
- Count distinct agreement replies in the top comments ("same here", "this exactly", "+1", "I have this exact problem")
```

- [ ] **Step 7: 在 `### 6. Dedupe before judging frequency` 内补充 duplicates 端点**

在 `Count crossposts as one evidence point.` 之后追加一行：

```markdown
When unsure whether two threads are crossposts, confirm via the duplicates endpoint (see [reddit-api-toolkit.md](reddit-api-toolkit.md) section 5).
```

- [ ] **Step 8: 在 Step 6 与 Step 7 之间插入聚类步骤**

````markdown
### 6.5. Cluster pains into named themes

Before classifying evidence quality, group deduplicated threads into named pain clusters:

- A cluster needs 2+ independent mentions to qualify. Single high-quality mentions go to a `弱信号` list, not to clusters.
- Name each cluster with a concrete theme ("pricing is opaque", "export breaks formatting"), not a category label.
- Tag each cluster with one type:
  - `complaint-about-existing`: users complain about a named tool/solution
  - `absence-of-solution`: users describe a job no tool serves
  - `feature-request`: users want an addition to a tool they otherwise accept
- Tag each cluster's solution density:
  - `white-space`: no solutions named at all
  - `execution-gap`: solutions exist but complaints persist (better wedge: UX, price, trust)
  - `saturated`: solutions exist and users sound satisfied

Frequency judgments in Step 8 operate on clusters, not raw threads. Do not conflate feature requests with pain points — label them separately; both are valuable but they are different signals.
````

- [ ] **Step 9: 扩充 Step 8（综合判断）**

在 `Use the dimensions in [scoring-rubric.md](scoring-rubric.md):` 的维度列表中，`- individual recurrence frequency` 之后追加一行：

```markdown
- trend direction
```

在 `The overall conclusion must match the mechanical rubric thresholds. Do not "average" by intuition if the evidence clearly says otherwise.` 段落之后追加：

```markdown
Also in this step:

- Evaluate every pre-registered kill criterion from Step 0: fired / did not fire / partially, each with evidence links.
- Assess trend direction: compare evidence density in the last 12 months vs. 12-36 months (use `t=year` vs `t=all` searches and thread dates). Flag `ai-absorbed` if 2+ independent recent threads report general AI handling the task acceptably.
- If the evidence clearly splits across 2+ distinct user segments with different strength, report a per-segment label for the top 2 segments in `分人群判定`. The overall label still follows the mechanical rubric over all evidence; the segment section tells the reader where the wedge is.
```

- [ ] **Step 10: 替换 Step 9（写备忘录）的章节清单**

将 `The memo must include:` 列表整体替换为：

```markdown
The memo must include:

- a one-line verdict
- overall conclusion label with a confidence level
- pre-registered kill criteria and whether each fired
- who has the pain, with per-segment labels when evidence splits
- 3-5 unmet pains as named clusters (type + solution density)
- current workarounds
- existing solutions inventory
- falsification pass results
- evidence thread table (link, subreddit, date, score, comments, agreement count, bucket, cluster)
- representative verbatim quotes in English, grouped by JTBD role (trigger / failed attempts / emotional language / desired outcome)
- a marketing copy bank: 3-5 user phrases usable in headlines, each with source link
- trend direction note
- query convergence process
- actual query log
- counter-signals and reasons not to build
- business-model implications
- the riskiest assumption and a cheapest-next-experiment design (≤2 weeks, ≤$100, behavioral, with a pre-committed pass/fail threshold and kill criteria)
- a pre-mortem: 3 most probable causes of death if pursued and failed in 12 months
- 5 Mom-Test style interview questions derived from the findings (past behavior, not future intent)
- sample and method notes
- explicit Reddit-only bias note
- crawl completeness note
```

- [ ] **Step 11: 结构校验**

Run: `grep -n '### 0\. Pre-register\|### 2\.8\. Run the falsification\|### 6\.5\. Cluster pains\|trend direction\|Mom-Test' .agents/skills/reddit-demand-validator/SKILL.md`
Expected: 5 类锚点全部命中

Run: `grep -c '^12\. \|^13\. \|^14\. \|^15\. ' .agents/skills/reddit-demand-validator/SKILL.md`
Expected: `4`

- [ ] **Step 12: Commit**

```bash
git add .agents/skills/reddit-demand-validator/SKILL.md
git commit -m "feat(v2): add pre-registration, falsification pass, clustering, trend and RAT to workflow"
```

---

### Task 4: `scoring-rubric.md` — 加权、趋势维度、revealed WTP、置信度、证伪门

**Files:**
- Modify: `.agents/skills/reddit-demand-validator/scoring-rubric.md`

- [ ] **Step 1: 在 `## Overall Rules` 列表末尾追加 3 条**

```markdown
- High-resonance threads (defined below) carry extra weight; a cluster containing one counts as 2 effective threads toward the `strong` threshold (at most 2 clusters per run may use this bonus)
- The falsification gate applies before the conclusion table: if satisfied signals dominate among high-engagement threads found in the falsification pass (roughly 2:1 or worse against pain signals), the conclusion cannot be `strong`
- Every conclusion carries a confidence level (`high` / `medium` / `low`) defined below; a `low` confidence verdict must be labeled directional, not conclusive
```

- [ ] **Step 2: 在 `## Overall Rules` 与 `## Mechanical Conclusion Table` 之间插入两个新小节**

````markdown
## High-Resonance Thread Definition

A deep-read thread is high-resonance when any of these holds:

- `score` ≥ 200, or
- `num_comments` ≥ 100, or
- `score` ≥ 50 AND 5+ distinct agreement replies ("same here", "this exactly", "+1")

For niche subreddits (under 50k subscribers), halve all thresholds. A high-resonance complaint is the community telling you the pain generalizes — it is the cheapest population-frequency signal Reddit offers.

## Confidence Level

| Confidence | Conditions |
|---|---|
| `high` | 8+ primary-evidence threads fully read via `.json`, falsification pass completed, no major fallback usage |
| `medium` | 5-7 primary threads, or notable `old.reddit` fallback usage, or limited comment depth on key threads |
| `low` | Fewer than 5 primary threads, or heavy snippet reliance, or falsification pass incomplete |

`low` confidence requires this wording next to the verdict: 方向性结论，非定论. Confidence describes how much to trust the label; it never changes the label itself.
````

- [ ] **Step 3: 修改机械结论表的 `strong` 行**

将 `strong` 行的 Required evidence pattern 整段替换为：

```text
At least 3 independent subreddits, at least 5 effective threads after dedup and resonance weighting (a cluster containing a high-resonance thread counts as 2, max 2 such bonuses), recurring same or adjacent pain, workaround cost is `medium` or `high`, willingness to pay/switch is at least `medium` or users are actively seeking alternatives, evidence quality is at least `medium`, AND the falsification gate did not fire.
```

- [ ] **Step 4: 在 Tie-breakers 列表末尾追加两条**

```markdown
- If the falsification gate fired, the maximum conclusion is `mixed`, regardless of pain-side evidence volume
- If trend direction is `declining` AND the `ai-absorbed` flag is set, the maximum conclusion is `mixed`; say explicitly that the window for this pain may be closing
```

- [ ] **Step 5: 重写维度 6（付费/切换意愿）**

将 `### 6. Willingness To Pay Or Switch` 整节替换为：

````markdown
### 6. Willingness To Pay Or Switch

Revealed preference (what users already pay) outranks stated preference (what users say they would pay).

- `high`
  - Revealed evidence only: users are already paying for substitutes — inferior tools they keep subscribing to, agencies/freelancers hired for this job, scripts/services they bought, concrete dollar amounts mentioned
  - Anchor examples:
    - "I pay $40/mo for X and it still can't..."
    - "we ended up hiring someone to do this manually"
    - r/forhire postings paying for this exact task
- `medium`
  - Explicit stated willingness ("I'd pay for this", "take my money") or active alternative-seeking because current options are unacceptable
  - Stated-only evidence can never exceed `medium`
- `low`
  - Users say current tools are good enough, not worth paying for, or not worth changing
````

- [ ] **Step 6: 在维度 7（Evidence Quality）之后新增维度 8**

````markdown
### 8. Trend Direction

Compare evidence density across time windows (threads from the last 12 months vs. 12-36 months, via `t=year` / `t=all` searches and thread dates).

- `rising`
  - Clearly more recent threads; new subreddits or new vocabulary forming around the pain
- `stable`
  - Similar density across windows; long-lived recurring pain
- `declining`
  - Mostly old threads; recent discussion sparse or resolved

Additionally set the `ai-absorbed` flag when 2+ independent threads from the last 12 months report that general AI tools now handle the task acceptably. `declining` + `ai-absorbed` caps the overall conclusion at `mixed` (see tie-breakers). A `rising` trend strengthens the build-now case and belongs in the memo's trend note.
````

- [ ] **Step 7: 结构校验**

Run: `grep -n 'High-Resonance Thread Definition\|## Confidence Level\|falsification gate\|### 8\. Trend Direction\|Stated-only evidence can never exceed' .agents/skills/reddit-demand-validator/scoring-rubric.md`
Expected: 5 类锚点全部命中

- [ ] **Step 8: Commit**

```bash
git add .agents/skills/reddit-demand-validator/scoring-rubric.md
git commit -m "feat(v2): resonance weighting, falsification gate, revealed WTP, trend dimension, confidence"
```

---

### Task 5: `report-template.md` — 备忘录模板大改

**Files:**
- Modify: `.agents/skills/reddit-demand-validator/report-template.md`

- [ ] **Step 1: 更新头部元信息**

将模板开头的：

```markdown
- 结论等级: <strong|mixed|weak>
```

替换为：

```markdown
- 结论等级: <strong|mixed|weak>
- 置信度: <high|medium|low><low 时追加：（方向性结论，非定论）>
- 数据观测时间: <YYYY-MM>
```

- [ ] **Step 2: 在 `## 一句话结论` 小节之后插入预注册与证伪章节**

````markdown
## 预注册的杀死标准

搜索开始前写下的证伪声明，以及它们是否被触发：

1. <杀死标准 1> — <触发 / 未触发 / 部分触发>：<1-2 句证据说明 + links>
2. <杀死标准 2> — <触发 / 未触发 / 部分触发>：<说明 + links>
3. <最强反向假设> — <成立 / 不成立>：<说明>

## 证伪轮结果

- 实际跑的证伪查询: `<query 1>`, `<query 2>`, `<query 3>`
- 满意信号 (`satisfied`): <N 条线程，要点 + links>
- 抱怨持续 (`still-complaining`): <N 条，要点 + links>
- AI 吞噬信号 (`ai-absorbed`): <N 条，要点 + links；无则写"未发现">
- 证伪门是否触发: <是/否>，<对结论等级的影响>
````

- [ ] **Step 3: 扩充人群章节，支持分人群判定**

在 `## 这个需求主要出现在谁身上` 的 `<用中文总结 2-4 类人群或场景。不要编造 persona。>` 之后追加：

```markdown
### 分人群判定（仅当证据明显分裂时填写）

- <人群 1>: <strong|mixed|weak> — <1-2 句依据 + links>
- <人群 2>: <strong|mixed|weak> — <依据 + links>
- 建议切入点: <信号最强的那个人群及理由>
```

- [ ] **Step 4: 改写痛点章节为聚类格式**

将 `## 最强 3-5 个未满足痛点` 的两个条目模板替换为：

```markdown
1. <痛点聚类名（具体主题，非品类标签）>
   - 类型: <对现有方案的抱怨 | 无解决方案的空白 | 功能请求>
   - 方案密度: <空白 | 执行差距 | 饱和>
   - 为什么这是痛点: <中文说明>
   - 聚类规模: <N 条独立线程，其中高共鸣 M 条>
   - 证据: <thread link 1>, <thread link 2>
2. <痛点聚类名>
   - 类型: <...>
   - 方案密度: <...>
   - 为什么这是痛点: <...>
   - 聚类规模: <...>
   - 证据: <links>

弱信号清单（单次出现但质量高，不计入聚类）：

- <信号> — <link>
```

- [ ] **Step 5: 在 `## 现有解决方案盘点` 小节之后插入证据线程表**

````markdown
## 证据线程表

| 线程 | subreddit | 日期 | 赞 | 评论数 | 附议数 | 证据等级 | 所属聚类 |
|---|---|---|---|---|---|---|---|
| [标题截断](link) | r/xxx | YYYY-MM | 312 | 145 | 8 | primary | <聚类名> |

附议数 = 评论区 "same here / this exactly / +1" 类回复的去重计数。高共鸣线程（定义见 scoring-rubric）将线程名加粗标注。
````

- [ ] **Step 6: 重构原话章节为 JTBD 分组 + 文案弹药库**

将 `## 代表性原话` 整节（含两个引用块示例）替换为：

````markdown
## 代表性原话（按 JTBD 角色分组）

共 3-7 条，优先来自不同 subreddit 和不同聚类。

### 触发场景（"每当…的时候"）

> "<English quote>"
>
> Source: <thread or comment link> ｜ 中文释义: <可选>

### 失败的尝试（"我试过…但…"）

> "<English quote>"
>
> Source: <link>

### 情绪强度（原话的愤怒/疲惫感）

> "<English quote>"
>
> Source: <link>

### 期望结果（"我只想要…"）

> "<English quote>"
>
> Source: <link>

## 营销文案弹药库

可直接用于落地页标题、广告语的用户原话片段（3-5 条，保持英文原文，附中文使用建议）：

1. "<短语>" — <建议用法：标题/副标题/广告首句> — <source link>
2. "<短语>" — <建议用法> — <source link>
````

- [ ] **Step 7: 在维度判断小节追加趋势方向**

在 `- 证据质量: <高|中|低>` 条目（含其依据与证据两行）之后追加：

```markdown
- 趋势方向: <rising|stable|declining>
  - ai-absorbed 标记: <是/否>
  - 依据: <近12个月 vs 12-36个月的证据密度对比，1-2 句>
  - 证据: <links>
```

- [ ] **Step 8: 在 `## 建议下一步验证什么` 之前插入 RAT、Pre-mortem、访谈问题三个章节**

````markdown
## 最危险假设与最便宜的下一步实验

- 最危险假设: "<如果它是错的，整个想法就死掉的那一条假设>"
  - 类别: <痛点真实性 | 付费意愿 | 触达渠道 | 复发频次>
  - 为什么最危险: <criticality 高 × 现有证据少>
- 最便宜实验（≤2 周，≤$100，测行为不测口头）:
  - 做什么: <例：落地页 + 邮箱收集，定向投到 r/xxx；或 5 个 Mom Test 访谈；或带价格的假门测试>
  - 通过线: <事前定死的数字，如"100 个定向访客中 ≥10% 留邮箱"；禁止"效果不错"这类模糊表述>
  - 杀死标准: <什么结果出现就放弃，如"200 访客转化 <5% 即放弃此方向">
- 失败后的备选路径: <一句话>

## Pre-mortem：如果 12 个月后失败了

假设做了并且失败了，最可能的 3 个死因（必须挂到具体维度和证据，不许写"没钱了"这类空话）：

1. <死因 1，关联维度 + 证据>
2. <死因 2>
3. <死因 3>

## 如果要做用户访谈，先问这 5 个问题

从本次调研发现生成的 Mom Test 风格问题（问过去行为，不问未来意愿，不提自己的产品）：

1. <"你上次遇到 <具体痛点> 是什么时候？当时发生了什么？">
2. <"你当时试了什么？哪些有用哪些没用？">
3. <"这件事多久发生一次？相比你手头其他麻烦，它排第几？">
4. <"你现在为解决它花了什么钱或时间？">
5. <"你身边还有谁也被这个问题困扰？">
````

- [ ] **Step 9: 在 `## 样本与方法说明` 列表中追加三行**

在 `- 低信号求助帖处理: ...` 之后追加：

```markdown
- 证伪轮说明: <跑了几条证伪查询，命中情况>
- 共鸣加权说明: <哪些聚类使用了高共鸣 ×2 加权，最多 2 个>
- 置信度依据: <为什么是 high/medium/low>
```

- [ ] **Step 10: 结构校验（与 SKILL.md Step 10 的章节清单逐项核对）**

Run: `grep -n '## 预注册的杀死标准\|## 证伪轮结果\|## 证据线程表\|## 营销文案弹药库\|## 最危险假设\|## Pre-mortem\|先问这 5 个问题\|趋势方向\|分人群判定' .agents/skills/reddit-demand-validator/report-template.md`
Expected: 9 类锚点全部命中

- [ ] **Step 11: Commit**

```bash
git add .agents/skills/reddit-demand-validator/report-template.md
git commit -m "feat(v2): memo template — pre-registration, falsification, evidence table, JTBD quotes, RAT, pre-mortem"
```

---

### Task 6: `README.md` — V2 说明

**Files:**
- Modify: `README.md`

- [ ] **Step 1: 在 `## What It Produces` 的 memo 清单中追加 V2 产出**

在 `- Reddit-only bias warning` 之后追加：

```markdown
- Pre-registered kill criteria with fired/not-fired evaluation
- Falsification pass results (satisfied / still-complaining / ai-absorbed signals)
- Evidence thread table with engagement metadata (score, comments, agreement count)
- Quotes grouped by JTBD role + a marketing copy bank
- Trend direction with AI-absorption flag
- Riskiest-assumption experiment (≤2 weeks, ≤$100, pre-committed pass/fail threshold)
- Pre-mortem and 5 Mom-Test interview questions
- Confidence level on every verdict
```

- [ ] **Step 2: 在 `## How The Scoring Works` 小节末尾追加**

```markdown
### What's new in v2

v1 only searched for complaints — which exist for any topic. v2 makes the skill try to kill the idea first:

- Kill criteria are written down BEFORE searching, and evaluated in the memo
- A mandatory falsification pass searches for satisfied users and AI-absorbed workflows; dominant satisfaction caps the verdict below `strong`
- Engagement matters: a 300-upvote complaint with dozens of "same here" replies outweighs five 2-upvote threads
- Willingness to pay requires revealed evidence (users already paying for substitutes); "take my money" comments alone max out at `medium`
- Every verdict carries a confidence level, and the memo ends with the single riskiest assumption plus the cheapest behavioral experiment to test it
```

- [ ] **Step 3: 更新 Files 清单**

在 `## Files` 代码块中 `  scoring-rubric.md` 之后追加一行 `  reddit-api-toolkit.md`。

- [ ] **Step 4: Commit**

```bash
git add README.md
git commit -m "docs(v2): document falsification-first methodology in readme"
```

---

### Task 7: 校准测试（golden runs）

V2 的验收标准：跑两个已知答案的想法，看结论是否符合预期。这是防止"什么都能验出需求"的最终防线。

**Files:**
- Create: `tasks/calibration-v2.md`
- Create: `research/reddit-demand/`（两份校准 memo）

- [ ] **Step 1: 用升级后的 skill 完整跑伪需求样本**

输入想法：`An app that recommends toothbrush colors based on your personality`（已知伪需求）。
按 SKILL.md V2 完整流程执行（含 Step 0 预注册与 2.8 证伪轮），产出 memo 到 `research/reddit-demand/`。

预期：结论 `weak`。如果得到 `mixed` 或更高，说明证伪门或聚类阈值有漏洞——回到 scoring-rubric.md 修正后重跑。

- [ ] **Step 2: 跑已知真实痛点样本**

输入想法：`A tool that helps freelancers chase overdue invoices and late-paying clients`（已知真实、长期、有付费行为的痛点）。

预期：结论 `strong` 或 `mixed`（不低于 mixed），且证据表包含高共鸣线程、money-talk 查询应找到已付费证据（催收服务、保理、会计外包等）。

- [ ] **Step 3: 记录校准结果**

创建 `tasks/calibration-v2.md`：

```markdown
# V2 校准测试记录

## 样本 1：牙刷颜色推荐（预期 weak）
- 实际结论: <label> / 置信度: <level>
- 证伪门触发: <是/否>
- 结论是否符合预期: <是/否>，<不符时的修正记录>

## 样本 2：自由职业者催款（预期 ≥ mixed）
- 实际结论: <label> / 置信度: <level>
- 高共鸣线程数: <N>
- 已付费证据: <找到/未找到，举例>
- 结论是否符合预期: <是/否>

## 修正记录
- <规则漏洞与对应 commit>
```

- [ ] **Step 4: Commit**

```bash
git add tasks/calibration-v2.md research/
git commit -m "test(v2): calibration runs — fake idea vs known-real pain"
```

---

### Task 8: `tasks/v2.1-backlog.md` — 多平台适配器蓝图（只写蓝图，不实施）

V2 刻意保持 Reddit-only 以便快速落地。但用户的最终目的是"任何新项目的消费者调研"，多平台是 v2.1 的方向。本任务只产出一页蓝图文档，防止过度工程。

**Files:**
- Create: `tasks/v2.1-backlog.md`

- [ ] **Step 1: 写入以下内容**

```markdown
# v2.1 Backlog — 多平台证据适配器

核心洞察：本 skill 真正可复用的是"证据流水线"（三档分级 → 去重 → 聚类 → 共鸣加权 → 证伪门 → 机械结论），它与 Reddit 无关。v2.1 把采集层做成可插拔适配器，流水线不变。

## 候选适配器（按证据信号强度排序）

1. **App Store / G2 / Capterra 差评适配器** — 现有竞品的 1-3 星差评 = 已付费用户的真实未满足需求，信号强度高于论坛抱怨。采集字段：星级、日期、版本、评论文本。
2. **小红书 / 知乎适配器** — 中文市场必备。本机已装 agent-reach skill（支持小红书搜索与评论），采集层直接调用它。
3. **YouTube 评论适配器** — 教程视频下的 "why is there no tool that..." 评论；agent-reach 的 yt-dlp 通道可用。
4. **Hacker News 适配器** — Algolia API 免费全文搜索，开发者向产品的高信号来源。

## 跨平台交叉验证规则（草案）

- 同一痛点聚类在 ≥2 个独立平台出现 → 等效于一条高共鸣加权
- 平台偏差互补声明：Reddit（西方/技术/男性偏置）+ 小红书（中国/消费/女性偏置）+ 差评（已付费用户）
- 各平台各自跑证伪轮，任一平台证伪门触发都要在备忘录中单独披露

## 明确不做

- 不做爬虫框架、不做数据库、不做前端看板
- 不引入需要付费 API key 的渠道作为硬依赖
```

- [ ] **Step 2: Commit**

```bash
git add tasks/v2.1-backlog.md
git commit -m "docs(v2): record v2.1 multi-platform adapter blueprint"
```

---

## 验收清单（全部完成后自查）

- [ ] `grep -rni 'falsification' .agents/skills/reddit-demand-validator/ | wc -l` ≥ 8（证伪概念贯穿四个文件）
- [ ] SKILL.md Step 9 章节清单与 report-template.md 中文标题逐项对应（人工核对）
- [ ] scoring-rubric 的 8 个维度名与 report-template 维度判断小节一致
- [ ] 校准测试：伪需求 → weak；真实痛点 → ≥ mixed
- [ ] git log 显示 8 个 feat/docs/test commit，无未提交改动
