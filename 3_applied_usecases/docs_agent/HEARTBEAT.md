tasks:
- name: docs-find-work
  interval: 6h
  prompt: "Find new work for the repo in USER.md. Check merged PRs, new releases,
    and open issues carrying a watched label, all since your last run (skip
    anything in docs-update-state.md). Use the change-triage and doc-plan skills.
    Follow AGENTS.md. If nothing is doc-worthy, do nothing."
- name: docs-followup
  interval: 3h
  prompt: "Check comments on the plan issues and doc PRs you have open. Requested
    changes -> revise. Approval -> use the write-docs skill. Follow AGENTS.md.
    Otherwise do nothing."
