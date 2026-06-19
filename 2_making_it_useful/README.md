# OpenClaw: Making It Useful
### A guide for the Give(a)Go event series · Event 2 of 3

---

# Part One: The Tools

---

## 1. Give It Eyes: Adding a Search Provider

Out of the box your agent can reason, but its knowledge stops at its training cutoff and it can't pull in fresh information. A **search provider** fixes that: it lets the agent run live web searches, read current pages, and ground its answers in real sources instead of guessing.

This is the single highest-leverage upgrade you can make today. Almost every useful workflow - research, news briefings, fact-checking, price tracking, "what happened this week in X" - depends on it.

### Which provider to use

There are a few good options. They all do the same core job; they differ in price, quality, and how they're tuned.

| Provider | Best for | Free tier | Where to get a key |
|---|---|---|---|
| **Exa** | AI-native semantic search, finding specific pages/papers | Yes, generous to start | [exa.ai](https://exa.ai) |
| **Brave Search** | General web search, privacy-friendly, cheap | Yes, 2,000 queries/month | [brave.com/search/api](https://brave.com/search/api) |
| **Tavily** | Search built specifically for AI agents, clean results | Yes, 1,000 credits/month | [tavily.com](https://tavily.com) |
| **Perplexity** | Search plus a summarised answer in one call | Pay as you go | [docs.perplexity.ai](https://docs.perplexity.ai) |

**Our recommendation for today:** start with **Brave** or **Exa**. Brave has the most forgiving free tier for general use; Exa is noticeably better when you want the agent to find a specific kind of page or source.

You don't have to pick just one. It's fine to add two and let the agent choose.

### Getting a key

The flow is the same for all of them:

1. Sign up on the provider's site (the links above)
2. Find the **API Keys** section in their dashboard
3. Generate a key and copy it somewhere safe

### Connecting it to your agent

The easiest path, is to just ask the agent:

> "Install the Brave search skill and use this API key: `BSA...`"

It will find the right skill on ClawHub, install it, store the key, and start using it. From then on, when you ask something that needs current information, it will search automatically.

### Try it

Once it's connected, test it with something that has to be live:

- "What's the weather in Dublin right now?"
- "Find me three recent articles about [topic you care about] and summarise them"
- "What did [company] announce this week?"

If it comes back with current, sourced answers instead of "I don't have access to real-time information," you're set.

---

## 2. Sharpen Its Personality: Leveling Up Your SOUL.md

In Event 1 you wrote a first SOUL.md. Most people's first version is too thin - a name and a couple of tone notes. That's fine as a start, but the difference between a generic assistant and one that feels like *yours* is in the detail.

Reminder of where it lives:

```
~/.openclaw/agents/[your-agent-name]/SOUL.md
```

### What the official guide stresses

Straight from the [OpenClaw SOUL docs](https://docs.openclaw.ai/concepts/soul), the points worth internalising:

- **SOUL.md is voice and style, nothing else.** It controls tone, how strong its opinions are, brevity vs. elaboration, humour, and how blunt it's allowed to be. Operational rules - confirmations, permissions, what it's allowed to touch - belong in `AGENTS.md`, not here.
- **Keep it short and sharp, not long and vague.** Strong, specific instructions beat aspirational prose. "Have opinions, skip filler, call out bad ideas early" works; "be helpful and thoughtful" does nothing.
- **Cut the corporate hedging.** Tell it to drop "it depends" and "absolutely," to be funny when it's natural, and to stay concise unless depth genuinely matters.
- **Leave the wrong things out.** No personal histories, changelogs, security policies, or vague vibes that don't change behaviour. Those either belong elsewhere or nowhere.
- **Iterate it like code.** It's not a one-time magic document. Version it, tweak it, see what changes.
- **"Sharp is good. Annoying is not."** Personality isn't a licence for sloppiness, and the tone still has to fit the room - shared channels, public replies, and customer-facing messages all need to land well.

### What a thin SOUL.md misses

A first-draft SOUL.md usually says how the agent should *sound*. A good one also says how it should *think and act*. Things to add now that you've used it for a while:

- **Defaults for ambiguous requests.** "When I ask you to 'look into' something, do the research and come back with a recommendation, not just a list of links."
- **How it carries itself when acting.** How proactive, how cautious, how much it narrates. (The hard rules about *what it may actually do* - confirm before sending, never delete without asking - live in `AGENTS.md`; SOUL.md is about the manner, not the permissions.)
- **Format preferences.** "Default to short bullet summaries. Only write prose when I ask for it."
- **What to proactively flag.** "If you notice I have back-to-back meetings with no break, say so."
- **Things you keep correcting.** Every time you find yourself re-explaining something to the agent, that's a line that belongs in SOUL.md.

### The fastest way to improve it

Ask the agent to interview you:

> "Read my current SOUL.md, then ask me five questions that would help you serve me better. After I answer, propose an updated version."

This works remarkably well. The agent knows where its own instructions are vague and will ask exactly the questions that close the gaps.

### A worked example

A thin line like:

```markdown
Be helpful and concise.
```

becomes something the agent can actually act on:

```markdown
## How I work
- Lead with the answer, then the reasoning if I need it. Don't bury the point.
- When you're not sure what I mean, make your best guess, act, and say what you assumed - don't stop to ask unless it's risky or expensive.
- I read these on my phone most of the time. Keep replies short. Bullets over paragraphs.
- If a task will take more than a few minutes, tell me up front and start - don't wait for me to say go.
```

Changes take effect at the start of the next session - send a message to trigger one.

---

## 3. Give It a Real Memory

SOUL.md is *who the agent is*. Memory is *what the agent knows about your world*. The two are different, and people often cram facts into SOUL.md that really belong in memory.

### The workspace files, revisited

| File | What it holds |
|---|---|
| `SOUL.md` | Personality, tone, behavioural rules |
| `MEMORY.md` | Long-term memory - things you want it to always know |
| `USER.md` | Structured facts about you: name, role, timezone, preferences |
| `AGENTS.md` | Operating rules and permissions |
| `HEARTBEAT.md` | Scheduled tasks, checked on a regular interval |
| `TOOLS.md` | The tools and integrations available to it |

### How memory actually works

The [official memory docs](https://docs.openclaw.ai/concepts/memory) make one principle very clear: **the agent only remembers what gets written to disk.** There is no hidden state. Memory is just plain Markdown files in the agent's workspace, and it's organised in layers:

| File | Role |
|---|---|
| `MEMORY.md` | Durable facts, preferences, and decisions. Loaded at the start of **every** session. Keep it curated and concise. |
| `memory/YYYY-MM-DD.md` | Daily working notes - detailed observations and session summaries. Indexed for search, but not auto-loaded every turn. |
| `DREAMS.md` (optional) | Consolidation summaries the agent promotes for you to review. |

The two layers do different jobs:

- **MEMORY.md** is the always-on, curated layer - the things worth loading every single session. Your role, main projects, key people, standing preferences.
- **Daily notes** are the detailed layer - context that's still useful later but doesn't need to be in front of the agent constantly. The agent searches them when relevant.

The flow between them matters: the agent jots detail into daily notes as it goes, then periodically **distils** the durable bits up into MEMORY.md and prunes what's stale. If MEMORY.md grows too large, OpenClaw truncates the copy it injects (the file stays intact) - that's your signal to push detail back down into daily notes.

### How it recalls and saves

- **Recall** happens through `memory_search` (hybrid vector + keyword search) and `memory_get` (direct retrieval). You don't call these - the agent does, automatically, when something jogs its memory.
- **Saving** is mostly hands-off. Before the context window compacts, OpenClaw runs a silent "flush" turn that prompts the agent to save anything important first, so it isn't lost. With **dreaming** enabled, it also promotes qualifying notes from daily files up into MEMORY.md in the background, logging them in DREAMS.md for you to review.
- **The core tip from the docs:** *"If you want your agent to remember something, just ask it."* It writes to the right file for you.

### Action-sensitive memories

One subtlety worth knowing: when a memory affects what the agent is allowed to *do* - an approval it's been granted, a temporary constraint, a handoff, an expiry - it should also capture the *conditions*: what changes behaviour, when it applies, when it expires, and who authorised it. A memory that says "Andrei approved auto-sending invoices" is dangerous without the "until end of Q3, for amounts under €500" attached.

### Making memory work for you

A few practices that make a big difference:

1. **Seed it deliberately.** Spend ten minutes today filling in MEMORY.md and USER.md with the things you'd otherwise re-explain. Who you are, what you're working on, the people and projects that come up often.

2. **Teach it to remember.** Add a line to SOUL.md like: *"When you learn something about me or my work worth knowing next time, save it. When I correct you, update memory so you don't repeat the mistake."* This turns every conversation into a chance to improve.

3. **Keep MEMORY.md lean, let daily notes hold the detail.** Don't paste transcripts or exhaustive archives into MEMORY.md. Distil; prune; move detail down a layer.

4. **For heavier search, look at QMD.** OpenClaw can run [QMD](https://docs.openclaw.ai/concepts/memory-qmd), a local-first search sidecar that adds BM25 indexing, vector search, reranking, and query expansion - all on your machine, no external APIs. It even lets the agent recall earlier session transcripts and index documents beyond your memory files. The builtin engine is fine for simple setups; reach for QMD when you want stronger recall, local processing, or search over past conversations.

### Try it

Tell the agent something it should remember, end the session, start a new one, and check that it stuck:

> "From now on, remember that I'm vegetarian and that 'the team' means Sarah, Dev, and Mei."

Next session: "Who's on the team?" If it answers correctly, memory is working.

---

## 4. Teach It New Tricks: Adding Skills

You installed your first skill in Event 1. Today we go wider. Skills are how the agent gains real capabilities - each one is a small package (usually a markdown doc plus some code) that teaches it to do a specific thing.

### Building a useful set

Rather than installing skills at random, think about your actual day and pick skills that cover the things you do most. A solid starter set for most people:

| If you spend time on... | Install... |
|---|---|
| Email | Gmail / Outlook |
| Scheduling | Google Calendar |
| Notes and docs | Notion / Obsidian / Google Drive |
| Tasks | Todoist / Linear |
| Code | GitHub |
| Staying informed | A news or RSS skill + your search provider |

### Installing

Just ask:

> "Install the Notion skill and walk me through connecting it"

Or via the CLI:

```bash
openclaw skills list        # see what's installed
openclaw skills install notion
```

### When there's no skill for it: ask the agent to build one

This is still the most surprising thing about OpenClaw. If ClawHub doesn't have what you want, describe it to the agent and it will write the skill itself:

> "I want a skill that checks the price of a product on a webpage every morning and messages me if it drops below a number I set. Build it."

It writes the files, installs them, and starts using them - in the same conversation. You don't need to know how to code; you need to know what you want.

### A note on permissions

As you add skills that can send emails, post messages, or spend money, revisit `AGENTS.md` and your SOUL.md confirmation rules. The more your agent can do, the more it matters that it checks with you before the actions you'd regret.

---

## 5. Connect Everything Else: Integrations

Search and skills cover a lot. But you almost certainly use apps that don't have a dedicated skill - your CRM, your bank, an internal tool, some niche app you love. This is where **integrations** come in, and it's the biggest unlock of the day.

### The idea

An integration is a bridge between your agent and another service's API. Instead of building that bridge by hand for every app, we use a platform that has already built thousands of them.

### Pipedream

[Pipedream](https://pipedream.com) is a service that connects to **2,000+ apps** and exposes them in a way your agent can use. It handles the authentication and the API details so you don't have to. They offer a **free account** that's plenty for personal use.

Crucially, Pipedream can act as an **MCP server** - a standard way for AI agents to use external tools. That means your OpenClaw agent can call any connected app through one consistent bridge.

### Setting it up

1. **Create a free account** at [pipedream.com](https://pipedream.com)
2. **Connect the apps you care about** in their dashboard - Gmail, Slack, Google Sheets, your CRM, whatever you use. Pipedream walks you through the login for each.
3. **Grab your MCP endpoint** from Pipedream (look for the MCP or Connect section)
4. **Tell your agent about it:**

   > "Add this MCP server so you can use my connected apps: `[your Pipedream MCP URL]`"

Once connected, the agent can read and write across every app you linked - without you needing a separate skill for each.

### What this makes possible

With Pipedream in the mix, requests like these become real:

- "When I get an email from a customer, add a row to my CRM spreadsheet"
- "Post a summary of today's calendar to my team Slack every morning"
- "Pull this week's Stripe revenue and put it in a Google Sheet"
- "If someone books a call via Calendly, draft a prep note in Notion"

### A word on trust

Integrations mean your agent can touch a lot of your accounts. Connect what you need, not everything. Keep the confirmation rules in your SOUL.md strict for anything that sends, posts, or pays. And remember that data sent to an external service has left your machine - connect services you're comfortable with the agent reaching.

---

# Part Two: Putting It Together

The tools above are ingredients. The point isn't to have them installed - it's to combine them into workflows that save you time. Here are patterns people actually run. Each one uses two or three of the pieces from Part One.

---

## 6. Workflow Examples

### A. The Morning Briefing

*Uses: search + calendar skill + memory + HEARTBEAT*

Every morning at 7am the agent messages you with: today's calendar, the weather, three headlines on topics you follow, and anything it flagged from yesterday. It knows what you care about because it's in memory; it gets the news through your search provider; it runs on schedule via HEARTBEAT.md.

> "Every weekday at 7am, send me a briefing: my calendar for the day, the Dublin weather, and the top three stories about AI and climate policy. Keep it short."

### B. Inbox Triage

*Uses: Gmail skill + SOUL.md rules + memory*

The agent reads your unread email, sorts it into "needs you," "FYI," and "ignore," drafts replies to the easy ones, and leaves the drafts for you to approve. Over time it learns your style from the edits you make - that's memory doing its job.

> "Go through my unread email. Summarise what needs a reply, draft responses for the straightforward ones, and don't send anything without showing me first."

### C. Research Assistant

*Uses: search provider + memory*

Instead of an afternoon of open tabs, you hand the question to the agent. It searches, reads the sources, and comes back with a structured summary and a recommendation - not just links. Because it's grounded in live search, the answer is current and cited.

> "Research the best options for [thing you're deciding on]. Compare the top three on price, reviews, and the criteria I care about, and tell me which you'd pick and why."

### D. The Cross-App Glue

*Uses: Pipedream integration + HEARTBEAT*

This is where integrations shine - connecting two apps that don't normally talk. A new lead in your CRM triggers a welcome email. A Stripe payment posts to a finance sheet. A calendar booking creates a prep doc. The agent sits in the middle and moves information where it needs to go.

> "Whenever a new contact is added to my CRM, draft a personalised intro email and save it as a draft in Gmail."

### E. The Standing Watch

*Uses: search + HEARTBEAT + messaging*

The agent watches something and only speaks up when it matters: a price drops, a competitor ships a feature, a flight gets cheaper, a website you depend on goes down, a keyword you track shows up in the news.

> "Check the price of [product/flight] every morning and message me only if it drops below [number]."

---

## 7. Build Your Own

Pick one workflow that would genuinely save you time this week. The recipe is the same every time:

1. **Name the outcome.** "I want X to happen so I don't have to do Y."
2. **Check the ingredients.** Which of today's tools does it need - search, a skill, an integration, the scheduler?
3. **Describe it to the agent in plain language.** Don't write code. Say what you want and let it build the skill or the automation.
4. **Run it once manually** to make sure it does the right thing.
5. **Put it on a schedule** (HEARTBEAT.md) if it should run on its own.
6. **Tune it.** The first version is rarely perfect. Correct it, and make sure the correction goes into memory or SOUL.md so it sticks.

The agents that become indispensable aren't the ones with the most skills installed. They're the ones with one or two workflows that quietly run every day and remove real work from someone's plate. Build one of those today.

---

## 8. Common Pain Points & Fixes

### Search returns nothing or errors

Check the API key is correct and that you haven't blown through the free tier. Most providers show your remaining quota in their dashboard. If one provider is rate-limited, add a second and let the agent fall back.

### The agent isn't remembering things

Two usual causes. First, you may be in a group chat - MEMORY.md only loads in private 1:1 sessions by default. Second, your SOUL.md may not tell it to save things. Add an explicit instruction to write learnings to memory.

### Pipedream / MCP connection fails

Make sure you copied the full MCP endpoint URL and that the apps you're trying to use are actually connected and authorised in the Pipedream dashboard. Re-authorise any app showing an expired connection.

### The agent does too much without asking (or asks about everything)

This is a SOUL.md tuning problem, not a bug. Be precise about where the line is: confirm before sending/posting/paying, act freely otherwise - or whatever balance suits you.

### A scheduled task didn't run

HEARTBEAT.md tasks run when the agent checks in on its interval. Make sure your machine was awake and the gateway was running at the scheduled time. For a laptop that sleeps, consider running OpenClaw on an always-on machine (a Mac Mini, Pi, or cheap cloud server).

---

## 9. What's Next: Event 3 Preview

**Event 3 - Applied Use Cases**

The final event takes everything you've built and points it at real work: email triage at scale, meeting prep, deeper research workflows, multi-agent setups where agents hand tasks to each other, and connecting OpenClaw to the tools your team uses.

Come with one workflow from today that you want to push further.

Keep an eye on [lu.ma/giveago](https://lu.ma/giveago) for the date.

---

## 10. Useful Links

| | |
|---|---|
| **Documentation** | [docs.openclaw.ai](https://docs.openclaw.ai) |
| **Skills marketplace** | [clawhub.ai](https://clawhub.ai) |
| **Pipedream** | [pipedream.com](https://pipedream.com) |
| **Exa search** | [exa.ai](https://exa.ai) |
| **Brave Search API** | [brave.com/search/api](https://brave.com/search/api) |
| **Tavily** | [tavily.com](https://tavily.com) |
| **SOUL.md spec** | [soul.md](https://soul.md) |
| **SOUL concept docs** | [docs.openclaw.ai/concepts/soul](https://docs.openclaw.ai/concepts/soul) |
| **Memory docs** | [docs.openclaw.ai/concepts/memory](https://docs.openclaw.ai/concepts/memory) |
| **QMD memory engine** | [docs.openclaw.ai/concepts/memory-qmd](https://docs.openclaw.ai/concepts/memory-qmd) |
| **Community Discord** | [discord.com/invite/clawd](https://discord.com/invite/clawd) |
| **Give(a)Go community** | [giveago.co](https://giveago.co) |

---

*Prepared for the Give(a)Go OpenClaw series · Event 2 · Dublin, June 2026*
