---
name: never-delete-without-preserving
description: Never delete or revert work without preserving it first — stash, branch, or copy before removing
metadata:
  type: feedback
---

Never delete work without preserving it first.

**Why:** Completed work was accidentally destroyed by a destructive git operation. Unrecoverable.

**How to apply:**
- Always `git stash -u` or create a branch before reverting changes
- Never `git checkout --` or `rm` files that represent completed work without confirming the user has what they need
- When moving code to a new location: copy first, verify it works, THEN remove from the original
- When separating concerns: preserve the original state before restructuring
