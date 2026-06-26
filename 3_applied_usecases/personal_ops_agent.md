# Personal Ops Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Every weekday at 7am you get one message with your day laid out: calendar, what needs a reply (with drafts already written), follow-ups you owe people, and tasks captured. You skim it over coffee and approve the few things that matter.

**Best for:** Everyone. This is the broadest, highest-value recipe and the best one to build first.
**Time to set up:** ~15 minutes
**Runs:** On a schedule (weekday mornings), plus on demand any time you ask.

---

## What it does

The agent wakes up before you do and assembles a single morning brief. It reads today's calendar, scans your unread email, and pulls anything you left in your notes. Then it does the thinking: it tells you what your day actually looks like, which emails need you, and drafts replies to the straightforward ones so they are ready to send. It captures follow-ups and to-dos so nothing slips, and it remembers your preferences (who matters, how you like replies written, what to ignore) so each brief is sharper than the last. The one thing it never does is send or commit anything without you: drafts wait for your nod.

The result is the difference between starting the day reacting to a messy inbox and starting it with a plan.

---

## What you'll need

| Piece | What for |
|---|---|
| **Composio apps** | Gmail (read mail, save drafts), Google Calendar (today's events) |
| **Optional Composio apps** | Notion or Google Drive (read your notes), Todoist or Google Tasks (capture to-dos) |
| **Messaging channel** | Telegram or WhatsApp, from Event 1, for the brief to land in |
| **Search provider** | Only if you want headlines in the brief. Otherwise not required. |

### Connect these apps in Composio

Assumes you have already done the one-time Composio MCP setup in the [folder README](./README.md).

- **Gmail** - reads your unread mail and saves reply drafts. The agent never sends; it leaves drafts in Gmail and shows them to you.
- **Google Calendar** - pulls today's events so the brief knows your schedule.
- **Notion** or **Google Drive** *(optional)* - reads a notes page or doc where you jot things, so loose ends make it into the brief.
- **Todoist** or **Google Tasks** *(optional)* - so "follow up with Sam" becomes a real task, not a line it forgets.

You do not need all of these. Gmail plus Calendar is enough for a great first version. Add the rest once it is working.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts to fit you.

```
You are my Personal Ops Agent. Each morning you prepare a single brief for me
and send it to me on [Telegram]. Here is exactly what the brief should contain,
in this order:

1. THE DAY: My calendar for today from Google Calendar. List each event with
   time and title. Flag back-to-back stretches with no break, and call out
   anything that needs prep.

2. INBOX: Scan my unread Gmail. Sort it into three groups:
   - "Needs you" - real messages that want a reply or a decision
   - "FYI" - worth knowing, no action
   - "Noise" - newsletters, notifications, receipts. Just a count, not a list.

3. DRAFTS: For up to [3] of the "Needs you" emails that have a clear answer,
   write a reply in my voice and save it as a draft in Gmail. In the brief,
   show me a one-line summary of each draft. Do NOT send anything.

4. FOLLOW-UPS: Anything I owe someone, or anyone who owes me, based on my
   email and [notes in Notion]. If a to-do app is connected, create tasks for
   the ones I clearly need to act on.

5. NOTES: Anything from my [Notion notes page] that looks like a loose end.

Keep the whole brief short enough to read on a phone. Lead with what matters.
No filler, no "let me know if you need anything."

Two standing rules:
- Never send an email, post a message, or complete a task on my behalf without
  showing me first and getting a yes.
- When I correct you - a draft's tone, who counts as important, what to treat
  as noise - save that to memory so the next brief is better.

Once you understand this, set it to run every weekday at 7am [my timezone], and
send me one now as a test.
```

When you send that last line, the agent will create the scheduled job for you (see Step 2 for what that looks like under the hood) and produce a test brief immediately.

---

## Step 2: The rules block

Two pieces: guardrails that always apply, and the schedule.

**`AGENTS.md`** (permissions and guardrails):

```markdown
## Personal Ops Agent
- May read Gmail, Google Calendar, Notion, and my task app freely.
- May save Gmail drafts freely. Must NEVER send email without my explicit yes.
- May create tasks in my to-do app. Must confirm before completing or deleting any.
- Never share the contents of my inbox or calendar outside my own channels.
- When I correct a draft's tone or what counts as important, write it to memory.
```

**The schedule (cron, not HEARTBEAT).** A morning brief runs at a wall-clock time, so it uses OpenClaw's scheduled-task (cron) feature, not the interval-based heartbeat. The easiest path is the last line of the setup prompt above: just tell the agent "run this every weekday at 7am" and it creates the job. If you prefer to do it by hand on the CLI:

```bash
openclaw cron create "0 7 * * 1-5" \
  "Run my Personal Ops morning brief and send it to me on Telegram." \
  --name "Morning brief" \
  --tz "Europe/Dublin" \
  --session isolated
```

`"0 7 * * 1-5"` means 7:00 on Monday to Friday. Change `Europe/Dublin` to your timezone. Use `--session isolated` so the scheduled run gets a clean context instead of inheriting whatever you were last chatting about.

> **When to use HEARTBEAT.md instead:** the heartbeat is for "every N minutes" watch tasks (for example, "check for urgent email every 30 minutes"), not for a fixed clock time. If you also want intra-day urgent-mail pings, add that as a heartbeat task. The 7am brief stays on cron.

---

## Step 3: Run it once

Before trusting it on a schedule, run it by hand and read the output:

> "Run my Personal Ops morning brief now and send it to me, but do not save any drafts yet, just show me what you would draft."

You should get a tight brief: your real calendar, your inbox sorted into the three buckets, one-line summaries of the replies it would draft, and the follow-ups it spotted. Check the draft tone and whether "Needs you" vs "Noise" matches your judgement. When that looks right, let it save drafts and put it on the schedule.

---

## Make it yours

- **Add a headline or two** - connect a search provider and add "end with the top 2 headlines on [topics I follow]" to the prompt.
- **Add an evening wrap** - a second cron job at 6pm: "what did I not get to, what's tomorrow look like."
- **Weekend mode off** - the `1-5` in the cron expression already skips weekends. Use `* * *` for every day.
- **Tighten the draft count** - if three drafts a morning is too many, drop it to one. Quality over volume early on.
- **Meeting prep** - "for any meeting with an external person, pull our last email thread with them and add two lines of context."

---

## Tuning & safety

- **Tone of drafts is the thing you will correct most.** The first week, edit the drafts and tell the agent why ("too formal", "I never open with 'I hope this finds you well'"). Those corrections go to memory and the drafts converge on your voice fast.
- **The one permission that matters: never auto-send.** The setup prompt and AGENTS.md both enforce this. Keep it that way until you genuinely trust the drafts, and even then consider leaving sending to yourself.
- **Make corrections stick.** The win of this recipe compounds only if the agent learns. If it keeps mis-sorting a sender as noise, say so once and confirm it saved that to memory. See Event 2's memory section for how that works.
- **Keep the brief short.** If it gets bloated, tell it to cut. A brief you actually read every morning beats a thorough one you skip.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
