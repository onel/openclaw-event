# Docs Agent
### Applied use case · OpenClaw Event 3

> **One-line outcome:** Whenever PRs get merged or a release ships, your agent reads what actually changed, works out which docs are now stale, and opens a GitHub issue with a clear plan. You review and reply right on the issue: ask for changes and it revises the plan, approve it and it opens the doc PR. Everything happens through GitHub, and nothing touches your docs until you say go.

**Best for:** Engineers, technical writers, and small teams whose docs always drift behind the code.
**Time to set up:** ~15 minutes
**Runs:** Checks for new merged PRs and releases every 6h, and follows up on its open plan issues every 3h. The doc work waits for your approval.

---

## What it does

Docs rot because nobody updates them at merge time. This agent closes that gap. On a schedule it looks at two things since it last ran: the PRs merged into your main branch, and any new releases that were published. It reads each one (PR title, description, and diff; release tag, title, and notes) and decides which changes are actually documentation-worthy: new features, changed defaults, removed flags, security fixes, breaking changes. The cleanups and internal refactors it ignores.

For the changes that matter, it figures out which doc files are now out of date and writes a plan: a checklist of files to touch, each with a short note on what needs to change and why. It posts that plan as a GitHub issue. From there, GitHub is where the two of you talk: every few hours it re-checks its open issues for your comments. If you ask for changes, it revises the plan in the issue and waits again. If you approve, it creates a branch, makes the edits, and opens a pull request that references the issue. Nothing in your docs changes until you say go.

The effect is that "update the docs" stops being the task everyone forgets after a merge, and becomes a reviewed plan waiting in your issues list, handled entirely through GitHub.

---

## What you'll need

| Piece | What for |
|---|---|
| **Composio apps** | GitHub (read merged PRs and diffs, create and comment on issues, open branches and PRs) |
| **Search provider** | Not required. |
| **Messaging channel** | Optional. All interaction happens on GitHub; add Telegram/WhatsApp only if you want a nudge when an issue has been waiting on you. |

### Connect these apps in Composio

Assumes you have done the one-time Composio MCP setup in the [folder README](../README.md).

- **GitHub** - the only app this recipe needs, and the channel you both work in. The agent reads merged PRs and diffs, creates and comments on the plan issue, reads your replies there, and (after your approval) creates a branch and opens the doc PR. Make sure the connection has read/write access to issues and pull requests on the repo or repos you care about.

If you have not connected Composio to your agent yet, do the one-time setup in the [folder README](../README.md) first.

---

## Step 1: The setup prompt

Paste this to your agent once. Edit the bracketed parts to fit your repo.

```
You are my Docs Agent for the repo [owner/repo]. Your job is to keep the
docs in sync with the code by watching merged PRs and new releases. You never
change docs without my approval.

GitHub is how we work together. You post plans as GitHub issues, I respond in the
issue comments, and you do the work as pull requests. Treat each issue thread as
our conversation: read my comments there, reply there, and don't move work
forward until I've approved it there. [Optional: only ping me on [Telegram] if an
issue has been waiting on me for more than a day.]

First, set yourself up:
- Read https://github.com/onel/openclaw-event/blob/master/3_applied_usecases/docs_agent/AGENTS.md
  and append its contents to your AGENTS.md - these are your permissions and guardrails.
- Set up your schedule yourself, don't make me run commands. Read
  https://github.com/onel/openclaw-event/blob/master/3_applied_usecases/docs_agent/HEARTBEAT.md
  and use it as your HEARTBEAT.md. It runs two watches: finding new work every 6h,
  and following up on open plan issues every 3h. (If I say I want fixed daily
  times instead, create the equivalent cron jobs with your cron tool.)
Replace [owner/repo] (and the timezone) with my actual values. Confirm what you
installed before doing anything else.

== JOB A: find new work (runs every 6h) ==

1. FIND WHAT'S NEW: First read your tracking file `docs-update-state.md` (in your
   workspace) for the list of PR numbers and release tags you've already analyzed.
   Then list two things from the repo:
   (a) PRs merged into [main], and
   (b) releases published on the repo (read each release's tag, title, and notes,
       and the PRs it includes).
   Drop anything already in the tracking file. If this is the first run and the
   file doesn't exist, create it and use the last [7 days]. If nothing new is left
   after filtering, stop and say so.

2. READ THE CHANGES: For each merged PR and each release, read the title,
   description/notes, and the diff. Read both sides of the diff: removed lines
   (the `-` side) often say as much as added lines about what the docs should now
   reflect. Decide what is documentation-worthy: new features, changed defaults,
   new or removed config/flags, API changes, breaking changes, and security fixes.
   Ignore internal refactors, test-only changes, formatting, and dependency bumps
   that have no user-facing effect. Apply my saved documentation preferences from
   memory when you decide this (see "Always"), so you skip what I've told you to skip.

3. PLAN THE DOC UPDATES: For each documentation-worthy change, find which doc
   files are now stale (look in [docs/, README.md]). Produce a checklist: one
   line per file to update, each with a short note on what needs to change and
   which PR it came from. If a change needs a brand-new doc page, say so.

4. POST THE PLAN: Open a GitHub issue titled "docs: updates for [release tag or
   merged PRs / date range]". The body is the checklist from step 3, grouped by
   file, with a one-line summary of each underlying change and the PR or release
   it came from. Label it [docs] and add a comment asking me to act in the thread:
   "Reply 'go' to approve and I'll start, or tell me what to change." Record the
   issue number in your `docs-update-state.md` open-plans list.

5. RECORD WHAT YOU PROCESSED: Add every PR number and release tag you analyzed in
   this run to `docs-update-state.md`, including the ones you skipped as not
   doc-worthy, so you never look at them twice. Record them as soon as you've
   analyzed them in step 2, even if no plan gets posted.

== JOB B: follow up on open plan issues (runs every 3h) ==

For each open plan issue you created (from your open-plans list), read any new
comments since you last checked, then:

- IF I REQUESTED CHANGES: update the plan to match. Edit the issue body so the
  checklist reflects what I asked for, then comment summarizing what you changed
  and ask me to confirm again. Do NOT start any work yet.
- IF I APPROVED (e.g. "go", "looks good", "approved"): start the work. Create a
  branch named [docs/<short-name>], make the doc edits exactly as the current
  plan says, and open a pull request that references the issue. Comment on the
  issue with the PR link. Leave the issue open until the PR is merged, then close
  it and remove it from your open-plans list.
- IF I ASKED A QUESTION or there's nothing actionable: answer in the thread and
  wait. Never start work without an explicit approval comment.

Record the last comment you handled per issue so you never act on it twice.

== ALWAYS: learn my doc preferences ==

Get better over time at what I accept and reject as good documentation. Whenever I
drop an item from a plan, request a change on an issue, edit one of your drafts,
or state a preference ("we don't document dependency bumps", "avoid marketing
language in docs", "always format code examples this way"), save it as a short,
durable rule to memory. Before saving, read existing memory and refine a matching
rule instead of adding a near-duplicate. Keep only a few high-quality preferences,
and don't invent one when a change teaches you nothing new. Read these preferences
at the start of every run and apply them.
```

---

## Step 2: The rules files

You do not need to paste these in by hand. The setup prompt above already tells the agent to read both files straight from this GitHub folder and install them. They are shown here so you know what the agent is installing, and so you can tweak them after.

> Files: [`AGENTS.md`](./AGENTS.md) and [`HEARTBEAT.md`](./HEARTBEAT.md) in [this folder](https://github.com/onel/openclaw-event/tree/master/3_applied_usecases/docs_agent). After the agent installs them, replace `[owner/repo]` with your repo.

**`AGENTS.md`** (permissions and guardrails):

```markdown
## Docs Agent
- GitHub is the main way we work: post plans as issues, read and reply in issue
  comments, deliver work as pull requests.
- May read merged PRs, releases, diffs, and the repo contents freely.
- May create issues, comment on them, and edit a plan issue's body to revise the plan.
- May create a branch and open a pull request, but ONLY after I approve the plan in
  the issue. Never before, and never merge the PR yourself.
- Must only touch documentation files (docs/, README, changelog). Never code.
- Must NEVER push to or merge into the default branch. Doc updates go on a branch
  as a pull request for review.
- Check open plan issues for new comments every 3h: requested changes -> update the
  plan; approval -> start the work.
- Skip refactors, test-only changes, formatting, and no-op dependency bumps.
- Keep a `docs-update-state.md` file with analyzed PRs/releases and open plan
  issues, and never re-process anything in it.
- When I drop or change plan items, save the rule to memory so future plans follow it.
```

**The schedule.** You don't run anything by hand: the setup prompt has the agent install the schedule itself. By default it's two interval watches in `HEARTBEAT.md` - find new work every 6h, follow up on open issues every 3h:

```markdown
tasks:
- name: docs-find-work
  interval: 6h
  prompt: "Job A: Check [owner/repo] for PRs merged into main and new releases
    since your last run. If there are documentation-worthy changes, post a plan
    issue and ask me to approve or request changes in the thread. If nothing new
    or nothing is doc-worthy, do nothing."
- name: docs-followup
  interval: 3h
  prompt: "Job B: Check the open plan issues you've created for new comments. If I
    requested changes, update the plan. If I approved, create a branch, make the
    doc edits, and open a PR that references the issue. Otherwise do nothing."
```

If you'd rather the find-work job run at a fixed time (say, each weekday morning) instead of every 6h, tell the agent so in the setup prompt and it creates a cron job itself with its cron tool, equivalent to the below. Keep the issue follow-up on the 3h heartbeat either way, so your comments get picked up promptly.

```bash
openclaw cron create "0 9 * * 1-5" \
  "Check [owner/repo] for PRs merged and releases published since yesterday and post a docs-update plan issue if needed." \
  --name "Docs update plan" --tz "Europe/Dublin" --session isolated
```

---

## Step 3: Run it once

Before trusting it on a schedule, run it by hand against recent history:

> "Look at the last 5 PRs merged into main on [owner/repo], plus the most recent release. Tell me which changes are documentation-worthy and which you'd skip, then draft (don't post) the doc-update plan you would open as an issue."

Check three things: did it correctly separate doc-worthy changes from noise (a dependency bump should be skipped, a changed default should not), did it point at the right doc files, and is each note specific enough that the eventual edit would be obvious. Deliberately include a PR that changed a default or removed a flag and confirm it catches it.

When the plans look right, let it post a real plan issue, then test the full GitHub loop: comment "drop item 2, and reword item 1" and confirm that on its next follow-up run it revises the plan in the issue rather than starting work; then comment "go" and confirm it opens a branch and a PR that references the issue. Once that round-trip works, turn on both watches.

---

## Make it yours

- **Watch several repos** - give it a list of repos in the prompt and have it post one plan issue per repo.
- **Changelog instead of an issue** - point it at `CHANGELOG.md` and have it draft the entry for the merged PRs rather than a plan issue.
- **README sync** - narrow it to "only flag changes that make the README's quickstart or config section wrong."
- **Release-only mode** - skip the merged-PR watch and have it run only when a release is published, covering every PR since the last tag in one plan.
- **Stricter filter** - tell it to only plan docs for changes labelled `feature` or `breaking`, so small fixes don't generate noise.

---

## Teach it what good docs look like

The "learn my doc preferences" part of the setup prompt already turns this on: the agent learns what you accept and reject as good documentation and applies it on every run. This section just explains the thinking behind it, so you know what it is doing and can correct it well. The point of memory here is to capture your project's documentation preferences so future plans match your house style without you re-explaining. A few principles that keep that memory useful:

- **Your corrections are the prime signal.** When you drop an item from a plan, edit a draft, or say things like "we don't document dependency bumps", "always avoid marketing language in docs", or "format code examples this way", treat that as a preference worth saving. These direct statements teach it more than anything else.
- **Read what changed, not just what was added.** In a merged PR, removed lines (the `-` side of the diff) often say as much as added ones about what the docs should now reflect. Look at both.
- **Save few, high-quality facts.** Aim for the handful of durable preferences per change, not a log of everything. A short, clear rule beats a long note.
- **Update, don't duplicate.** Before saving, check existing memory for a fact that already covers it and refine that one instead of adding a near-copy.
- **Saving nothing is fine.** If a change teaches you nothing new about how this project wants its docs, don't invent a preference.

The fastest way to build this memory is to be explicit in the first couple of weeks: when you reject a planned item or edit a draft, say why in one line. Those lines become the rules the agent follows from then on.

---

## Tuning & safety

- **Get the doc-worthy filter right first.** The whole value is in skipping noise and catching the changes that actually break docs. Spend the first week correcting it ("this refactor didn't need docs", "you missed this changed default") so the calls land in memory.
- **The plan is the review gate, not the edits.** Keep the rule that nothing touches docs until you reply "go" on the issue. A wrong doc edit auto-committed is worse than a stale doc.
- **Feedback isn't instant.** The agent picks up your issue comments on its 3h follow-up, so it acts within a few hours of your reply, not the second you post. Shorten the follow-up interval if you want faster turnarounds, but every run costs tokens.
- **Everything lives on GitHub on purpose.** Plans, your feedback, and the work are all in issues and PRs, so there's a full audit trail and nothing depends on a chat history. Make sure the GitHub connection has write access to issues and PRs but not merge rights.
- **Never let it near code or the default branch.** It edits documentation files only, and only on a branch as a PR. It never merges. Double-check those permissions.
- **Teach it your conventions.** Where docs live, how the changelog is formatted, what you never bother documenting: save these to memory or `SOUL.md` so plans match your house style without re-explaining.

---

*Part of the Give(a)Go OpenClaw series · Event 3 · Applied Use Cases*
