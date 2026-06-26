# Support / Ticket Triage Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Every incoming support request gets read, classified, matched against your docs, and given a draft reply within minutes, with a ticket created and routed to the right person. You approve replies and handle only the hard ones. At day's end you get a summary of what's still open.

**Best for:** Support teams, ops people, and small teams where support lands on whoever has time.
**Time to set up:** ~20 minutes
**Runs:** Watches for new requests through the day, plus a daily summary. Replies wait for approval.

---

## What it does

Support is mostly triage: figuring out what a message is, how urgent it is, whether you already have an answer, and who should handle it. This agent does that triage the moment a request arrives. It reads each new ticket or form submission, classifies it by type and urgency, searches your knowledge base for a known answer, and drafts a reply grounded in your own docs. It creates or updates a ticket in your tracker and routes it to the right owner. The easy, answerable requests come to you as ready-to-send drafts; the genuinely hard or sensitive ones get flagged for a human with the context already gathered. At the end of the day it tells you what is still unresolved and what is aging.

The effect is a support queue that is already sorted, drafted, and routed before a person looks at it, instead of a wall of unread messages.

---

## What you'll need

| Piece | What for |
|---|---|
| **Composio apps** | Intake source (Gmail, or Zendesk/Freshdesk), Notion or Google Drive (your knowledge base) |
| **Optional Composio apps** | Jira or Linear (ticket tracking), Slack (routing and pings) |
| **Search provider** | Optional. Only if answers sometimes need public/web info beyond your docs. |
| **Messaging channel** | Telegram or WhatsApp, for draft approvals and the daily summary |

### Connect these apps in Composio

Assumes you have done the one-time Composio MCP setup in the [folder README](./README.md).

- **Gmail** or **Zendesk/Freshdesk** - where requests come in. The agent reads new ones; it never sends a reply without approval.
- **Notion** or **Google Drive** - your knowledge base. The agent searches your docs and FAQs to ground replies in real answers, not guesses.
- **Jira** or **Linear** *(optional)* - so each request becomes a tracked ticket with type, priority, and owner.
- **Slack** *(optional)* - to route a ticket to the right person and post the daily summary.

A great first version is just Gmail plus a Notion knowledge base. Add the tracker and Slack routing once classification is reliable.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts.

```
You are my Support Triage Agent. You process incoming support requests so a
human only has to handle the hard ones. For each new request in [my support
inbox / Zendesk]:

1. CLASSIFY:
   - Type: [bug / how-to / billing / feature request / account / other]
   - Urgency: [P1 broken-for-customer / P2 important / P3 routine]
   - One-line summary of what they actually want.

2. LOOK IT UP: Search my knowledge base in [Notion] for a known answer. If you
   find one, base your reply on it and tell me which doc you used. If there is
   no good answer, say so - do not invent one.

3. DRAFT A REPLY: Write a reply in our support voice: [friendly, clear, gets to
   the fix fast, no corporate padding]. For anything you're confident about,
   draft a complete reply. For anything sensitive (refunds, complaints, legal,
   security, an angry customer), do NOT draft a confident reply - flag it for a
   human and gather the context they'll need.

4. TRACK + ROUTE: Create or update a ticket in [Jira/Linear] with the type,
   urgency, summary, and the draft. Route it to [the right owner / team] and, if
   set up, ping [the Slack channel] for P1s.

5. HAND TO ME: Send me the draft replies for approval on [Telegram], grouped by
   urgency. I reply with which to send. Send nothing on your own.

At [6pm my timezone] each day, send me a summary: what came in, what's resolved,
what's still open and aging, and anything stuck waiting on a human.

Hard rules: never send a reply to a customer without my explicit yes. Never make
promises about refunds, timelines, or policy. When I edit a draft or recategorise
a ticket, learn from it and save the correction to memory.
```

---

## Step 2: The rules block

**`AGENTS.md`** (permissions and guardrails):

```markdown
## Support Triage Agent
- May read the support inbox, my knowledge base, and the ticket tracker freely.
- May create and update tickets and route them. Must confirm before closing one.
- Must NEVER send a reply to a customer without my explicit yes.
- Must NOT commit to refunds, timelines, discounts, or policy. Flag those to a human.
- For sensitive cases (complaints, legal, security, angry customers), flag, don't
  auto-draft a confident answer.
- Ground every reply in a knowledge-base doc; if there's no source, say so.
- When I recategorise a ticket or edit a draft, save the correction to memory.
```

**The schedule (two parts).** New requests need a frequent watch (HEARTBEAT), and the summary is a fixed time (cron).

Add to `HEARTBEAT.md`:

```markdown
tasks:
- name: support-triage
  interval: 15m
  prompt: "Check for new support requests since last check. For each, classify,
    look up the answer, draft a reply, create/route a ticket, and send me drafts
    for approval. If nothing new, do nothing."
```

Create the daily summary as a cron job (ask the agent, or run on the CLI):

```bash
openclaw cron create "0 18 * * 1-5" \
  "Send me the end-of-day support summary: incoming, resolved, open and aging, blocked." \
  --name "Support EOD summary" --tz "Europe/Dublin" --session isolated
```

---

## Step 3: Run it once

Before letting it watch the queue, test on real-but-safe examples:

> "Take the last 3 requests in my support inbox and run the full triage on them, but do not send anything and do not close any tickets. Show me how you'd classify, what doc you'd use, and the draft reply for each."

Check three things: is the classification right, did it find the correct knowledge-base doc, and is the draft something you would actually send. Deliberately include a tricky one (a refund or complaint) and confirm it flags rather than confidently answers. When triage is reliable, turn on the 15-minute watch.

---

## Make it yours

- **Form submissions too** - point the intake at a Typeform/Google Form inbox, not just email.
- **Auto-send the truly safe ones** - once you trust it, allow it to send replies for one narrow category (for example, password-reset how-tos) while everything else still waits for approval. Tighten this carefully.
- **Knowledge gaps report** - "each week, list the questions you couldn't answer from our docs, so we know what to write."
- **SLA watch** - "flag any P1 open more than 1 hour and any P2 open more than a day."
- **Tone per channel** - a warmer voice for consumer email, terser for a developer support queue.

---

## Tuning & safety

- **Get classification right before anything else.** If urgency and type are wrong, everything downstream is wrong. Correct miscategorisations explicitly the first week so they land in memory.
- **The knowledge base is the quality ceiling.** Replies are only as good as the docs they are grounded in. If drafts are weak, the fix is usually better docs, not a better prompt. Use the "knowledge gaps" variation to find what to write.
- **Never let it improvise policy.** Refunds, timelines, and legal answers are exactly where an eager agent does damage. The rules above forbid it; keep them.
- **Approval gate stays on.** A wrong reply sent to a customer is public and remembered. Keep replies behind your yes until you have strong evidence a category is safe to automate, and even then scope it tightly.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
