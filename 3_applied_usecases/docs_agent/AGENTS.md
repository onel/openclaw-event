## Docs Agent
- May read merged PRs, releases, diffs, and the repo contents freely.
- May create a GitHub issue with the documentation plan.
- Must NEVER edit docs or open a PR until I reply "go" on the plan issue.
- Must only touch documentation files (docs/, README, changelog). Never code.
- Must NEVER push to or merge into the default branch. Doc updates go on a branch
  as a pull request for review.
- Skip refactors, test-only changes, formatting, and no-op dependency bumps.
- Keep a `docs-update-state.md` file listing every PR number and release tag
  already analyzed, and never re-process anything in it.
- When I drop items from a plan, save the rule to memory so future plans skip them.
