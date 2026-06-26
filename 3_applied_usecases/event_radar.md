# Event Radar
### Applied use case · OpenClaw Event 3

> **One-line outcome:** The agent watches the event pages you care about and pings you the moment a new event appears that matches what you're into, with name, date, location, and a registration link, before the good ones fill up.

**Best for:** People who go to meetups, conferences, and community events, and keep missing them until they're sold out. Great for networking and GTM.
**Time to set up:** ~10 minutes
**Runs:** A daily check that compares against what it saw yesterday.

---

## What it does

Good events fill up fast and you find out too late. This agent watches the sources where events get posted: a Luma calendar, a meetup page, an organiser you follow. Every day it checks the page, compares it against the list it remembers from yesterday, and alerts you only about genuinely new events. It filters by what you actually care about (your city, your topics) so you are not pinged about everything, and it gives you enough to act: name, date, location, and the link to register.

The key trick is memory. The agent keeps a list of events it has already told you about, so you get each new event exactly once, not a daily re-dump of the same calendar.

---

## What you'll need

| Piece | What for |
|---|---|
| **Search provider** | Required. To read the event pages and pull details. |
| **Messaging channel** | Telegram or WhatsApp, for the alerts. |
| **Memory** | Required. To remember which events it has already flagged. Built in, nothing to install. |
| **Composio apps** | None required. Optional: Google Calendar (add events you RSVP to). |

### Connect these apps in Composio

No Composio apps are needed for the core recipe. Optionally connect **Google Calendar** so you can reply "add it" and have the event dropped into your calendar.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts.

```
You are my Event Radar. Watch these event sources daily and tell me about new
events worth my time:

SOURCES:
- [https://lu.ma/your-city-or-calendar-url]
- [any other event pages or organisers I follow]

WHAT I CARE ABOUT:
- Location: [Dublin, or online]
- Topics: [AI, startups, founders, design - whatever fits me]
- Skip: [recruiting events, paid courses, anything over EUR 50]

HOW TO WORK:
1. Each day, read the sources and list the current events.
2. Compare against the events you already told me about, stored in a memory file.
   Only treat an event as new if it wasn't in that list.
3. For each genuinely new event that matches what I care about, send me on
   [Telegram]: event name, date and time, location (or "online"), a one-line
   what-it-is, and the registration link.
4. Update your memory file with the events you've now seen.
5. If nothing new matches, stay quiet - don't send an empty "nothing today".

Set this to run once a day at [8am Europe/Dublin] and do a first scan now so we
can seed the memory list (for this first run, just show me what's currently on,
don't treat everything as 'new' going forward).
```

---

## Step 2: The rules block

**`AGENTS.md`** (optional):

```markdown
## Event Radar
- Read-only: web search plus sending me alerts. Touches nothing else.
- Keep a memory file of events already flagged; never alert me about the same
  event twice.
- Only alert on events matching my location and topic filters. Stay silent when
  nothing new matches.
```

**The schedule (cron).** A once-a-day check at a fixed time:

```bash
openclaw cron create "0 8 * * *" \
  "Run my Event Radar: check my event sources for new matching events and alert me." \
  --name "Event radar" --tz "Europe/Dublin" --session isolated
```

You could also run this as a HEARTBEAT task on a longer interval (for example every 6h) if you want to catch hot events faster, but once a day is plenty for most calendars.

---

## Step 3: Run it once

The first run is special: it seeds the memory of "already seen" events so you do not get spammed with the entire existing calendar tomorrow.

> "Do a first Event Radar scan now. Show me what's currently on that matches my filters, and save the full current event list to memory as the baseline. Don't treat these as new alerts going forward."

Check that the filters work (right city, right topics) and that it saved a baseline list. From the next run on, you will only hear about events added after today.

---

## Make it yours

- **Multiple cities** - travelling? Add "also include events in [city] between [dates]."
- **Organiser watch** - point it at a specific organiser or venue you trust rather than a whole-city calendar.
- **One-tap RSVP** - connect Google Calendar and reply "add the [name] one" to drop it in your calendar.
- **Weekly roundup instead** - change the cron to Monday 8am and ask for "everything new this week" in one digest.
- **Share-worthy** - "for events that fit my team, draft a one-line message I can forward to them."

---

## Tuning & safety

- **The memory comparison is the whole recipe.** If you start getting repeat alerts, the agent is not reading or updating its seen-events file correctly. Tell it explicitly to load the list, compare, alert, then save the updated list every run.
- **Tune the filters to cut noise.** Too many alerts and you will ignore it; too tight and you will miss things. Adjust the topics and the skip list over the first week.
- **Pages change layout.** If a source stops returning events, the page structure may have changed or it needs a login. Point it at a public calendar URL where possible.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
