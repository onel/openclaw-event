# Weather & Conditions Alert
### Applied use case · OpenClaw Event 3

> **One-line outcome:** The agent only messages you when it matters: if it's raining, snowing, or windy right now, you get a heads-up plus whether the next two hours get better or worse. On clear mornings, silence.

**Best for:** Anyone who steps outside on a schedule: dog walkers, cyclists, commuters, market and event ops, anyone running outdoor logistics.
**Time to set up:** ~10 minutes
**Runs:** Every morning, but only speaks up when a threshold is crossed.

---

## What it does

A daily weather summary is noise: you stop reading it. This agent does the opposite. It checks the actual current conditions at a set time and stays silent unless something is wrong. If it is raining, snowing, or windy right now, it alerts you and then looks at the next two hours to tell you whether to wait it out or just go. The point is a signal you trust: when this agent messages you, you know to grab a coat or change your plan, because it does not message you otherwise.

This is the "standing watch" pattern from Event 2, applied to weather: watch something, speak only when it crosses a line.

---

## What you'll need

| Piece | What for |
|---|---|
| **Weather data** | Required. OpenClaw's built-in weather tool, or a weather app via Composio, or your search provider. |
| **Messaging channel** | Telegram or WhatsApp, for the alert. |
| **Composio apps** | None required. Optional: a weather service toolkit if you want a specific provider. |

### Connect these apps in Composio

No Composio apps are needed: OpenClaw can fetch current conditions and an hourly forecast directly, or via your search provider. Optionally connect a dedicated weather toolkit in Composio if you prefer a specific source or need a precise station.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts.

```
You are my Weather Alert agent. Each morning, check the CURRENT conditions for
[Dublin] - the present weather right now, not the daily summary or the forecast.

ALERT LOGIC:
- If it is raining, snowing, or windy right now, send me a text alert on
  [Telegram]. Otherwise, send nothing at all - silence means it's fine.
- When you do alert, also fetch the hourly forecast for the next two hours and
  tell me in one line whether conditions will get better or worse, so I know
  whether to go now or wait.

Keep alerts to two lines max: what it's doing now, and the two-hour outlook.

My thresholds (adjust the defaults if these are set):
- "Windy" means above [30 km/h].
- Also alert if the temperature is below [2C] (ice risk) [or above 28C].

Set this to run every morning at [7am Europe/Dublin].
```

---

## Step 2: The rules block

**`AGENTS.md`** (optional):

```markdown
## Weather Alert agent
- Read-only: check conditions and send me an alert. Touches nothing else.
- Always check present conditions, not the daily summary.
- Stay silent when conditions are fine. Only message me when a threshold is crossed.
```

**The schedule (cron).** A fixed morning check:

```bash
openclaw cron create "0 7 * * *" \
  "Check current weather for Dublin; alert me only if raining, snowing, windy, or icy, with a 2-hour outlook." \
  --name "Weather alert" --tz "Europe/Dublin" --session isolated
```

Change `0 7 * * *` to your time. Note `--session isolated` so each morning's check is clean.

---

## Step 3: Run it once

Because it stays silent on good days, test it in a way that forces output:

> "Run my weather check now. Even if conditions are fine and you wouldn't normally alert, this time tell me what you found and exactly what alert you would have sent if it were bad, so I can see the logic works."

Confirm it is reading current conditions (not a daily forecast) and that the two-hour outlook makes sense. Then trust the silence: on a clear morning, no message is the correct behaviour.

---

## Make it yours

- **Different thresholds for different days** - "on [Tuesday/Thursday] I cycle, so also alert me on heavy rain even if it's light elsewhere."
- **A specific window** - "I leave at 8:30; tell me the conditions for 8:30 to 9:00 specifically."
- **Other conditions** - air quality, UV index, pollen, frost on the car, surf or wind for a sport.
- **Multiple locations** - check home and the place you commute to.
- **Evening check too** - a second cron for "will I need a coat tonight?"

---

## Tuning & safety

- **Silence is a feature, not a bug.** The value is that a message means something. Resist the urge to make it send a daily "all clear", that is exactly what trains you to ignore it.
- **Tune the thresholds to your tolerance.** Too sensitive and it cries wolf; too high and you get caught out. Adjust the wind and temperature numbers over a couple of weeks.
- **Confirm it reads present conditions.** The most common mistake is the agent summarising the day's forecast instead of what is happening now. The prompt is explicit about this; check it on the first run.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
