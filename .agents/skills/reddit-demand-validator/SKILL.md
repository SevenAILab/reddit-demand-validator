---
name: reddit-demand-validator
description: Use when the user wants to validate a project, startup, feature, or market idea by researching real user demand, unmet pain points, workarounds, and verbatim language on Reddit. Best for early-stage idea validation, problem discovery, and pre-build demand checks. Prefers search-engine site:reddit.com discovery over Reddit internal search, deep-reads threads with JSON and old.reddit fallback, filters noisy evidence, and writes a Chinese markdown research memo with English quotes preserved.
---

# Reddit Demand Validator

Use this skill when someone says things like:

- "Validate this idea on Reddit"
- "Check whether users actually want this"
- "Find unmet pain points for this project"
- "Research real user language and complaints before I build"
- "See if this topic has demand on Reddit"

## Inputs

Ask for only one thing if it is missing:

- The idea or problem to validate

Do not proactively ask for ICP, persona, geography, pricing, or business model. If the user already provided any of that context, use it to sharpen the queries.

## Output

- Write the final memo to `research/reddit-demand/<slug>-<YYYY-MM-DD>.md`
- Create the directory if needed
- Write the memo in Chinese
- Preserve representative Reddit quotes in English
- Include source links throughout the memo

If local file writing is unavailable for any reason, return the memo in chat and explicitly mention the intended output path.

## Load These Files

- Read [query-templates.md](query-templates.md) before generating search queries
- Read [scoring-rubric.md](scoring-rubric.md) before assigning conclusion strength
- Use [report-template.md](report-template.md) as the output structure

## Core Rules

1. Use search-engine `site:reddit.com` discovery, not Reddit internal search, for the first pass.
2. Discover the right subreddits from user pain language first; do not start from product-category jargon unless the user already framed the problem that way.
3. Prefer communities where problem owners talk, not communities where founders talk.
4. Use only deduplicated evidence when judging frequency or breadth.
5. Use only two evidence statuses for questionable material:
   - `excluded`: do not count or cite it as support
   - `weak evidence`: may be cited, but cannot be the sole support for any dimension or overall conclusion
6. Never give a numeric score. The only overall demand-strength labels are `strong`, `mixed`, and `weak`.
7. After the first-pass search, run a second pass using the concrete blockers users actually mention. Refine toward constraint-led queries instead of staying at the category level.
8. Threads that mostly ask strangers to solve the problem directly, but do not contain concrete constraints, failed attempts, or stakes, default to `weak evidence`.
9. Record the actual search queries used. The memo must be auditable enough to debug or rerun.
10. Every key judgment must point to concrete thread links, and usually at least one quote.
11. The report must explicitly say it is based only on Reddit and may reflect Reddit's platform bias.

## Demand vs Business Model

This skill validates whether demand is real and evidenced on Reddit. It does not decide whether the idea is a good SaaS, marketplace, service, or venture-scale business.

Keep these separate:

- Demand strength: whether real users show repeated pain, workaround cost, active search, or willingness to pay/switch
- Business-model implications: whether the pain is recurring enough for subscription, whether the audience is narrow, whether ACV/LTV may be low, or whether acquisition may be hard

Do not downgrade `strong` demand because the problem is episodic, low-repeat, or hard to monetize. Put those observations in `商业模式提示`, `反向信号`, or `建议下一步验证什么`.

## Workflow

### 1. Normalize the idea

Translate the idea into 5 query inputs:

- `task`: what the user is trying to do
- `outcome`: the result they want
- `problem`: the frustrating part of the current workflow
- `current_tool`: the tool or workaround they may use today
- `category`: the broad category label, if useful

Create 3-6 keyword variants for each input. Favor concrete user language over startup language.

### 2. Run first-pass search

Use [query-templates.md](query-templates.md) to build 10-15 search-engine queries. Start with:

- `pain`
- `request`
- `workaround`

Add `switching` and `wtp` once you have early signals.

The first pass should answer:

- Which subreddits repeatedly surface this pain?
- Are the complaints coming from real problem owners?
- Is the topic recent enough to matter now?

Maintain a query log as you go:

- exact query string
- search stage: `broad exploration` or `constraint-led`
- rough hit quality: useful / mixed / poor
- blockers or subreddits discovered from the query

### 2.5. Refine into constraint-led follow-up search

Do not stop at the broad category query if the results are noisy.

From the first pass, extract 3-6 concrete blockers or decision constraints users mention, then run a narrower follow-up search round using those blockers.

Examples of blocker types:

- legal or compliance
- pricing or budget
- integrations or migration
- reliability or performance
- approvals or collaboration
- discoverability or distribution
- availability or ownership checks

Use [query-templates.md](query-templates.md) for a constraint-led second pass. This is often where the highest-signal threads appear.

The final memo must include a visible `查询收敛过程` section with:

- first-pass queries
- hit quality or hit rate
- blockers extracted from first-pass evidence
- second-pass constraint-led queries

### 3. Build the candidate subreddit list

Promote a subreddit when at least one of these is true:

- Similar pain appears there at least twice
- Similar workaround patterns appear there repeatedly
- The thread authors are clearly problem owners, not founders or marketers

Demote founder-centric communities. By default, do not use these as primary evidence sources:

- `r/Entrepreneur`
- `r/SideProject`
- `r/startups`
- `r/SaaS`

You may cite at most one thread from each of those communities, and only if it contains specific, high-signal pain details.

By default, exclude a subreddit from the main evidence pool if either is true:

- Subscriber count is below `5k`
- Search results suggest there has been little or no relevant activity in the last 6 months

Try `https://www.reddit.com/r/<subreddit>/about.json` to confirm subscriber count and metadata. If that is unavailable, infer activity from visible recent results and note that subreddit metadata could not be confirmed.

### 4. Select threads for deep reading

Target 8-12 deep-read threads total.

Prioritize:

- Threads from the last 12 months
- Threads with concrete scenarios, not vague discussion
- Threads with evidence of repeated pain, workaround, switching, or willingness to pay
- Threads with active comments, because workaround details often appear there

If strong recent evidence is sparse, expand to 24 months and say so in the memo.

If you still cannot find 8 strong threads, continue with fewer and explicitly note `effective thread count is limited`.

### 5. Read each thread with fallback

For each selected thread, use this exact fallback order:

1. Try `https://www.reddit.com/.../.json`
2. If JSON fails, returns a block page, 403, or non-structured content, try `https://old.reddit.com/...`
3. If `old.reddit.com` also fails or is unreadable, fall back to the search-result snippet only

Never stop the overall workflow because one thread is blocked.

If your primary browsing tool cannot open `old.reddit.com` even though the URL itself is live, use a secondary fetch mechanism if one is available, such as a shell HTTP request or another page-fetching tool. Only fall back to search snippets after both the canonical JSON path and a genuine HTML fallback attempt have failed.

When `.json` works:

- Read the OP body
- Read up to the top 20 comments by score
- Expand up to 5 promising second-level reply chains when the thread actually has depth

When only `old.reddit.com` works:

- Read the visible OP body
- Read visible top-level comments
- Use visible replies when available
- Mark that comment-depth evidence is limited

If the thread is shallow and does not contain enough second-level discussion, do not force a quota. Record that comment depth was limited and continue.

When only search snippets are available:

- Mark the thread as `weak evidence`
- Do not use it as the sole support for any judgment
- Record in the memo that full thread content could not be read

### 6. Dedupe before judging frequency

Treat evidence as clusters, not raw thread counts.

Count crossposts as one evidence point.

Treat threads as the same evidence cluster when both are true:

- Titles are nearly identical, or clearly refer to the same original post/news/event
- The first 200 characters of the OP body are highly similar, or one is obviously a repost of the other

When duplicates exist:

- Keep the most complete thread as the primary evidence
- Keep the others only as secondary notes showing spread
- Do not let duplicates increase the frequency score

### 7. Classify evidence quality

Every thread must end up in one of these buckets:

- `primary evidence`
- `weak evidence`
- `excluded`

Use `excluded` for:

- Obvious self-promotion
- Giveaway or lead-gen posts
- Pure title bait with no real body
- Empty or deleted content with no usable context
- Duplicate reposts with no new information
- Clear bot or automated posting

Use `weak evidence` for:

- Suspected AI-generated writing that still contains concrete details
- Very high score but suspiciously low discussion
- Threads from founder/builder communities
- Threads where only snippets were accessible
- Authors whose accounts look unusually new, narrow, or promotional
- Threads that ask the community to solve the entire problem directly, but provide little or no concrete context, failed attempts, constraints, or consequences

`Weak evidence` may reinforce a pattern, but it cannot be the only support for:

- any dimension rating
- the overall `strong` conclusion
- claims about willingness to pay

If an author looks suspicious and the thread matters, you may check `https://www.reddit.com/user/<author>/about.json`. Use that only for filtering; do not make author identity part of the report.

### 8. Synthesize demand and unmet need

Before rating the evidence, extract named incumbent or workaround solutions from the evidence:

- tools, products, platforms, agencies, service providers, communities, spreadsheets, scripts, manual processes, or "ask AI" workflows
- user complaints about those solutions
- whether each solution is free/low-friction, paid software, service/agency, or manual workaround

If users name no concrete existing solutions, say so. Do not invent competitors from general knowledge unless the user explicitly asks for broader market research beyond Reddit.

Use the dimensions in [scoring-rubric.md](scoring-rubric.md):

- pain intensity
- population frequency
- workaround cost
- incumbent maturity
- willingness to pay or switch
- evidence quality
- individual recurrence frequency

Assign `high`, `medium`, or `low` to each dimension and back each one with 1-3 links.

Then choose only one overall conclusion:

- `strong`
- `mixed`
- `weak`

The overall conclusion must match the mechanical rubric thresholds. Do not "average" by intuition if the evidence clearly says otherwise.

Individual recurrence frequency is reported for business-model interpretation. It should not lower the demand-strength conclusion when the demand evidence otherwise meets `strong`.

### 9. Write the memo

Use [report-template.md](report-template.md) exactly as the base shape.

The memo must include:

- a one-line verdict
- overall conclusion label
- who has the pain
- 3-5 unmet pains
- current workarounds
- existing solutions inventory
- representative verbatim quotes in English
- query convergence process
- actual query log
- counter-signals and reasons not to build
- business-model implications
- next validation questions
- sample and method notes
- explicit Reddit-only bias note
- crawl completeness note

## Query and Evidence Heuristics

### Good signals

- Users describe the same pain independently across multiple subreddits
- Users mention ugly, manual, or time-consuming workarounds
- Users ask for recommendations because existing tools do not fit
- Users say they switched tools, want alternatives, or would pay for relief
- Replies contain specifics about costs, breakage, edge cases, or workflow pain
- The OP names concrete blockers, prior failed attempts, or real decision constraints

### Bad signals

- Generic founder chatter with no lived detail
- Threads about trends, markets, or startup ideas instead of actual workflows
- Threads where everyone quickly agrees existing tools are already good enough
- Old threads with no recent activity and no modern equivalents
- Threads that are mostly "pick one for me" or "just tell me what to do" with no constraints, no failed attempts, and no real stakes

## Guardrails

- Prefer recent, user-owned pain over clever product positioning
- Prefer 3 good threads over 20 noisy threads
- Quote sparingly and precisely; pick the lines that best reveal the user's real language
- If the evidence is weak or mixed, say so directly
- Do not force a positive case for building
