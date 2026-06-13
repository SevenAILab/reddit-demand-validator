# Reddit Demand Validation Memo

Use this structure for the final output file. Keep the headings in Chinese.

```markdown
# <idea> 的 Reddit 需求验证

- 研究日期: <YYYY-MM-DD>
- 输出路径: `research/reddit-demand/<slug>-<YYYY-MM-DD>.md`
- 结论等级: <strong|mixed|weak>
- 置信度: <high|medium|low><low 时追加：（方向性结论，非定论）>
- 数据观测时间: <YYYY-MM>

## 一句话结论

<1-2 句中文结论。先回答需求证据强不强，不要把商业模式难度混进结论等级。>

## 预注册的杀死标准

搜索开始前写下的证伪声明，以及它们是否被触发：

1. <杀死标准 1>：<触发 / 未触发 / 部分触发>，<1-2 句证据说明 + links>
2. <杀死标准 2>：<触发 / 未触发 / 部分触发>，<说明 + links>
3. <最强反向假设>：<成立 / 不成立>，<说明>

## 证伪轮结果

- 实际跑的证伪查询: `<query 1>`, `<query 2>`, `<query 3>`
- 满意信号 (`satisfied`): <N 条线程，要点 + links>
- 抱怨持续 (`still-complaining`): <N 条，要点 + links>
- AI 吞噬信号 (`ai-absorbed`): <N 条，要点 + links；无则写"未发现">
- 证伪门是否触发: <是/否>，<对结论等级的影响>

## 这个需求主要出现在谁身上

<用中文总结 2-4 类人群或场景。不要编造 persona。>

### 分人群判定（仅当证据明显分裂时填写）

- <人群 1>: <strong|mixed|weak> - <1-2 句依据 + links>
- <人群 2>: <strong|mixed|weak> - <依据 + links>
- 建议切入点: <信号最强的那个人群及理由>

## 最强 3-5 个未满足痛点

1. <痛点聚类名（具体主题，非品类标签）>
   - 类型: <对现有方案的抱怨 | 无解决方案的空白 | 功能请求>
   - 方案密度: <空白 | 执行差距 | 饱和>
   - 为什么这是痛点: <中文说明>
   - 聚类规模: <N 条独立线程，其中高共鸣 M 条>
   - 证据: <thread link 1>, <thread link 2>
2. <痛点聚类名>
   - 类型: <...>
   - 方案密度: <...>
   - 为什么这是痛点: <中文说明>
   - 聚类规模: <...>
   - 证据: <links>

弱信号清单（单次出现但质量高，不计入聚类）：

- <信号> - <link>

## 用户当前怎么 workaround

- <当前替代或 workaround 1>
  - 代价: <时间/复杂度/错误率/上下文切换等>
  - 证据: <thread link>
- <当前替代或 workaround 2>
  - 代价: <说明>
  - 证据: <thread link>

## 现有解决方案盘点

- 免费/低门槛方案: <ChatGPT, search, community asking, spreadsheets, open-source tools, etc. if named in evidence>
  - 用户主要不满: <what users say is missing or broken>
  - 引用线程: <links>
- 付费工具或产品: <named tools/products if users mention them>
  - 用户主要不满: <price, quality, setup, coverage, trust, etc.>
  - 引用线程: <links>
- 服务/外包/人工方案: <agencies, consultants, freelancers, expert services if mentioned>
  - 用户主要不满: <cost, deliverable quality, turnaround, trust, etc.>
  - 引用线程: <links>
- 未被现有方案满足的空白: <1-3 concise gaps>

## 证据线程表

| 线程 | subreddit | 日期 | 赞 | 评论数 | 附议数 | 证据等级 | 所属聚类 |
|---|---|---|---|---|---|---|---|
| [标题截断](link) | r/xxx | YYYY-MM | 312 | 145 | 8 | primary | <聚类名> |

附议数 = 评论区 "same here / this exactly / +1" 类回复的去重计数。高共鸣线程（定义见 scoring-rubric）将线程名加粗标注。

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

1. "<短语>" - <建议用法：标题/副标题/广告首句> - <source link>
2. "<短语>" - <建议用法> - <source link>

## 维度判断

- 痛感强度 (Pain Intensity): <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 人群频次 (Population Frequency): <高|中|低>
  - 依据: <how widely this appears across independent users/subreddits>
  - 证据: <links>
- 个体复发频次 (Individual Recurrence Frequency): <高|中|低>
  - 依据: <how often the same user likely faces the problem again>
  - 业务含义: <subscription / one-time / service / marketplace / lead-gen implication>
  - 证据: <links or inference from use case>
- Workaround 成本 (Workaround Cost): <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 现有替代成熟度 (Incumbent Maturity): <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 付费/切换意图 (Willingness To Pay Or Switch): <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 证据质量 (Evidence Quality): <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 趋势方向 (Trend Direction): <rising|stable|declining>
  - ai-absorbed 标记: <是/否>
  - 依据: <近12个月 vs 12-36个月的证据密度对比，1-2 句>
  - 证据: <links>

## 商业模式提示

- <Explain recurrence, audience breadth, buying motion, LTV/ACV risk, or channel implications. These notes must not change the demand-strength label.>

## 反向信号与不做的理由

- <反向信号 1>
  - 证据: <link>
- <反向信号 2>
  - 证据: <link>

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

## 建议下一步验证什么

- <应该继续访谈或验证的具体问题 1>
- <应该继续验证的渠道、定价、切入场景或用户段 2>
- <如果暂时不做，下一次该看什么 3>

## 样本与方法说明

- 研究范围: <只基于 Reddit>
- 搜索方式: <search-engine site:reddit.com queries>
- 搜索主题: <简述 task/problem/current tool variants>
- 深读线程数: <N>
- 候选 subreddit: <list>
- 纳入主证据的 subreddit: <list>
- 时间窗口: <last 12 months or expanded to 24 months>
- 去重说明: <crossposts and near-duplicates counted once>
- 弱证据说明: <how many weak-evidence threads were cited, if any>
- 低信号求助帖处理: <how direct-solve / low-context threads were downgraded or excluded>
- 证伪轮说明: <跑了几条证伪查询，命中情况>
- 共鸣加权说明: <哪些聚类使用了高共鸣 ×2 加权，最多 2 个>
- 置信度依据: <为什么是 high/medium/low>

## 查询收敛过程

### 第一轮: 广泛探索

- 实际跑的查询:
  1. `<query>`
  2. `<query>`
- 命中率或命中质量: <N/M useful, or useful/mixed/poor with short rationale>
- 提取出的具体阻碍: <blocker 1>, <blocker 2>, <blocker 3>
- 提取出的候选 subreddit: <subreddit list>

### 第二轮: 约束驱动

- 基于第一轮阻碍生成的查询:
  1. `<query>`
  2. `<query>`
- 命中率或命中质量: <N/M useful, or useful/mixed/poor with short rationale>
- 第二轮新增或强化的发现: <short notes>

## 实际查询日志

List 8-12 representative exact query strings. Include both broad exploration and constraint-led queries.

1. `<query>`
2. `<query>`
3. `<query>`

## 抓取完整性说明

If all selected threads were readable through `.json`, use one concise line:

- 所有纳入主样本的线程均成功读取 `.json` 全文与评论；未触发 fallback。

If any fallback was needed, use the expanded format:

- 读到 `.json` 全文与评论的线程: <count or list>
- 回退到 `old.reddit` 的线程: <count or list>
- 只能使用搜索摘要的线程: <count or list>
- 评论层证据受限的地方: <short note>
- 使用了哪些兜底抓取方式: <json / old.reddit / other fetch path>

## 局限性

- 本结论仅基于 Reddit，不代表全部市场。
- Reddit 样本可能偏技术、英语、西方和特定性别结构。
- 若目标市场是 B2B、女性向、非英语地区或低线上讨论行业，需要补充其他渠道验证。
```

## Writing Notes

- The memo should read like a decision memo, not a link dump
- Use short, direct Chinese prose
- Preserve English quotes exactly
- If evidence is weak, say it plainly
- Mark any `weak evidence` inline when it materially affects a conclusion
