---
name: change-triage
description: Decide whether a merged PR, a new release, or a labeled issue actually needs a documentation update. Use before planning any doc work.
---

# Change triage

Given a PR, a release, or a labeled issue, decide if it is doc-worthy and what it touches. I read everything with the `gh` CLI and `git` (see `AGENTS.md`).

## Find candidates
Since my last run (skip anything already in `docs-update-state.md`):
- Merged PRs: `gh pr list --repo <owner/repo> --state merged --search "merged:>=<date>" --json number,title,mergedAt`.
- Releases: `gh release list --repo <owner/repo>` then `gh release view <tag>`.
- Labeled issues: `gh issue list --repo <owner/repo> --label docs --state open --json number,title,labels`.

## Read both sides
- For a PR: `gh pr view <n> --json title,body` and `gh pr diff <n>` for the full diff. The removed lines (the `-` side) often say as much as the added lines about what the docs should now reflect.
- For a release: `gh release view <tag>` for the tag, title, and notes, and the PRs it includes.
- For a labeled issue: `gh issue view <n> --comments` for the body and any comments. The request is whatever the human is asking for.

## Doc-worthy
Plan docs for: new features, changed defaults, new or removed config/flags, API changes, breaking changes, security fixes.

## Not doc-worthy
Skip: internal refactors, test-only changes, formatting, and dependency bumps with no user-facing effect.

## Apply preferences
Read `MEMORY.md` first and honor saved rules (for example "we don't document dependency bumps"). When in doubt, skip and note why; a stale doc is better than a wrong one.

## Output
For each item: doc-worthy yes/no, a one-line reason, and (if yes) which user-facing behavior changed. Record the item in `docs-update-state.md` as soon as it is analyzed, including skips.
