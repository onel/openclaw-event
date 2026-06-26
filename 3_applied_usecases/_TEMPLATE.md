<!--
RECIPE TEMPLATE for OpenClaw Event 3 use cases.
Copy this file, rename it, and fill in each section.
Keep the tone practical and non-engineer-friendly, matching Event 2.
House rule: no em dashes anywhere. Use commas, colons, or hyphens.
Delete these comments before publishing.
-->

# [Use Case Name]
### Applied use case · OpenClaw Event 3

> **One-line outcome:** [What the reader has by the end, e.g. "Every morning at 7am you get a brief of your day with replies already drafted."]

**Best for:** [Who this is for / when it fits]
**Time to set up:** [e.g. ~10 minutes]
**Runs:** [On a schedule / on demand / when something happens]

---

## What it does

[2-4 sentences in plain language. Describe the flow the way a person would experience it, not the implementation. Name the steps the agent takes: what it watches, what it understands, what it produces, when it hands back to you.]

---

## What you'll need

| Piece | What for |
|---|---|
| **Composio apps** | [e.g. Gmail, Google Calendar, Slack] |
| **Search provider** | [If the recipe needs live web data. Otherwise: "Not required."] |
| **Other** | [Any skill, file, or account this depends on. Otherwise leave out.] |

### Connect these apps in Composio

[List each Composio app/toolkit to connect, with a one-line note on why. Assume the reader has already done the one-time Composio MCP setup from the README.]

- **[App]** - [why it's needed / what the agent does with it]
- **[App]** - [...]

If you have not connected Composio to your agent yet, do the one-time setup in the [folder README](./README.md) first.

---

## Step 1: The setup prompt

Paste this to your agent once. It tells the agent what the recipe is and how to behave. Edit the bracketed parts to fit you.

```
[The copy-paste setup prompt. Written as a brief to the agent in plain
language: the goal, the inputs it should pull, the output format, the
schedule if any, and where it should hand off to you. Use [brackets] for
the bits the reader must personalize.]
```

---

## Step 2: The rules block

Add these to your agent's workspace files. They set the guardrails and, if the recipe runs on its own, the schedule.

**`AGENTS.md`** (permissions and guardrails):

```markdown
[The few lines that define what the agent may and may not do for this
recipe: what it can do freely, what needs confirmation, what it must
never do. Be specific about sends, posts, and anything that spends money.]
```

**The schedule** (only if it runs on its own). Pick the right mechanism:

- **Fixed clock time** (e.g. 7am daily, Monday reports): use a cron job, not the heartbeat. Easiest is to tell the agent "run this every weekday at 7am" and it creates the job. CLI equivalent:

  ```bash
  openclaw cron create "[cron expr, e.g. 0 7 * * 1-5]" \
    "[what runs]" --name "[name]" --tz "[timezone]" --session isolated
  ```

- **Interval watch** (e.g. check every 30 minutes): use `HEARTBEAT.md`:

  ```markdown
  tasks:
  - name: [task-id]
    interval: [e.g. 30m]
    prompt: "[short instruction]"
  ```

Omit this whole block for on-demand recipes.

---

## Step 3: Run it once

Before trusting it on a schedule, run it by hand and check the output:

> "[A one-off trigger prompt that produces the same result as the scheduled run, so the reader can sanity-check it now.]"

You should get [describe the expected good output]. If it looks right, the scheduled version will do the same on its own.

---

## Make it yours

A few easy variations:

- **[Variation]** - [one line on how to change the prompt or apps to get it]
- **[Variation]** - [...]

---

## Tuning & safety

- [The most likely thing to get wrong and how to fix it, e.g. tone of drafted replies, too noisy, wrong threshold.]
- [The permission worth double-checking for this recipe, e.g. "never let it send without showing you first."]
- [Where to teach it your preferences so corrections stick: memory / SOUL.md.]

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
