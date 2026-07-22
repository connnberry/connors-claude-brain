---
name: diagnose-before-fixing
description: Understand root cause before fixing, test each change individually, ask questions when unsure rather than guessing
metadata:
  type: feedback
---

"Let me think about this properly" should be the DEFAULT approach, not a last resort. Diagnose the root cause before making changes. Then test each change individually. If you're not sure about something — ask.

**Why:** Blindly trying fixes one after another wastes time and erodes trust. Bulk fan-out of changes produced untested edits that had to be reverted. "I don't want fast, I want right." Asking a quick question costs 10 seconds; guessing wrong costs 10 minutes.

**How to apply:**
1. Understand WHY the issue exists (read the relevant code, trace the DOM/style cascade)
2. Form a hypothesis, verify it by reading source
3. If uncertain about intent, direction, or approach — **ask the user**. Don't assume.
4. Write the fix with confidence — don't "try this and see"
5. For multi-component work: fix one at a time, test each individually in the browser
6. Don't assume props are "not needed" without checking what they visually do
7. Never fan out agents for bulk changes when the task requires careful verification
