---
name: write-docs
description: After a plan is approved, make the documentation edits on a branch and open a pull request. Also handles change requests on an open doc PR.
---

# Write docs

Only run this once a plan is approved in its thread. Approval is `/docs go` (or a plain "go" in a thread I own). Sign every comment with my name.

Every action runs through the `gh` CLI against the GitHub API. I never clone the repo or use local `git` (see `AGENTS.md`). All commands take `--repo <owner/repo>`.

## Open the PR (all through gh api, no local git)
The `git/ref` and `git/refs` below are GitHub REST API endpoint paths called via `gh api`, not local git commands.
1. Get the default branch head SHA: `gh api repos/<owner/repo>/git/ref/heads/<default-branch> --jq .object.sha`.
2. Create the doc branch with the prefix from `USER.md`: `gh api repos/<owner/repo>/git/refs -f ref=refs/heads/docs/<short-name> -f sha=<sha>`.
3. For each doc file, make the edit exactly as the current plan says, touching documentation files only, following the `house-style` skill and saved preferences. To commit a file: read its current blob sha on the branch (`gh api "repos/<owner/repo>/contents/<path>?ref=docs/<short-name>" --jq .sha`), then write it with `gh api repos/<owner/repo>/contents/<path> -X PUT -f message="<commit msg>" -f branch=docs/<short-name> -f sha=<blob-sha> -f content="$(base64 -w0 <newfile>)"`. Omit `-f sha=...` when creating a brand-new file.
4. Open the PR: `gh api repos/<owner/repo>/pulls -f title="..." -f head=docs/<short-name> -f base=<default-branch> -f body="... Closes #<n> ..."`, referencing the source issue. Comment on the source issue with the PR link via `gh issue comment <n>`.
5. Leave the issue open until the PR is merged, then close it (`gh issue close <n>`) and clear it from `open_plans`.

## Respond to PR feedback
On an open doc PR, read comments with `gh pr view <n> --comments` and treat a maintainer review comment as a change request (or `/docs <note>` if others are in the thread):
- Make the requested edits on the same branch with another `gh api ... contents ... -X PUT` (fetch the fresh blob sha first), then reply with `gh pr comment <n>` summarizing what changed.
- Update `last_comment_handled` so the same comment is not acted on twice.

## Never
- Never merge the PR (no `gh pr merge`, no merge API call). Never touch code or the default branch. If the plan would require either, stop and ask in the thread.
