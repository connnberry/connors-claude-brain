---
name: never-invent-apis
description: Only use real documented APIs and verified component props — never fabricate animations, effects, or assume props exist
metadata:
  type: feedback
---

Only use real, documented framework/library APIs. Never fabricate animations, effects, or component props. Always verify Neptune component props via the Wise Design System MCP before writing JSX.

**Why:** A fake "materialise" animation was once presented as a native capability when it didn't exist. Separately, guessing component props leads to broken UI or runtime errors. Neptune components update frequently — props you remember may have changed or never existed.

**How to apply:**
1. Before using any Neptune component: call `list-all-documentation` then `get-documentation` from the MCP
2. If implementing visual effects, only use documented APIs — if something needs custom code, be explicit that it's custom
3. If unsure whether an API exists, say so rather than guessing
4. If the MCP is unavailable, tell the user rather than proceeding from memory
