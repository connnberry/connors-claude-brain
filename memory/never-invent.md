---
name: never-invent
description: Never fabricate APIs, component names, props, tokens, or colour values — verify everything exists before using it
metadata:
  type: feedback
---

Never make things up. This applies to APIs, component props, component names, token names, colour values, and design system patterns. If you haven't verified it exists, don't use it.

**Why:** A fake "materialise" animation was once presented as a native capability when it didn't exist. Separately, guessing token names leads to invisible styling (the variable resolves to nothing), and inventing component names leads to import errors the designer has to debug. Neptune components update frequently — what you remember may be wrong.

**How to apply:**
1. Before using any Neptune component: call `list-all-documentation` then `get-documentation` from the Wise Design System MCP
2. Never hardcode hex colours — use `var(--color-*)` tokens, and verify the token name exists in the project's token docs
3. Never invent component names — if you're not sure `<PromoCard>` or `<InlineAlert>` exists, check the MCP
4. Never assume a prop exists because it "sounds right" — verify it
5. If implementing visual effects, only use documented APIs — if custom, be explicit that it's custom
6. If unsure about anything: say so, or ask. Never guess silently.
