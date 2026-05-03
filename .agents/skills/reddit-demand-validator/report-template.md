# Reddit Demand Validation Memo

Use this structure for the final output file. Keep the headings in Chinese.

```markdown
# <idea> 的 Reddit 需求验证

- 研究日期: <YYYY-MM-DD>
- 输出路径: `research/reddit-demand/<slug>-<YYYY-MM-DD>.md`
- 结论等级: <strong|mixed|weak>

## 一句话结论

<1-2 句中文结论。先回答需求证据强不强，不要把商业模式难度混进结论等级。>

## 这个需求主要出现在谁身上

<用中文总结 2-4 类人群或场景。不要编造 persona。>

## 最强 3-5 个未满足痛点

1. <痛点标题>
   - 为什么这是痛点: <中文说明>
   - 证据: <thread link 1>, <thread link 2>
2. <痛点标题>
   - 为什么这是痛点: <中文说明>
   - 证据: <thread link 1>, <thread link 2>

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

## 代表性原话

Pick 3-7 quotes total. Prefer quotes from different subreddits and different evidence clusters.

> "<English quote>"
>
> Source: <thread or comment link>
>
> 中文释义: <optional short Chinese gloss>

> "<English quote>"
>
> Source: <thread or comment link>

## 维度判断

- 痛感强度: <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 人群频次: <高|中|低>
  - 依据: <how widely this appears across independent users/subreddits>
  - 证据: <links>
- 个体复发频次: <高|中|低>
  - 依据: <how often the same user likely faces the problem again>
  - 业务含义: <subscription / one-time / service / marketplace / lead-gen implication>
  - 证据: <links or inference from use case>
- Workaround 成本: <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 现有替代成熟度: <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 付费/切换意图: <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>
- 证据质量: <高|中|低>
  - 依据: <1-2 句>
  - 证据: <links>

## 商业模式提示

- <Explain recurrence, audience breadth, buying motion, LTV/ACV risk, or channel implications. These notes must not change the demand-strength label.>

## 反向信号与不做的理由

- <反向信号 1>
  - 证据: <link>
- <反向信号 2>
  - 证据: <link>

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
