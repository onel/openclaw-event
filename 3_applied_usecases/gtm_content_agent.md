# GTM / Content Workflow Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** You drop a rough idea into a notes page. By the time you come back, the agent has turned it into a LinkedIn draft, a newsletter outline, and a CRM follow-up, all grounded in current context and all waiting for your approval before anything goes out.

**Best for:** Founders, marketers, and salespeople who have more ideas than time to package them.
**Time to set up:** ~15 minutes
**Runs:** When a new idea appears (watch), or on demand. Nothing publishes without you.

---

## What it does

Most good content dies as a half-formed note. This agent closes the gap between "I have a thought" and "it's published." You keep a single place for raw ideas: a Notion page, a Google Doc, a folder. The agent watches it. When a new idea shows up, it pulls relevant market and news context through your search provider, then turns the raw idea into finished drafts: a LinkedIn post in your voice, a newsletter outline, a short research digest, and, where the idea is about a person or account, a CRM follow-up. It does the 80% of content work that is not the creative spark: research, framing, repurposing into each channel's format.

Then it stops and asks. Nothing posts, sends, or updates a record until you say yes. You stay the editor; the agent is the drafting machine.

---

## What you'll need

| Piece | What for |
|---|---|
| **Composio apps** | Notion or Google Drive (your idea source), LinkedIn (draft posts) |
| **Optional Composio apps** | Gmail or a mailing tool (newsletter), HubSpot or your CRM (follow-ups), Twitter/X |
| **Search provider** | Required. This recipe leans on live market and news context. |
| **Messaging channel** | Telegram or WhatsApp, to receive drafts for approval |

### Connect these apps in Composio

Assumes you have done the one-time Composio MCP setup in the [folder README](./README.md).

- **Notion** or **Google Drive** - the agent reads your idea source from here and can write the finished drafts back as a new page or doc.
- **LinkedIn** - the agent prepares posts. It drafts and shows you; it does not auto-post.
- **Gmail** or your **mailing tool** *(optional)* - for newsletter outlines and sending, only after approval.
- **HubSpot** or your **CRM** *(optional)* - to draft a follow-up against the right contact or deal.
- **Twitter/X** *(optional)* - to repackage the same idea as a thread.

Start with Notion plus LinkedIn plus a search provider. Add the CRM and mailing pieces once the core loop feels good.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts.

```
You are my GTM / Content Agent. I keep raw content ideas in [my Notion page
called "Ideas"]. Your job is to turn each rough idea into finished, on-brand
drafts that I only need to approve, not write from scratch.

When I point you at an idea (or you find a new one in the Ideas page), do this:

1. UNDERSTAND IT: Read the rough note. If it's thin, infer the angle I'm going
   for. Don't ask me to clarify unless it's genuinely ambiguous.

2. ADD CONTEXT: Use web search to pull current, relevant context - recent news,
   a stat, what others are saying, a counterpoint. Ground the content in
   something real and current. Cite sources to me, not in the post.

3. PRODUCE DRAFTS, in my voice:
   - A LinkedIn post: [hook-first, 1-2 short paragraphs, no hashtags spam,
     no "thoughts?" filler]
   - A newsletter outline: a working title plus 3-5 bullet sections
   - A short research digest: 3-5 bullets of the context you found, with links
   - If the idea is about a specific person, company, or account I'm pursuing,
     a CRM follow-up note or a draft outreach email

4. SHOW ME: Send all of it to me on [Telegram] for review. Save the drafts to
   [a new Notion page under "Drafts"] so I can edit there too.

My voice: [direct, specific, a bit contrarian. I lead with the point. I don't
do corporate hedging or "in today's fast-paced world" openers.]

Hard rule: never publish a LinkedIn post, send a newsletter, send an email, or
update a CRM record without showing me the exact content and getting an explicit
yes. Draft everything; send nothing on your own.

When I edit a draft, learn from the edit and save what changed about my voice or
preferences to memory.
```

---

## Step 2: The rules block

**`AGENTS.md`** (permissions and guardrails):

```markdown
## GTM / Content Agent
- May read my Ideas source and write drafts to my Drafts location freely.
- May run web searches freely.
- Must NEVER publish to LinkedIn/Twitter, send a newsletter or email, or update
  a CRM record without showing me the exact content and getting an explicit yes.
- Keep a consistent brand voice; when I edit a draft, save the change to memory.
- Cite sources to me privately; do not paste raw URLs into published copy.
```

**The schedule (HEARTBEAT watch).** This recipe reacts to new ideas rather than running at a fixed time, so a heartbeat watch fits better than cron. Add to `HEARTBEAT.md`:

```markdown
tasks:
- name: idea-watch
  interval: 1h
  prompt: "Check my Ideas page for any new idea added since last check. If there
    is one, run the full GTM content workflow on it and send me the drafts. If
    nothing new, do nothing."
```

If you would rather batch it (say, draft everything once a day at 9am instead of reacting hourly), use cron instead:

```bash
openclaw cron create "0 9 * * 1-5" \
  "Process any new ideas in my Ideas page into drafts and send them to me." \
  --name "Daily content drafts" --tz "Europe/Dublin" --session isolated
```

---

## Step 3: Run it once

Test it on a single idea before letting it watch:

> "Take this idea and run the full workflow on it, but don't post or send anything: '[paste one rough idea here]'."

You should get a LinkedIn draft in your voice, a newsletter outline, a short sourced digest, and (if relevant) a CRM follow-up, all in one message. Check the voice and whether the added context is accurate and current. Edit one draft and confirm the agent saved what it learned. Once the drafts are usable with light edits, turn on the watch.

---

## Make it yours

- **Pick your channels** - drop LinkedIn for Twitter/X threads, or add an Instagram caption. Just change the "PRODUCE DRAFTS" list.
- **GTM news monitoring** - add a second task: "every morning, summarise what moved in [my market] and flag anything worth a post."
- **Approval in the channel** - tell it to format drafts so you can reply "ship 1 and 3" and it posts only those.
- **Repurpose old wins** - "once a week, find a past post that did well and pitch me a fresh angle on it."
- **Tone presets** - keep two voices ("punchy" vs "thoughtful") in memory and let you pick per idea.

---

## Tuning & safety

- **Voice is everything here.** Spend the first few drafts correcting tone hard and explicitly. The agent converges fast once corrections are in memory, and a post that sounds like you is the whole point.
- **Guard against generic, AI-flavoured copy.** Tell it plainly what you never want: no "in today's landscape," no empty "thoughts?", no hashtag stuffing. Bad content published in your name is worse than no content.
- **Never auto-publish.** Public posts and outreach are hard to take back. Keep the approval gate even after you trust it; one wrong auto-post to LinkedIn is a bad day.
- **Check the facts it adds.** Live context is the recipe's strength and its risk. Confirm any stat or claim before you approve a post built on it.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
