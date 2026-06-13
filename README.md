# Reddit Demand Validator

Reddit Demand Validator is a lightweight Codex/agent skill for validating startup, product, feature, and content ideas against real Reddit conversations.

It helps answer:

- Do real users talk about this problem?
- Which communities contain the highest-signal evidence?
- What unmet pains, workarounds, and objections show up repeatedly?
- What are users' own words?
- Is the demand evidence `strong`, `mixed`, or `weak`?
- What business-model risks are separate from demand strength?

The skill is intentionally small: no CLI, no runtime, no API keys required for v1. It uses search-engine `site:reddit.com` discovery, Reddit thread `.json` reads when available, `old.reddit.com` fallback, evidence filtering, query logs, and a structured Chinese markdown memo.

## What It Produces

Each run writes a memo to:

```text
research/reddit-demand/<slug>-<YYYY-MM-DD>.md
```

The memo includes:

- One-line demand verdict
- `strong` / `mixed` / `weak` demand label
- Who has the pain
- 3-5 unmet pain points
- Current workarounds
- Existing solutions inventory
- Representative English quotes
- Dimension ratings, including population frequency and individual recurrence frequency
- Business-model notes
- Counter-signals
- Query convergence process
- Actual query log
- Crawl completeness notes
- Reddit-only bias warning
- Pre-registered kill criteria with fired/not-fired evaluation
- Falsification pass results (satisfied / still-complaining / ai-absorbed signals)
- Evidence thread table with engagement metadata (score, comments, agreement count)
- Quotes grouped by JTBD role + a marketing copy bank
- Trend direction with AI-absorption flag
- Riskiest-assumption experiment (≤2 weeks, ≤$100, pre-committed pass/fail threshold)
- Pre-mortem and 5 Mom-Test interview questions
- Confidence level on every verdict

## Install

Clone this repository into any workspace where you use Codex skills:

```bash
git clone https://github.com/SevenAILab/reddit-demand-validator.git
```

Then copy or keep the project-level skill folder:

```text
.agents/skills/reddit-demand-validator/
```

If your project already has an `.agents/skills/` directory, copy only this folder into it:

```bash
cp -R reddit-demand-validator/.agents/skills/reddit-demand-validator /path/to/your-project/.agents/skills/
```

Restart or reload your Codex/agent session so the new skill metadata is discovered.

## Usage

Ask Codex something like:

```text
Use reddit-demand-validator to validate whether there is real Reddit demand for an AI fitness coach.
```

Or in Chinese:

```text
用 Reddit Demand Validator 帮我调研 AI 健身私教在 Reddit 上有没有真实需求，核心未满足痛点是什么。
```

The skill will:

1. Convert the idea into search terms for tasks, outcomes, pains, current tools, and categories.
2. Run broad `site:reddit.com` discovery queries.
3. Extract concrete blockers from first-pass results.
4. Run constraint-led follow-up searches.
5. Select 8-12 high-signal threads.
6. Read each thread through `.json`, then `old.reddit.com`, then snippet fallback.
7. Deduplicate crossposts and near-duplicates.
8. Classify evidence as `primary evidence`, `weak evidence`, or `excluded`.
9. Produce a markdown research memo.

## How The Scoring Works

The skill separates demand strength from business-model attractiveness.

Demand strength asks:

- Is the pain repeated across independent communities?
- Are there enough deduplicated effective threads?
- Is there meaningful workaround cost?
- Are users seeking alternatives or showing willingness to pay/switch?
- Is the evidence quality at least medium?

Business-model context is reported separately:

- Population frequency: how many independent users/communities show the pain
- Individual recurrence frequency: how often the same user faces the pain again
- Pricing and acquisition risks
- Whether the problem looks subscription-friendly, one-time, service-led, or lead-gen oriented

This avoids marking a real but episodic need as weak just because it may not be a good SaaS.

### What's new in v2

v1 only searched for complaints - which exist for any topic. v2 makes the skill try to kill the idea first:

- Kill criteria are written down BEFORE searching, and evaluated in the memo
- A mandatory falsification pass searches for satisfied users and AI-absorbed workflows; dominant satisfaction caps the verdict below `strong`
- Engagement matters: a 300-upvote complaint with dozens of "same here" replies outweighs five 2-upvote threads
- Willingness to pay requires revealed evidence (users already paying for substitutes); "take my money" comments alone max out at `medium`
- Every verdict carries a confidence level, and the memo ends with the single riskiest assumption plus the cheapest behavioral experiment to test it

## Evidence Rules

The skill uses three evidence buckets:

- `primary evidence`: usable for conclusions
- `weak evidence`: can reinforce a pattern but cannot be sole support
- `excluded`: not counted or cited as support

Examples of weak evidence:

- Snippet-only threads
- Founder/builder chatter
- Low-context "solve this for me" posts
- Suspiciously promotional or shallow threads

Examples of excluded evidence:

- Obvious self-promotion
- Giveaway or lead-gen posts
- Deleted/unreadable content
- Duplicate reposts with no new information
- Clear bot/automated posts

## Files

```text
.agents/skills/reddit-demand-validator/
  SKILL.md
  query-templates.md
  report-template.md
  scoring-rubric.md
  reddit-api-toolkit.md
```

## Notes

- This skill researches Reddit only. It should not be treated as full market research.
- Reddit samples may overrepresent English-speaking, Western, technical, male, or highly online audiences.
- Reddit `.json` and `old.reddit.com` access can be unstable. The skill includes fallback behavior and requires the report to disclose crawl completeness.
- This is not a Reddit API client and does not require OAuth in v1.

## License

MIT
