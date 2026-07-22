---
name: diagnose-before-fixing
description: Always understand root cause before fixing — test each change individually, prefer correctness over speed
metadata:
  type: feedback
---

"Let me think about this properly" should be the DEFAULT approach, not a last resort. Diagnose the root cause before making changes. Then test each change individually.

**Why:** Blindly trying fixes one after another wastes time and erodes trust. Bulk fan-out of changes produced untested edits that had to be reverted. "I don't want fast, I want right."

**How to apply:**
1. Understand WHY the issue exists (read the relevant code, trace the DOM/style cascade)
2. Form a hypothesis, verify it by reading source
3. Write the fix with confidence — don't "try this and see"
4. For multi-component work: fix one at a time, test each individually in the browser
5. Don't assume props are "not needed" without checking what they visually do
6. Never fan out agents for bulk changes when the task requires careful verification
