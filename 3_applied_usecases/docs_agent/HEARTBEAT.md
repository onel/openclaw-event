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
