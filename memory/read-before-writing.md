---
name: read-before-writing
description: Always read source code, documentation, and component implementations before writing or modifying anything
metadata:
  type: feedback
---

Do not write code until you've read the relevant source. When the user shares documentation or says to research something, READ it fully and discuss findings BEFORE touching any code. Wait for explicit "go ahead" / "do it" / "implement it" before editing files.

**Why:** Jumping into code without reading leads to wrong implementations that have to be reverted. A session once burned 39 minutes and many tokens making wrong edits when the answer was in a doc the user had already provided.

**How to apply:** Research first, confirm understanding, then implement. This applies to:
- Component source code (read the actual JSX/CSS, not just types)
- Documentation links the user shares
- Existing patterns in the codebase before creating new ones
