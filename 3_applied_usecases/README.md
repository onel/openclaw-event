# OpenClaw: Applied Use Cases

*Event 3 of 3 · Give(a)Go x AI Collective Dublin · Sponsored by Bronto, BrowserUse, Tensorix*

📍 **Location:** Baseline, 3rd Floor, 61 Thomas St, The Liberties, Dublin 8

🕒 **Time:** 11:00 AM – 5:00 PM

📅 **Date:** 27 June 2026

---

**GitHub repo:** github.com/onel/openclaw-event

---

### A guide for the Give(a)Go event series · Event 3 of 3

In Event 1 you set OpenClaw up. In Event 2 you made it useful: search, a sharper SOUL.md, real memory, skills, and integrations. Event 3 is where it stops being a toy and starts removing real work from your plate.

This event is a library, not a lecture. Each use case below is a self-contained recipe: connect a couple of apps, paste in a setup prompt, drop in a small rules block, and you have a working agent for that job. Pick the one closest to your world, get it running today, then adapt the pattern to the rest.

---

## Sponsors

<table>
  <tr>
    <td align="center"><img src="https://github.com/user-attachments/assets/774a46d5-27a0-490c-b7d0-e65fcbbfa358" alt="Browser Use" height="60"></td>
    <td align="center"><img src="https://bronto.io/assets/bronto-logo-DbWPAuNL.png" alt="Bronto" height="60"></td>
    <td align="center"><img src="https://tensorx.ai/wp-content/uploads/2026/05/tensorx-logo.svg" alt="Tensorx" height="60"></td>
  </tr>
</table>

---

## How these recipes work

Every recipe follows the same three-step shape, so once you have done one the rest are fast:

1. **Connect the apps** through [Composio](https://composio.dev) (or Pipedream, or any other "connector app"). Composio exposes 860+ tools your agent can call through a single MCP connection.
2. **Paste the setup prompt.** A plain-language brief that tells the agent what the use case is and how you want it to behave. No code.
3. **Drop in the rules block.** A few lines for `AGENTS.md` (permissions and guardrails) and, where the job runs on a schedule, `HEARTBEAT.md`.

That is the whole pattern. The recipes differ only in which apps they connect and what the agent is told to do.

### One-time setup: connect Composio

You will do this once, then reuse it across every recipe.

1. Create a free account at [composio.dev](https://composio.dev) and get your API key from the dashboard
2. Connect the apps you want the agent to reach (Gmail, Slack, Notion, GitHub, your CRM, and so on). Composio handles the login and tokens for each.
3. Add Composio to your agent as an MCP server. The simplest way is to just ask the agent:

   > "Add an MCP server named `composio`, transport HTTP, URL `https://connect.composio.dev/mcp`, with the header `x-consumer-api-key: [your Composio API key]`."

   That single endpoint gives the agent access to every app you connect in Composio.

From then on, every recipe can use any app you have connected. If a recipe needs an app you have not connected yet, connect it in Composio and it becomes available without touching the agent again.

> See [composio.dev/claw](https://composio.dev/claw) for the full OpenClaw setup, including alternative connection methods and per-app permission scopes.

> **Coming from Event 2?** You may already have Pipedream wired up. Composio works the same way (an MCP bridge); these recipes standardize on Composio for its tool coverage, but the pattern is identical if you prefer to stay on Pipedream.

---

## The recipes

| # | Use case | What it does | Best for |
|---|---|---|---|
| 1 | [Personal Ops Agent](./personal_ops_agent.md) | Morning brief from calendar + inbox + notes, drafts replies, tracks follow-ups | Everyone |
| 2 | [GTM / Content Workflow Agent](./gtm_content_agent.md) | Turns raw ideas into LinkedIn drafts, newsletter outlines, and CRM follow-ups | Founders, marketers, sales |
| 3 | [Support / Ticket Triage Agent](./support_triage_agent.md) | Classifies incoming tickets, drafts replies, routes and summarizes | Support, ops, small teams |
| 4 | [Daily Sports Digest](./sports_digest.md) | A scheduled digest for a competition you follow (e.g. World Cup 2026) | Anyone, great first build |
| 5 | [Event Radar](./event_radar.md) | Watches Luma / event sources and pings you about ones that match | Community, networking, GTM |
| 6 | [Weather & Conditions Alert](./weather_alert.md) | Watches conditions and only speaks up when a threshold is crossed | Outdoor, ops, logistics |
| 7 | [House Search Agent](./house_search_agent.md) | Scores new property listings against your criteria and summarizes the keepers | Anyone house-hunting |
| 8 | [Docs Agent](./docs_agent/README.md) | Watches merged PRs and releases, plans the doc updates, and opens issues + PRs | Engineers, technical writers |

More recipes will be added over time. The template below is what each one follows, so you can write your own.

---

## Write your own recipe

Every recipe in this folder is built from the same template: [`_TEMPLATE.md`](./_TEMPLATE.md). To build a use case we have not covered:

1. Copy `_TEMPLATE.md` to a new file.
2. Fill in the apps to connect, the setup prompt, and the rules block.
3. Run it once by hand, then put it on a schedule if it should run on its own.

The agents that become indispensable are not the ones with the most apps connected. They are the ones running one or two recipes that quietly save real time every day.

---

## Useful links

| | |
|---|---|
| **Composio** | [composio.dev](https://composio.dev) |
| **Documentation** | [docs.openclaw.ai](https://docs.openclaw.ai) |
| **Skills marketplace** | [clawhub.ai](https://clawhub.ai) |
| **Event 2 guide** | [Making It Useful](../2_making_it_useful/README.md) |
| **Community Discord** | [discord.com/invite/clawd](https://discord.com/invite/clawd) |

---

*Prepared for the Give(a)Go OpenClaw series · Event 3 · Dublin, 2026*
