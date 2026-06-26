# House Search Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Instead of refreshing property portals, you get a daily shortlist: new listings that match your criteria, each scored against what you actually care about, with the dealbreakers already filtered out and a one-line reason it made the cut.

**Best for:** Anyone house-hunting or flat-hunting who is tired of scrolling portals and missing good listings. One of the standout use cases from the event registrations.
**Time to set up:** ~15 minutes
**Runs:** A daily scan that only surfaces new, qualifying listings.

---

## What it does

House hunting is a daily grind of checking the same portals and mentally re-applying the same filters. This agent does that for you. You tell it your criteria once: area, budget, bedrooms, must-haves, dealbreakers. Each day it checks new listings, scores each one against your criteria, throws out the ones that fail a hard rule, and sends you only the keepers with a short reason each made the list. It remembers what it has already shown you, so you see each property once, and it can keep a running log so you can compare and track the ones you liked.

It does not replace viewing a place or your judgement. It removes the tedious part: the daily filtering, so your attention goes only to listings genuinely worth a look.

---

## What you'll need

| Piece | What for |
|---|---|
| **Search provider** | Required. To read listings and pull details from portals. |
| **Composio apps** | Gmail (optional but recommended), to read portal alert emails |
| **Optional Composio apps** | Notion or Google Sheets (a log of scored listings) |
| **Memory** | Required. To remember listings already shown. Built in. |
| **Messaging channel** | Telegram or WhatsApp, for the daily shortlist |

### Connect these apps in Composio

Assumes you have done the one-time Composio MCP setup in the [folder README](./README.md).

- **Gmail** *(recommended)* - the most reliable source. Set up saved-search alert emails on your portal (Daft, Rightmove, Zillow, etc.) and let the agent read those alerts, rather than scraping the site directly.
- **Notion** or **Google Sheets** *(optional)* - a running log of every listing it scored, so you can sort, compare, and keep notes.

Search-only works, but portals often resist scraping. Gmail alert emails are the steadier input.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts carefully: these criteria are the whole recipe.

```
You are my House Search Agent. Each day you find new property listings that fit
me, score them, and send me only the ones worth my attention.

MY CRITERIA:
- Area: [Dublin 6, 8, or commutable within 30 min of city centre]
- Budget: [up to EUR 450,000]
- Type: [2+ bed house or apartment]
- Must-haves: [outdoor space, BER C or better, not ground floor]
- Nice-to-haves: [parking, near a park, south-facing]
- Dealbreakers (auto-reject): [over budget, 1 bed, no outdoor space, flood zone]

SOURCES:
- Read new property alert emails in my Gmail from [Daft / the portal].
- Optionally also search [portal URLs] for new listings.

HOW TO WORK each day:
1. Gather new listings from the sources.
2. Reject anything that hits a dealbreaker - don't show me those.
3. For the rest, score 0-100 against my criteria and write a one-line reason
   (what's good, what's the catch).
4. Compare against listings you've already shown me (kept in a memory file) so I
   only see each property once.
5. Send me on [Telegram] the new qualifying listings, best score first: address,
   price, key facts, the score, the one-line reason, and the link.
6. If a log is set up, append every scored listing to [my Notion/Sheet].
7. Update your memory of seen listings. If nothing new qualifies, stay quiet.

Don't contact agents, book viewings, or fill in any forms. Just find, score, and
report. I decide what to act on.
```

---

## Step 2: The rules block

**`AGENTS.md`** (permissions and guardrails):

```markdown
## House Search Agent
- May read my Gmail property alerts and run web searches. May write to my
  listings log.
- Must NEVER contact estate agents, enquire about a property, book a viewing, or
  submit any form. Find, score, and report only.
- Keep a memory file of listings already shown; never show the same one twice.
- Auto-reject anything hitting a dealbreaker; never surface those.
- When I tell you I like or dislike a listing, learn what that implies about my
  taste and save it to memory to sharpen future scoring.
```

**The schedule (cron).** A once-a-day scan:

```bash
openclaw cron create "0 8 * * *" \
  "Run my House Search: find new listings, reject dealbreakers, score the rest, send me the new keepers." \
  --name "House search" --tz "Europe/Dublin" --session isolated
```

---

## Step 3: Run it once

The first run seeds memory so you do not get flooded tomorrow:

> "Do a first House Search run now. Score what's currently available against my criteria, show me the top few, and save the full set you considered to memory as the baseline so future runs only show genuinely new listings."

Check the scoring against your own gut on a few listings, and confirm the dealbreakers are actually filtering. If it is scoring things you would never consider, refine the criteria before turning it loose daily.

---

## Make it yours

- **Renting instead** - swap budget for monthly rent and add filters like "furnished" or "pets allowed".
- **Commute-aware** - "score higher if within 30 min by public transport of [office address]" and let it check routes.
- **Price-drop watch** - "also alert me if a listing I've already seen drops its price."
- **Compare two areas** - score listings in two neighbourhoods and tell you which area is giving better value this week.
- **Weekly digest** - change the cron to weekly if the market where you are is slower.

---

## Tuning & safety

- **Your criteria are the recipe.** Vague criteria give vague results. Be specific about dealbreakers especially, since those do the filtering. Refine them over the first week as you see what it surfaces.
- **Teach it your taste.** Scoring gets much better when you tell it why you liked or passed on a listing and it saves that. A few rounds of feedback and the shortlist sharpens.
- **Keep it read-only.** The one firm rule: it must never contact an agent or enquire on your behalf. House transactions are high-stakes and personal; the agent finds and scores, you make every move.
- **Verify before you act.** Listing data can be stale or wrong (sold, price changed). Always confirm on the actual listing before you get excited or reach out.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
