tasks:
- name: docs-update
  interval: 6h
  prompt: "Check [owner/repo] for PRs merged into main and for new releases since
    your last run. If there are documentation-worthy changes, post a plan issue
    and ask me to confirm. If nothing new or nothing is doc-worthy, do nothing."
