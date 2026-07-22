---
name: never-invent-apis
description: Only use real documented APIs — never fabricate animations, effects, or component props that don't exist
metadata:
  type: feedback
---

Only use real, documented framework/library APIs. Never fabricate animations, effects, or component props.

**Why:** A fake "materialise" animation was once presented as a native capability when it didn't exist. This erodes trust immediately — the designer can't tell what's real vs invented without verifying every claim.

**How to apply:** When implementing visual effects or using component APIs:
1. Only use documented APIs you can verify exist
2. If something needs custom code, be explicit that it's custom — don't frame it as native
3. If unsure whether an API exists, say so rather than guessing
4. Verify Neptune component props via the Wise Design System MCP before writing JSX
