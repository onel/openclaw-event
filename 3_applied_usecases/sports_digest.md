# Daily Sports Digest
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Every morning you get one message with today's fixtures in your timezone and yesterday's results, scorers, and a one-line highlight, with results hidden behind a spoiler tag until you tap to reveal.

**Best for:** Anyone, and a great first build. It is simple, it runs on a schedule, and it shows off search plus memory plus messaging without touching anything sensitive.
**Time to set up:** ~10 minutes
**Runs:** Every morning on a schedule.

---

## What it does

You follow a competition: the World Cup, your football league, F1, whatever. This agent gives you a clean daily briefing instead of you opening five sports sites. Each morning it pulls today's fixtures and converts kick-off times to your timezone, then pulls yesterday's results with scorers and a one-line highlight for each. Because nobody wants results spoiled before they have watched the recording, it hides the scores behind a spoiler marker you choose to reveal.

It is low-stakes by design: it only reads public information and only sends you a message. That makes it the perfect recipe to build first and prove the pattern end to end.

---

## What you'll need

| Piece | What for |
|---|---|
| **Search provider** | Required. This is how it gets live fixtures and results. |
| **Messaging channel** | Telegram or WhatsApp, where the digest lands. |
| **Composio apps** | None required. Optional: Google Calendar, to add a match you care about as an event. |

### Connect these apps in Composio

This recipe needs no Composio apps to work: a search provider (from Event 2) plus your messaging channel is enough. Optionally connect **Google Calendar** if you want it to drop a specific match into your calendar.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts.

```
You are my Daily Sports Agent. Each morning, send me a digest about [the World
Cup 2026] to [Telegram]. Build it with web search, preferring official and
reputable sources.

TODAY'S FIXTURES:
- For each match today, show: teams (with flag or club emojis), the competition
  stage or round, venue and city, and kick-off time converted to [Europe/Dublin].
- If there are no matches today, say so in one line.

YESTERDAY'S RESULTS:
- For each match, the final score with goal scorers if available, and a
  one-sentence highlight.
- IMPORTANT: hide all results behind a spoiler. Put the fixtures and a clear
  "Results below - tap to reveal" line first, then the results in a Telegram
  spoiler block so I'm not spoiled by the preview.

Keep it short and scannable. Lead with today, then yesterday's spoiler section.
Don't pad it with commentary I didn't ask for.

Set this to run every day at [8am Europe/Dublin] and send me one now as a test.
```

---

## Step 2: The rules block

This recipe is read-only and sends only to you, so the guardrails are light.

**`AGENTS.md`** (optional, for tidiness):

```markdown
## Daily Sports Agent
- Read-only: web search plus sending me the digest. Touches nothing else.
- Prefer official/reputable sources; if results are unconfirmed, say so.
- Always put fixtures first and hide results behind a spoiler.
```

**The schedule (cron).** A fixed morning time, so use a cron job. Easiest is the last line of the setup prompt; the agent creates it. By hand:

```bash
openclaw cron create "0 8 * * *" \
  "Send my daily sports digest to Telegram." \
  --name "Sports digest" --tz "Europe/Dublin" --session isolated
```

`"0 8 * * *"` is 8:00 every day. Use `0 8 * * 1-5` for weekdays only.

---

## Step 3: Run it once

> "Build my sports digest now and send it to me, exactly as the daily version would look."

Check three things: are today's kick-off times converted to your timezone, are yesterday's results actually correct (spot-check one against a site), and is the spoiler hiding the scores in the message preview. When it looks right, the scheduled version does the same each morning.

---

## Make it yours

- **Any competition** - swap "the World Cup 2026" for your league, your team, F1, the NBA, cricket. The pattern is identical.
- **Your team only** - "only include matches involving [team]; ignore the rest."
- **Standings and what's next** - add "include the current table and my team's next fixture."
- **No spoilers ever** - drop yesterday's results entirely and make it fixtures-only.
- **Add it to your calendar** - connect Google Calendar and add "put today's [team] match in my calendar."

---

## Tuning & safety

- **Results accuracy is the only real risk.** Live scores can be wrong if a source is stale. Tell it to prefer official sources and to flag anything it could not confirm rather than guessing.
- **Spoiler formatting is messaging-specific.** Telegram supports spoiler blocks; if you use a channel that does not, ask the agent to put results in a separate follow-up message instead.
- **Keep it lean.** If the digest gets chatty, tell it to cut commentary. A digest you read in ten seconds beats a wall of match reports.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
