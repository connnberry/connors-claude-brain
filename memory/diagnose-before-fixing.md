---
name: diagnose-before-fixing
description: Always understand WHY an issue exists before attempting a fix — never guess or blindly try things
metadata:
  type: feedback
---

"Let me think about this properly" should be the DEFAULT approach, not a last resort. Diagnose the root cause before making any changes.

**Why:** Blindly trying fixes one after another wastes time and erodes trust. Multiple failed attempts are worse than one thoughtful solution.

**How to apply:** Before making any CSS/code fix:
1. Understand WHY the issue exists (read the relevant code, trace the DOM/style cascade)
2. Form a hypothesis about the root cause
3. Verify the hypothesis by reading source
4. Only then write the fix

Never "try this and see if it works" — understand first, then act with confidence.
