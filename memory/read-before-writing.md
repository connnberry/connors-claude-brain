---
name: read-before-writing
description: Don't write code until you fully understand the task, have read the relevant source, and are confident in your approach — confirm with the user if not
metadata:
  type: feedback
---

Do not write code until you're 100% sure what you're building and how you're delivering it. Read the relevant source, understand the task fully, and confirm your approach before touching any files.

**Why:** Jumping into code without understanding leads to wrong implementations that have to be reverted. A session once burned 39 minutes making wrong edits when the answer was in a doc the user had already provided. Starting before confirming direction means wasted work when the delivery doesn't match expectations.

**How to apply:**
- Confirm you understand what's being asked before writing anything — if unclear, ask
- Read actual source code (not just types) before writing overrides or modifications
- Read documentation the user shares FULLY before implementing
- Check existing patterns in the codebase before creating new ones
- Wait for explicit "go ahead" / "do it" / "implement it" before editing files
- If you're not confident in the approach, say so — propose what you'd do and let the user confirm
- Never assume — verify how things work, and verify what "done" looks like
