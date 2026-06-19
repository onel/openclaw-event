# OpenClaw: Getting It Alive
### A setup guide for the Give(a)Go event series · Event 1 of 3

---

## 1. What Is OpenClaw?

OpenClaw is a personal AI agent that runs on your own machine - a laptop, a Mac Mini, a Raspberry Pi, or a cloud server - and actually does things. It reads and writes files, browses the web, sends emails, manages your calendar, runs code, and connects to almost any app you already use.

What makes it different from ChatGPT or Claude.ai:

- **It runs on your hardware.** Your data doesn't go through someone else's cloud unless you choose it.
- **You reach it through chat apps you already use** - WhatsApp, Telegram, Discord, Signal, iMessage. No new interface to learn.
- **It remembers you.** Context persists between sessions. It becomes more useful the longer you use it.
- **It takes actions, not just answers.** It can execute tasks in the background, on a schedule, without you asking each time.
- **It's open source and extensible.** You can install community-built skills, or ask the agent to write new ones for you.

The goal today: leave with OpenClaw running, connected to a messaging app, with a personality you've written, doing at least one real thing.

---

## 2. Before You Come: Pre-flight Checklist

Do these before arriving. It will save you 20–30 minutes on the day.

### Check your Node.js version

OpenClaw is a Node.js application - Node is the runtime that executes it on your machine. You don't interact with Node directly; it's just the engine under the hood.

```bash
node --version
```

You need **v22.19 or higher**. v24 is recommended. If you don't have it or your version is older, install it: [nodejs.org/en/download](https://nodejs.org/en/download)

### Get an API key

OpenClaw needs access to an AI model to think and respond.

We've partnered with **Tensorx.ai** to give every attendee free credits for open source models - no billing setup needed.

#### Setting up your Tensorx account

1. Use the signup link provided at the event. You will need to create a new account for this to work
2. After Signup, go to **API Keys** and:
- enter a name for they (eg. my openclaw API key)
- click Generate Key
3. Copy the key - it starts with `tx-` - and keep it somewhere handy

**You'll paste this key into the onboarding wizard when you install OpenClaw.**

#### Other providers
If you'd prefer to use a provider you already have an account with, any of these work too:

| Provider | Where to get it |
|---|---|
| **Anthropic** | [console.anthropic.com](https://console.anthropic.com) |
| **OpenAI** | [platform.openai.com](https://platform.openai.com) |
| **Google** | [aistudio.google.com](https://aistudio.google.com) |
| **Other** | [docs.openclaw.ai/providers](https://docs.openclaw.ai/providers) - 60+ supported |

The rest of this guide assumes Tensorx. If you're using a different provider, the steps are the same - you'll just pick your provider from the list instead of entering a custom URL.

### Windows users

Both native Windows and WSL2 are supported. **WSL2 is strongly recommended** - it gives you a Linux-style environment and is more stable for the full OpenClaw experience. See the [Windows setup guide](https://docs.openclaw.ai/platforms/windows) if you haven't set up WSL2 before.

---

## 3. Install and Run Onboarding

Follow the [step-by-step setup guide](./setup/README.md) - it covers the install command, the onboarding wizard, and connecting Telegram, with screenshots for every step.

---

## Other things

### Choosing a Model

When setting up OpenClaw you will be asked what model you prefer.

You can change the model any time in the dashboard under **Settings → Model**. The model ID format is `provider/model-id`, for example `anthropic/claude-sonnet-4-6` or `openai/gpt-4o`.

**A note on costs:** The Tensorx free credits cover normal use during the event. If you switch to a paid provider (Anthropic, OpenAI, etc.), API usage isn't free - a reasonably active day typically costs $0.50–$2. Set a spend limit in your provider's dashboard if you're concerned about runaway costs.

### Where it lives

```
~/.openclaw/agents/[your-agent-name]/SOUL.md
```

You can edit it with any text editor, or ask the agent itself to help you write it.

### What to put in it

There's no rigid format. It's a plain text description of the assistant you want. A few things worth including:

- **A name** - give it one if you want; most people do
- **Tone and style** - formal, casual, dry, warm, direct, verbose, whatever fits you
- **What it should always do** - e.g. "always confirm before sending emails", "always ask before deleting files"
- **What it should never do** - hard limits you want enforced
- **Context about you** - your job, your timezone, your main tools, your working style
- **Anything it should proactively do** - e.g. "if I mention I'm stressed, suggest I take a break"

### Starter template

```markdown
# [Agent Name]

You are [Name], a personal assistant for [your name].

## Tone
You communicate in a direct, warm tone. No corporate speak. Short sentences. 
You can be lightly funny but never try too hard.

## Behaviour
- Always confirm before sending emails or messages on my behalf
- Never delete files without explicit confirmation
- If you're unsure what I want, ask one clarifying question rather than guessing

## Context
- I'm based in Dublin, Ireland (UTC+1 in summer)
- I work in [your field/role]
- My main tools are [e.g. Gmail, Notion, GitHub]

## What I care about
[Write a few sentences about what matters to you and what you want this agent to help with]
```

Changes to SOUL.md take effect the next time a new session starts - send a message to trigger it.

---

## 4. Writing Your SOUL.md

SOUL.md is the file that defines your agent's personality - its tone, values, how it communicates, and what kind of assistant it is. OpenClaw reads this file at the start of every session.

Without a SOUL.md your agent is generic. With one, it feels like yours.


---

## 5. Setting Up MEMORY.md

OpenClaw has a set of workspace files that control different aspects of your agent. MEMORY.md is where curated long-term memory lives - facts and context you want the agent to always have access to.

The full set of files and what they do:

| File | What it controls |
|---|---|
| `SOUL.md` | Personality, tone, and behavioural rules |
| `MEMORY.md` | Long-term memory - things you want it to always know |
| `USER.md` | Structured facts about you: name, role, preferences |
| `AGENTS.md` | Operating rules and permissions |
| `HEARTBEAT.md` | Scheduled tasks - the agent reads this every 30 minutes and executes anything whose time has come |
| `TOOLS.md` | Documents the tools and integrations available to it |

### MEMORY.md in practice

Think of it as seeding the agent with context it would otherwise need to figure out from scratch. Things worth putting in:

- Who you are and what you do
- Your current projects and their status
- Key people in your life/work and who they are
- Tools, accounts, and services it has access to
- Recurring things you ask it to do
- Things it has learned about you that you want it to retain

The agent will also update MEMORY.md itself over time as it learns things worth keeping. You can edit it directly to correct or remove anything.

**Important:** MEMORY.md should only load in private sessions (your 1:1 DMs with the agent), not group chats. This is the default - don't change it.

---

## 6. Your First Skill

Skills are plugins that extend what your agent can do. They're plain text files - usually a markdown doc and some code - that live in your agent's workspace.

### Finding skills

Browse the community marketplace at **[clawhub.ai](https://clawhub.ai)** - there are hundreds of skills for email, calendars, GitHub, Obsidian, weather, news, home automation, and much more.

### Installing a skill

The easiest way is to just ask your agent:

> "Install the Gmail skill from ClawHub"

It will find it, confirm with you, and install it. You can also use the CLI:

```bash
openclaw skills install gmail
```

### A first skill to try today

Pick one thing you actually want your agent to do. Some ideas that work well as a first skill:

- **Gmail** - have it summarise your unread emails
- **Google Calendar** - ask what's on your schedule this week
- **Weather** - get a morning briefing
- **Obsidian** - connect it to your notes
- **GitHub** - track open PRs or issues

### Ask the agent to build a skill

One of the wildest things about OpenClaw: the agent can write its own skills. If you can't find what you want on ClawHub, just describe what you want to a running agent and ask it to build it. It will write the skill files, install them, and start using them - all in the same chat.

---

## 7. Common Pain Points & Fixes

Things people hit during setup, and how to get past them.

### "command not found: openclaw" after install

Close your terminal completely and open a fresh one. The install script updates your PATH variable but the running terminal won't pick it up until it restarts.

### Node version is too old

Run `node --version`. If it's below v22.19, install a newer version from [nodejs.org](https://nodejs.org). On a Mac, using `nvm` (Node Version Manager) makes it easy to switch versions without breaking anything.

### API key errors during onboarding

Double-check you copied the full key with no trailing spaces. Anthropic keys start with `sk-ant-`, OpenAI keys start with `sk-`. If onboarding accepted the key but requests are failing later, check your spend limit or billing status in your provider's dashboard.

### Gateway won't start / port conflict

Port 18789 is the default. If something else is using it:

```bash
openclaw gateway status  # check what's happening
openclaw gateway restart # try a restart first
```

If you have another OpenClaw instance running (e.g. from a previous install), stop it first with `openclaw gateway stop`.

### WhatsApp keeps disconnecting

This is a known issue. WhatsApp's connection sometimes drops, especially if your machine goes to sleep. Re-scanning the QR code reconnects it. If it's happening constantly, keep your machine from sleeping while the agent is active, or consider using Telegram instead for the event.

### Windows: plugin installs failing with `spawn npm ENOENT`

This is the most common Windows-specific bug. It happens on native Windows installs because npm is a batch file wrapper (`npm.cmd`) that Node's spawn can't find without special handling. The fix is WSL2 - switch to it and the issue disappears. If you're mid-event and need a quick fix, update to the latest OpenClaw version first (`openclaw update`) as recent releases include a workaround.

### Windows: "running scripts is disabled"

Open PowerShell as Administrator and run:

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Then retry the install.

### WSL2: can't reach the dashboard from Windows browser

The Gateway runs inside WSL2 but is accessible from Windows. Try `http://localhost:18789` in your Windows browser. If that doesn't work, find your WSL2 IP with `ip addr show eth0` inside the Ubuntu terminal and use that address instead.

---

## 8. What's Next: Event Series Preview

This is the first of three events. Each one builds on the last.

**Event 1 - Getting It Alive** (today)
Install, connect, personalise, and get one real skill running.

**Event 2 - Getting Productive**
Going deeper on prompting your agent well, setting up automations and scheduled tasks via HEARTBEAT.md, and building habits around using it daily.

**Event 3 - Real Work Use Cases**
Applying OpenClaw to actual work: email triage, meeting prep, research workflows, multi-agent setups, and connecting it to the tools your team uses.

Keep an eye on [luma.com/giveago](https://lu.ma/giveago) for the next event dates.

---

## 9. Useful Links

| | |
|---|---|
| **Documentation** | [docs.openclaw.ai](https://docs.openclaw.ai) |
| **GitHub** | [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw) |
| **Community Discord** | [discord.com/invite/clawd](https://discord.com/invite/clawd) |
| **Skills marketplace** | [clawhub.ai](https://clawhub.ai) |
| **SOUL.md spec** | [soul.md](https://soul.md) |
| **Node.js install** | [nodejs.org/en/download](https://nodejs.org/en/download) |
| **Getting started docs** | [docs.openclaw.ai/start/getting-started](https://docs.openclaw.ai/start/getting-started) |
| **OpenClaw on X** | [@openclaw](https://x.com/openclaw) |
| **Give(a)Go community** | [giveago.co](https://giveago.co) |

---

*Prepared for the Give(a)Go OpenClaw series · Event 1 · Dublin, June 2026*