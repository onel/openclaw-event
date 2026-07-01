---
name: write-docs
description: After a plan is approved, make the documentation edits on a branch and open a pull request. Also handles change requests on an open doc PR.
---

# Write docs

Only run this once a plan is approved in its thread. Approval is `/docs go` (or a plain "go" in a thread I own). Sign every comment with my name.

All edits and GitHub actions run through `git` and the `gh` CLI (see `AGENTS.md`).

## Open the PR
- `git pull` on the default branch, then create a branch using the prefix in `USER.md`: `git checkout -b docs/<short-name>`.
- Make the edits exactly as the current plan says, touching documentation files only. Follow the `house-style` skill and saved preferences.
- Stage and commit only doc files (`git add <doc paths>`, `git commit`), then `git push -u origin docs/<short-name>`.
- Open the PR with `gh pr create --title ... --body ...`, referencing the source issue in the body (for example `Closes #<n>` or `Refs #<n>`). Comment on the source issue with the PR link via `gh issue comment <n>`.
- Leave the issue open until the PR is merged, then close it (`gh issue close <n>`) and clear it from `open_plans`.

## Respond to PR feedback
On an open doc PR, read comments with `gh pr view <n> --comments` and treat a maintainer review comment as a change request (or `/docs <note>` if others are in the thread):
- Make the requested edits on the same branch, `git commit` and `git push`, then reply with `gh pr comment <n>` summarizing what changed.
- Update `last_comment_handled` so the same comment is not acted on twice.

## Never
- Never merge the PR (no `gh pr merge`). Never touch code or the default branch. If the plan would require either, stop and ask in the thread.
