# Reddit Demand Scoring Rubric

Use this rubric after deduplication and evidence classification.

## Overall Rules

- The only demand-strength conclusions are `strong`, `mixed`, and `weak`
- Every dimension must be rated `high`, `medium`, or `low`
- Every dimension must cite 1-3 links
- `Weak evidence` can reinforce a pattern, but it cannot be the sole support for:
  - any dimension rating
  - the overall `strong` conclusion
  - claims about willingness to pay or switching intent
- `Excluded` evidence does not count at all
- Judge population frequency using deduplicated evidence clusters, not raw threads
- Individual recurrence frequency is a business-model context dimension, not a demand-strength downgrade
- High-resonance threads (defined below) carry extra weight; a cluster containing one counts as 2 effective threads toward the `strong` threshold (at most 2 clusters per run may use this bonus)
- The falsification gate applies before the conclusion table: if satisfied signals dominate among high-engagement threads found in the falsification pass (roughly 2:1 or worse against pain signals), the conclusion cannot be `strong`
- Every conclusion carries a confidence level (`high` / `medium` / `low`) defined below; a `low` confidence verdict must be labeled directional, not conclusive

## High-Resonance Thread Definition

A deep-read thread is high-resonance when any of these holds:

- `score` >= 200, or
- `num_comments` >= 100, or
- `score` >= 50 AND 5+ distinct agreement replies ("same here", "this exactly", "+1")

For niche subreddits (under 50k subscribers), halve all thresholds. A high-resonance complaint is the community telling you the pain generalizes - it is the cheapest population-frequency signal Reddit offers.

## Confidence Level

| Confidence | Conditions |
|---|---|
| `high` | 8+ primary-evidence threads fully read via `.json`, falsification pass completed, no major fallback usage |
| `medium` | 5-7 primary threads, or notable `old.reddit` fallback usage, or limited comment depth on key threads |
| `low` | Fewer than 5 primary threads, or heavy snippet reliance, or falsification pass incomplete |

`low` confidence requires this wording next to the verdict: 方向性结论，非定论. Confidence describes how much to trust the label; it never changes the label itself.

## Mechanical Conclusion Table

Use this table first. Do not override it with gut feel.

| Demand label | Required evidence pattern |
| --- | --- |
| `strong` | At least 3 independent subreddits, at least 5 effective threads after dedup and resonance weighting (a cluster containing a high-resonance thread counts as 2, max 2 such bonuses), recurring same or adjacent pain, workaround cost is `medium` or `high`, willingness to pay/switch is at least `medium` or users are actively seeking alternatives, evidence quality is at least `medium`, AND the falsification gate did not fire. |
| `mixed` | Real pain exists, but one or more demand-strength inputs are incomplete: fewer than 3 subreddits, fewer than 5 effective threads, unclear workaround cost, weak or conflicting alternative-seeking, high incumbent satisfaction, or evidence quality only `medium` with important caveats. |
| `weak` | Evidence is sparse, old, mostly inaccessible, mainly founder chatter, mostly weak evidence, or users quickly indicate existing alternatives already solve the problem well enough. |

Tie-breakers:

- If `strong` thresholds are met, use `strong` even when individual recurrence frequency is `low`
- If the only concern is monetization, audience size, subscription fit, or acquisition difficulty, keep the demand label intact and explain the issue under `商业模式提示`
- If evidence quality is `low`, the result cannot be `strong`
- If all key support is `weak evidence`, the result must be `weak`
- If incumbent maturity is `high` because users are satisfied, the result is usually `mixed` or `weak`; if incumbent maturity is `high` but users still complain about gaps, use the other dimensions to decide
- If the falsification gate fired, the maximum conclusion is `mixed`, regardless of pain-side evidence volume
- If trend direction is `declining` AND the `ai-absorbed` flag is set, the maximum conclusion is `mixed`; say explicitly that the window for this pain may be closing

## Dimension Rubric

### 1. Pain Intensity

- `high`
  - Users describe repeated, concrete pain in vivid language
  - They mention broken workflows, lost time, missed outcomes, stress, or daily annoyance
  - Anchor examples:
    - "every single day"
    - "insane that this still..."
    - "wasting hours"
    - detailed stories about work or life disruption
- `medium`
  - Users clearly dislike the problem, but the impact sounds occasional or manageable
  - They describe friction more than damage
- `low`
  - The complaint is casual, vague, or quickly dismissed
  - Anchor examples:
    - one-line gripe with no context
    - "not a big deal"
    - "I can live with it"

### 2. Population Frequency

- `high`
  - The same pain appears across at least 3 subreddits and 5+ deduplicated threads
  - Different users describe similar jobs, failures, or complaints independently
- `medium`
  - The pain repeats, but only in a narrow set of communities or only a few threads
- `low`
  - The pain appears rarely, mostly in duplicates, or only in one small corner of Reddit

### 3. Individual Recurrence Frequency

This dimension describes how often the same user is likely to hit the problem again. It informs business-model risk, not demand-strength truth.

- `high`
  - The same user faces it weekly or monthly
  - Anchor examples:
    - email management
    - scheduling coordination
    - recurring reporting
- `medium`
  - The same user faces it a few times per year or on a predictable cycle
  - Anchor examples:
    - taxes
    - annual planning
    - quarterly compliance
- `low`
  - The same user faces it only a few times in a lifetime or at rare life/business transitions
  - Anchor examples:
    - naming a company
    - buying a home
    - planning a wedding

When this is `low`, add a business-model note about one-time payment, service, marketplace, lead-gen, or episodic acquisition. Do not lower the demand label for this reason alone.

### 4. Workaround Cost

- `high`
  - Users rely on spreadsheets, manual copy-paste, multiple tools, scripts, exports, or brittle hacks
  - The workaround clearly costs time, money, accuracy, or context switching
  - Anchor examples:
    - "export then import"
    - "I use three tools"
    - "I built my own script"
- `medium`
  - Users have workarounds, but they are only somewhat annoying
- `low`
  - Users already have easy, acceptable workarounds and do not sound especially bothered

### 5. Incumbent Maturity

- `high`
  - Existing tools appear strong and satisfy most users
  - Many threads quickly point to the same solid alternatives with little pushback
- `medium`
  - Existing tools help, but there are notable gaps, tradeoffs, or adoption friction
- `low`
  - Existing tools are clearly missing, clunky, fragmented, or untrusted

Interpretation note:

- `high` incumbent maturity weakens the build case
- `low` incumbent maturity strengthens the build case

This dimension can affect demand strength only when users appear satisfied. A mature market with persistent complaints can still show `strong` demand for a better wedge.

### 6. Willingness To Pay Or Switch

Revealed preference (what users already pay) outranks stated preference (what users say they would pay).

- `high`
  - Revealed evidence only: users are already paying for substitutes - inferior tools they keep subscribing to, agencies/freelancers hired for this job, scripts/services they bought, concrete dollar amounts mentioned
  - Anchor examples:
    - "I pay $40/mo for X and it still can't..."
    - "we ended up hiring someone to do this manually"
    - r/forhire postings paying for this exact task
- `medium`
  - Explicit stated willingness ("I'd pay for this", "take my money") or active alternative-seeking because current options are unacceptable
  - Stated-only evidence can never exceed `medium`
- `low`
  - Users say current tools are good enough, not worth paying for, or not worth changing

### 7. Evidence Quality

- `high`
  - Most support comes from accessible threads with readable OP bodies and comment context
  - Evidence comes from problem-owner communities, not founder chatter
  - Weak evidence is rare and non-essential
  - Strong threads contain concrete constraints, failed attempts, or real stakes
- `medium`
  - The evidence is usable, but some important threads are old, partial, or from less ideal communities
  - Some supporting threads are advice-seeking threads with only partial context
- `low`
  - Much of the case depends on snippets, founder chatter, suspicious authors, or inaccessible threads
  - Many threads ask strangers to solve the problem directly without enough context to explain the underlying unmet need

### 8. Trend Direction

Compare evidence density across time windows (threads from the last 12 months vs. 12-36 months, via `t=year` / `t=all` searches and thread dates).

- `rising`
  - Clearly more recent threads; new subreddits or new vocabulary forming around the pain
- `stable`
  - Similar density across windows; long-lived recurring pain
- `declining`
  - Mostly old threads; recent discussion sparse or resolved

Additionally set the `ai-absorbed` flag when 2+ independent threads from the last 12 months report that general AI tools now handle the task acceptably. `declining` + `ai-absorbed` caps the overall conclusion at `mixed` (see tie-breakers). A `rising` trend strengthens the build-now case and belongs in the memo's trend note.

## Business-Model Notes

Report these separately from the demand label:

- Low individual recurrence may favor one-time payment, productized service, agency referral, marketplace, templates, or lead-gen
- High individual recurrence may support subscription or retention-led products
- Narrow audiences may still have strong demand but require careful channel strategy
- Expensive acquisition can make a strong demand hard to build as a business; that is not the same as weak demand
