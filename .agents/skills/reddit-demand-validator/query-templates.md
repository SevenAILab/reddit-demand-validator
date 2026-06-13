# Reddit Demand Query Templates

Use these templates to build search-engine queries with `site:reddit.com/r/`.

Do not rely on a single wording. Generate at least:

- one `task` version
- one `problem` version
- one `current_tool` or `category` version when relevant

Start broad enough to discover communities, then narrow into the promising subreddits.

## Placeholder Guide

- `<task>`: the job the user is trying to get done
- `<outcome>`: the end result they want
- `<problem>`: the frustrating part of the workflow
- `<current tool>`: the tool they already use today
- `<category>`: the broader product category
- `<pain relief>`: the relief they say they want, not your product pitch

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

## Pain

Use this first. It is the best query family for subreddit discovery.

### Task version

```text
site:reddit.com/r/ ("I hate" OR "frustrated with" OR "tired of" OR "annoying" OR "why is there no" OR "wish there was") ("<task>")
```

### Problem version

```text
site:reddit.com/r/ ("I hate" OR "frustrated with" OR "tired of" OR "annoying" OR "why is there no" OR "wish there was") ("<problem>")
```

### Current-tool version

```text
site:reddit.com/r/ ("I hate" OR "frustrated with" OR "tired of" OR "annoying" OR "broken" OR "pain in the ass") ("<current tool>")
```

### Extra pain phrases

Use as alternates when the initial results are weak:

- `"every single day"`
- `"wasting hours"`
- `"manual process"`
- `"insane that"`
- `"so clunky"`
- `"there has to be a better way"`

## Workaround

Use this to uncover ugly substitutes and workflow friction.

### Task version

```text
site:reddit.com/r/ ("workaround" OR "hacky" OR "manual process" OR "spreadsheet" OR "glue together" OR "using X and Y") ("<task>")
```

### Problem version

```text
site:reddit.com/r/ ("workaround" OR "hacky" OR "manual process" OR "spreadsheet" OR "copy paste" OR "duct tape") ("<problem>")
```

### Current-tool version

```text
site:reddit.com/r/ ("hacky" OR "manual" OR "export then import" OR "using multiple tools") ("<current tool>")
```

## Request

Use this to find active solution-seeking behavior.

### Outcome version

```text
site:reddit.com/r/ ("looking for" OR "is there a tool" OR "recommendation for" OR "any app that" OR "need a way to") ("<outcome>")
```

### Task version

```text
site:reddit.com/r/ ("looking for" OR "recommendation for" OR "need a way to" OR "how do you") ("<task>")
```

### Problem version

```text
site:reddit.com/r/ ("any tool for" OR "what do you use for" OR "is there anything that") ("<problem>")
```

## Switching

Use this after you identify likely incumbents or category names.

### Tool version

```text
site:reddit.com/r/ ("alternative to" OR "switched from" OR "better than" OR "replace" OR "leave <tool>") ("<current tool>")
```

### Category version

```text
site:reddit.com/r/ ("alternative to" OR "switching from" OR "better option than" OR "moving away from") ("<category>")
```

### Pain-led switching version

```text
site:reddit.com/r/ ("switching because" OR "fed up with" OR "done with") ("<current tool>" OR "<problem>")
```

## Willingness To Pay

Use this last, once you already have signs of pain and workarounds.

### Outcome version

```text
site:reddit.com/r/ ("would pay for" OR "take my money" OR "happy to pay" OR "pay for something that") ("<outcome>")
```

### Relief version

```text
site:reddit.com/r/ ("would pay for" OR "worth paying for" OR "I would buy" OR "I'd subscribe to") ("<pain relief>")
```

### Problem version

```text
site:reddit.com/r/ ("take my money" OR "happy to pay" OR "worth paying to avoid") ("<problem>")
```

## Subreddit Discovery Tips

- Start with `pain` and `request`
- Read result URLs before opening threads; note which communities repeat
- Add subreddit-specific follow-up searches only after a community proves relevant
- Prefer owner communities over founder communities
- Exclude generic startup communities from the main evidence pool unless a thread is unusually detailed

## Suggested Query Mix

For a normal run, use roughly:

- 4-5 pain queries
- 2-3 request queries
- 2-3 workaround queries
- 1-2 switching queries
- 1-2 willingness-to-pay queries
- 1-2 money-talk queries (revealed spend)
- 3+ falsification queries (mandatory, run as the final pass)

If evidence is thin, iterate on user language rather than immediately broadening to startup jargon.

## Constraint-Led Follow-Up Queries

Use these after the first pass, once you know what users are actually stuck on.

The goal is to move from broad category noise into concrete blockers.

### Generic blocker pattern

```text
site:reddit.com/r/ ("<task>" OR "<problem>") ("<constraint>" OR "<failure mode>" OR "<blocking condition>")
```

### Advice + failed-attempt pattern

```text
site:reddit.com/r/ ("<task>" OR "<problem>") ("tried" OR "doesn't work" OR "stuck" OR "keep running into") ("<constraint>")
```

### Comparison-under-constraint pattern

```text
site:reddit.com/r/ ("<task>" OR "<problem>") ("best way" OR "alternative" OR "recommendation") ("<constraint>")
```

## Constraint Examples

Choose only the blocker types that actually appeared in the first-pass evidence.

- legal or compliance
- pricing or budget
- integration or migration
- speed or reliability
- collaboration or approval
- discoverability or SEO
- ownership or availability
- localization or language
- mobile or desktop fit
- workflow complexity

Do not guess blockers that did not appear in the first-pass threads. The second pass should be evidence-led, not imagination-led.

## In-Subreddit Follow-Up (Reddit API)

Once a subreddit is promoted into the candidate list, run the constraint-led queries INSIDE it via the Reddit API for higher precision than search engines:

```bash
curl -s -A "demand-validator/2.0" \
  "https://www.reddit.com/r/{subreddit}/search.json?q={blocker phrase}&restrict_sr=on&sort=relevance&t=year&limit=25"
```

See [reddit-api-toolkit.md](reddit-api-toolkit.md) for time filters, comment search, and rate limits.
