<!--
Research playbook for writing Event 3 recipes.
Not published to attendees: this is our authoring reference.
Before writing any recipe, work through the source tiers below.
House rule: no em dashes.
-->

# Recipe research sources

Where to look, in order, when writing each use-case recipe. The goal is recipes that actually run, not thin marketing examples. Always cross-check at least one official source against one real-world source before publishing.

## Tier 1: Official OpenClaw (ground truth for how things work)

Use these to get the mechanics right: file formats, scheduling, skills, permissions. Never describe a mechanism from memory; confirm it here.

- **Docs home** - https://docs.openclaw.ai
- **Personal assistant setup** - https://docs.openclaw.ai/start/openclaw
- **Automation overview** - https://docs.openclaw.ai/automation
- **Heartbeat (scheduling)** - https://docs.openclaw.ai/gateway/heartbeat
- **HEARTBEAT.md template** - https://docs.openclaw.ai/reference/templates/HEARTBEAT
- **Default AGENTS.md** - https://docs.openclaw.ai/reference/AGENTS.default
- **Skills** - https://docs.openclaw.ai/tools/skills
- **Creating skills** - https://docs.openclaw.ai/tools/creating-skills
- **Thinking levels** - https://docs.openclaw.ai/tools/thinking
- **Token use and costs** - https://docs.openclaw.ai/reference/token-use

Key facts already confirmed:
- Heartbeat is a periodic main-session turn, default every 30m (1h for Anthropic OAuth/token auth). Keep heartbeat entries tiny.
- Skills are markdown (SKILL.md + YAML frontmatter); keep them concise, watch for command injection if they use exec.

## Tier 2: Composio (our integration layer, ground truth for connecting apps)

- **OpenClaw setup page** - https://composio.dev/claw
- **Composio MCP + OpenClaw guide** - https://composio.dev/content/how-to-use-composio-mcp-with-openclaw
- **Composio framework page** - https://composio.dev/toolkits/composio/framework/openclaw
- **Per-app toolkit pages** (one per integration we use), e.g.:
  - GitHub - https://composio.dev/toolkits/github/framework/openclaw
  - World News API - https://composio.dev/toolkits/world_news_api/framework/openclaw
  - Find any app's page at composio.dev/toolkits/[app]/framework/openclaw

**Correction to apply to our README** (verify before locking):
- Composio advertises **860+ tools**, not "250+". Update the figure.
- The real connection is an MCP server named `composio`, transport **HTTP**, URL **`https://connect.composio.dev/mcp`**, with header **`x-consumer-api-key: <your-api-key>`**. Our README's "grab your MCP URL from the dashboard" is roughly right but we should give the exact URL + header.
- There are 3 ways to enable Composio in OpenClaw; the MCP-server method above is the primary one to document.

## Tier 3: Real-world recipe collections (for ideas, flows, and what people actually run)

Mine these for the shape of each workflow, then rebuild on our own template + Composio. Do not copy thin examples verbatim.

- **ClawBytes (KiloClaw cookbook)** - https://kilo.ai/kiloclaw/bytes
  - Copy-paste prompt + required tools + setup tips per recipe. Closest format to ours, but many are thin. Good skeletons, shallow depth.
  - The specific ones the user flagged: FIFA/World Cup digest, ClawBite AI, Luma event monitor, GassiWeather alert.
- **KiloClaw by-role recipe pages** - https://kilo.ai/kiloclaw/openclaw-for (150+ recipes), plus role pages: beginners, devops, lawyers, scientists, architects, designers, pentesters. Good for domain-specific flows.
- **awesome-openclaw-usecases (community, verified)** - https://github.com/hesamsheikh/awesome-openclaw-usecases
  - Contributors must have run a use case for at least a day before submitting. Higher signal than marketing listicles. Categories: social media, creative/building, infra/devops, productivity, research/learning, finance/trading.

## Tier 4: Listicles and roundups (breadth, sanity-checks, framing)

Lower signal, useful for confirming a use case is common and for audience language. Do not trust mechanics from these.

- Hostinger - https://www.hostinger.com/tutorials/openclaw-use-cases
- KDnuggets (7 practical) - https://www.kdnuggets.com/7-practical-openclaw-use-cases-you-should-know
- Sphere Partners (100 use cases) - https://www.sphereinc.com/blogs/100-openclaw-use-cases-you-can-try-today
- Forward Future (25+ real) - https://forwardfuture.ai/p/what-people-are-actually-doing-with-openclaw-25-use-cases
- Kanerika (15 that work) - https://kanerika.com/blogs/openclaw-usecases/

## Tier 5: Adjacent automation ecosystems (for workflow design patterns, not OpenClaw specifics)

When a use case maps to a well-trodden automation (CRM updates, content repurposing), borrow the trigger/branch/output design from mature tools, then implement agent-native in OpenClaw.

- Zapier templates and Make scenarios (trigger-action and branching patterns)
- n8n / Activepieces (open-source workflow graphs)
- Notion Custom Agents, ClickUp Super Agents (how incumbents frame scheduled autonomous jobs)

## Authoring checklist per recipe

1. Find 2-3 real versions of the workflow in Tier 3 (ClawBytes + awesome repo).
2. Confirm every mechanism (heartbeat, skills, permissions) against Tier 1.
3. Confirm the exact Composio apps exist and note their toolkit pages (Tier 2).
4. Write our version on `_TEMPLATE.md`: setup prompt + AGENTS.md/HEARTBEAT.md block.
5. Add value the thin examples miss: guardrails, run-once test, tuning, variations.
