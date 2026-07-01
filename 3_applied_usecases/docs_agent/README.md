# Docs Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Whenever a release ships, a PR merges, or someone files a `docs` issue, your agent reads what actually changed, works out which docs are now stale, and opens (or replies with) a GitHub plan. You review right on the thread: ask for changes and it revises, approve and it opens the doc PR. Everything happens through GitHub, and nothing touches your docs until you say go.

**Best for:** Engineers, technical writers, and small teams whose docs always drift behind the code.
**Time to set up:** ~15 minutes
**Runs:** Finds new work every 6h (releases, merged PRs, and `docs`-labeled issues), and follows up on its open plan issues and doc PRs every 3h. The doc work waits for your approval.

---

## What it does

Docs rot because nobody updates them at merge time. This agent closes that gap. It watches three things:

1. **New releases and merged PRs.** It reads each one (PR title, description, and diff; release tag, title, and notes) and decides which changes are documentation-worthy: new features, changed defaults, removed flags, security fixes, breaking changes. Internal refactors and cleanups it ignores.
2. **Issues you label `docs` or `documentation`.** A labeled issue is a direct request. The agent works the plan right in that issue instead of opening a duplicate.
3. **Your comments on the threads it is working in.** It re-checks its open plan issues and its open doc PRs, so a `/docs go` or a change request gets picked up and acted on. You address it with the `/docs` call-sign rather than an @-mention, and it signs its comments so it is clear who is replying.

For the changes that matter, it writes a plan: a checklist of files to touch, each with a short note on what needs to change and why. It posts that as an issue (or as a comment on your labeled issue). If you ask for changes, it revises and waits. If you approve, it creates a branch, makes the edits, and opens a pull request that references the thread. Nothing in your docs changes until you say go.

---

## How it is built

The recipe is a small OpenClaw workspace. You do not paste the whole procedure into a prompt; it lives in durable files the agent installs and re-reads every run. All of its GitHub work (reading PRs, releases, and issues; commenting; opening branches and PRs) runs through the `gh` CLI and `git`, which it installs and authenticates on the first run.

| File | What it is |
|---|---|
| `SOUL.md` | Identity, tone, and the hard boundaries (docs only, never merge, never ship without approval). |
| `AGENTS.md` | The operating rules: what it may do, the three triggers, and which skill to use when. |
| `USER.md` | Your config and conventions: repo, doc paths, watched labels, branch prefix, timezone, and the `/docs` call-sign. |
| `HEARTBEAT.md` | The schedule: two thin tasks, find-work every 6h and follow-up every 3h. |
| `docs-update-state.md` | The agent's own dedup and tracking file. It maintains this; you don't. |
| `skills/` | The reusable procedures: `change-triage`, `doc-plan`, `write-docs`, `house-style`. |

---

## What you'll need

| Piece | What for |
|---|---|
| **GitHub CLI (`gh`)** | How the agent does everything on GitHub: read merged PRs and diffs, read and label issues, create and comment on issues and PRs, open branches and PRs. The agent installs and authenticates it for you on the first run. |
| **A GitHub login/token** | Read/write on issues and pull requests for the repos you care about, but **not** merge or admin rights. Provided once via `gh auth login` or a `GH_TOKEN` env var. |
| **Search provider** | Not required. |
| **Messaging channel** | Optional. All interaction happens on GitHub; add Telegram/WhatsApp only if you want a nudge when a thread has been waiting on you. |

This agent works entirely through the `gh` CLI and `git`, so there is no hosted integration to connect for this recipe. Just make sure whatever login it authenticates with can read and write issues and pull requests but cannot merge or administer the repo.

---

## Step 1: Install the workspace

Paste this to your agent once.

```
You are my Docs Agent for the repo [owner/repo]. Your job is to keep the docs in
sync with the code by watching releases, merged PRs, and docs-labeled issues. You
never change docs without my approval, and GitHub is how we work together.

Set yourself up from this folder, do not make me run commands:
https://github.com/onel/openclaw-event/tree/master/3_applied_usecases/docs_agent

- Install and set up the GitHub CLI yourself: check whether `gh` is present and
  install it if not, authenticate (`gh auth login` or a `GH_TOKEN` env var, with
  read/write on issues and PRs but no merge rights), and `gh repo clone
  [owner/repo]` so you can read and edit docs directly. Confirm with `gh auth
  status`.
- Install SOUL.md, AGENTS.md, and HEARTBEAT.md into your workspace as-is.
- Install the skills under skills/ (change-triage, doc-plan, write-docs,
  house-style) into your workspace skills folder.
- Create USER.md from the template there and fill in my values: repo [owner/repo],
  default branch, doc paths, watched labels, branch prefix, and timezone.
- Create docs-update-state.md from its schema if it does not exist, and seed it
  with the last [7 days] of history.

HEARTBEAT.md runs two watches: find-work every 6h and follow-up every 3h. (If I
say I want fixed daily times instead, create the equivalent cron jobs with your
cron tool.) Confirm what you installed before doing anything else.
```

---

## Step 2: The files

You do not need to paste these by hand; the setup prompt above tells the agent to install them from this folder. They are listed in [the folder](https://github.com/onel/openclaw-event/tree/master/3_applied_usecases/docs_agent) so you can read and tweak them: [`SOUL.md`](./SOUL.md), [`AGENTS.md`](./AGENTS.md), [`USER.md`](./USER.md), [`HEARTBEAT.md`](./HEARTBEAT.md), [`docs-update-state.md`](./docs-update-state.md), and the [`skills/`](./skills) folder.

After install, the only file you edit is `USER.md` (your repo and conventions). Everything else works unchanged.

If you'd rather the find-work job run at a fixed time (say each weekday morning) instead of every 6h, tell the agent in the setup prompt and it creates a cron job itself, equivalent to:

```bash
openclaw cron create "0 9 * * 1-5" \
  "Find docs work for [owner/repo]: releases, merged PRs, and docs-labeled issues since yesterday. Post plans per AGENTS.md." \
  --name "Docs find-work" --tz "Europe/Dublin" --session isolated
```

Keep the follow-up on the 3h heartbeat either way, so your comments get picked up promptly.

---

## Step 3: Run it once

Before trusting it on a schedule, run each trigger by hand against recent history:

- **Releases / PRs:** "Look at the last 5 PRs merged into main on [owner/repo], plus the most recent release. Tell me which changes are documentation-worthy and which you'd skip, then draft (don't post) the plan you would open."
- **Labeled issue:** open a test issue, label it `docs`, and confirm it replies with a plan in that issue rather than opening a new one.
- **The loop:** on a real plan, comment `/docs drop item 2, and reword item 1` and confirm it revises rather than starting work; then comment `/docs go` and confirm it opens a branch and a PR that references the thread; then request a change on the PR and confirm it edits the same branch.

Check three things on the plans: did it separate doc-worthy changes from noise (a dependency bump should be skipped, a changed default should not), did it point at the right doc files, and is each note specific enough that the edit would be obvious.

---

## Make it yours

- **Watch several repos** - list them in `USER.md` and have it post one plan issue per repo.
- **Changelog instead of an issue** - point it at `CHANGELOG.md` and have it draft the entry rather than a plan issue.
- **README sync** - narrow it to "only flag changes that make the README's quickstart or config wrong."
- **Release-only mode** - skip the merged-PR watch and run only when a release is published.
- **Stricter filter** - only plan docs for changes labelled `feature` or `breaking`, so small fixes don't generate noise.

---

## Teach it what good docs look like

The `house-style` skill seeds the defaults; `MEMORY.md` overrides them with what you actually correct. Whenever you drop an item from a plan, edit a draft, or state a preference ("we don't document dependency bumps", "avoid marketing language in docs", "format code examples this way"), it saves a short durable rule and applies it next run. A few principles keep that memory useful:

- **Your corrections are the prime signal.** Direct statements teach it more than anything else.
- **Read what changed, not just what was added.** Removed lines in a diff often say as much as added ones.
- **Save few, high-quality facts.** A short, clear rule beats a long note, and saving nothing is fine.
- **Update, don't duplicate.** Refine an existing rule instead of adding a near-copy.

Be explicit in the first couple of weeks: when you reject a planned item or edit a draft, say why in one line. Those lines become the rules it follows from then on.

---

## Tuning & safety

- **Get the doc-worthy filter right first.** The whole value is in skipping noise and catching the changes that break docs. Spend the first week correcting `change-triage` so the calls land in memory.
- **The plan is the review gate, not the edits.** Nothing touches docs until you reply "go". A wrong doc edit auto-committed is worse than a stale doc.
- **Feedback isn't instant.** It picks up comments on its 3h follow-up, so it acts within a few hours, not the second you post. Shorten the interval for faster turnarounds, at a token cost.
- **Everything lives on GitHub on purpose.** Plans, feedback, and work are all in issues and PRs, so there is a full audit trail. It runs on the `gh` CLI and `git`, so give the login it authenticates with write access to issues and PRs but not merge rights.
- **Never let it near code or the default branch.** It edits documentation files only, and only on a branch as a PR. It never merges. Double-check those permissions.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
