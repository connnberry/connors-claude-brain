---
name: commit-after-progress
description: Commit locally after any significant change to save progress — don't wait until end of session
metadata:
  type: feedback
---

Commit locally after any meaningful unit of work (feature addition, bug fix, style change).

**Why:** A session once lost hours of uncommitted work when a destructive git operation was run. Frequent local commits provide safety checkpoints.

**How to apply:** After completing a meaningful change, create a local commit without being asked. For prototype repos, push straight to main (no branches or PRs needed). Use `• ` (bullet character) for commit body lists — it renders correctly in Slack notifications.
