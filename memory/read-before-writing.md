---
name: read-before-writing
description: Always read source code, documentation, and component implementations before writing or modifying anything — never assume how things work
metadata:
  type: feedback
---

Do not write code until you've read the relevant source. When the user shares documentation or says to research something, READ it fully and discuss findings BEFORE touching any code. Wait for explicit "go ahead" / "do it" / "implement it" before editing files.

**Why:** Jumping into code without reading leads to wrong implementations that have to be reverted. A session once burned 39 minutes making wrong edits when the answer was in a doc the user had already provided. Separately, writing overrides without reading component source led to broken sizing because the actual rendering model (hardcoded pixel values, not em units) was different from what was assumed.

**How to apply:**
- Research first, confirm understanding, then implement
- Read actual JS/ESM source (not just `.d.ts` types) before writing overrides
- Read a component's CSS to understand how sizing and layout actually work
- Check existing patterns in the codebase before creating new ones
- Never assume — verify how things render in practice
