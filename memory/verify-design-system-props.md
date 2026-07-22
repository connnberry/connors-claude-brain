---
name: verify-design-system-props
description: Always verify component props via the Wise Design System MCP — never guess or assume props from memory
metadata:
  type: feedback
---

Before using any Neptune component from `@transferwise/components`, verify its props via the Wise Design System MCP:

1. Call `list-all-documentation` to find the component ID
2. Call `get-documentation` with that ID to get the real props

**Why:** Neptune components update frequently. Props you remember from a previous session may have changed, been deprecated, or never existed. Guessing props leads to broken UI or runtime errors that the designer has to debug.

**How to apply:** Every time you write JSX using a Neptune component, verify first. If the MCP is unavailable, tell the user rather than guessing. This applies even to components you've used before in the same session — always check.
