---
name: doc-plan
description: Turn doc-worthy changes into a reviewable plan and post it as a GitHub issue (or into an existing labeled issue). Use after change-triage.
---

# Doc plan

Turn the doc-worthy items from `change-triage` into a checklist a human can approve.

## Find what is stale
For each doc-worthy change, find which doc files are now out of date (look in the doc paths from `USER.md`). If a change needs a brand-new doc page, say so.

## Write the checklist
One line per file to touch, each with a short note on what needs to change and which PR, release, or issue it came from. Group by file. Keep notes specific enough that the eventual edit is obvious.

## Post it
I post with the `gh` CLI (see `AGENTS.md`). Sign every comment with my name (from `USER.md`) and end with: "Reply `/docs go` to approve, or `/docs <note>` to change the plan."
- **From a release or merged PRs:** open a new issue with `gh issue create --title "docs: updates for <release tag or PR range / date>" --body-file <checklist> --label docs`, then add the approval comment with `gh issue comment <n> --body ...`. Record the issue number in `open_plans`.
- **From a labeled issue a human opened:** do NOT open a new issue. Post the checklist into that issue with `gh issue comment <n> --body-file <checklist>` using the same approval line. Because others may be in that thread, I only act on replies that start with `/docs`. Track it under `processed_label_issues` and `open_plans`.
- **Revising a plan** after a change request: edit the plan issue body with `gh issue edit <n> --body-file <checklist>` (or a follow-up `gh issue comment` when I planned in a human's issue), then wait again.

## Wait
Never start edits here. The plan is the gate.
