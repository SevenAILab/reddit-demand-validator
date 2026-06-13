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
