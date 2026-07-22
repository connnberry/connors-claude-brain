---
name: dataset-awareness
description: All data-driven components must use the dataset-aware hook layer — never call registry functions directly for display data
metadata:
  type: feedback
---

ALL data-driven components must use the dataset-aware hook layer. Never call `accountDef.getCurrencies()`, `accountDef.getTransactions()`, or `accountDef.getCards()` directly for display data.

**Why:** Critical bug where switching datasets didn't change what was displayed because components bypassed the hook layer and called registry functions directly (which always return power-user data regardless of active dataset).

**How to apply:**
- Use `useActiveCurrencies()`, `useActiveTransactions()`, `useAllCards()` — never direct registry calls
- Always ask: "Does this work if I switch datasets?"
- Always ask: "Does this work if I add a new account type?"
- Test on all available datasets, not just the default
