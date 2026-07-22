---
name: audit-derived-values
description: When auditing data, verify values are actually computed from source — hardcoded strings that match today are bugs waiting to happen
metadata:
  type: feedback
---

When auditing financial data or any derived values, don't just check "is this value present?" — check "if the source number changes, does this value update automatically?"

**Why:** A "formattedBalance" was a hardcoded string that happened to match the computed value at the time. It was declared "all good" when the architecture actually allowed silent drift.

**How to apply:** For any displayed value, trace back to the source. If there's a manual step between source data and display (hardcoded string, separate constant, manual sum), flag it as fragile. Computed values (`.reduce()`, helper functions, derived state) are safe; hardcoded duplicates are not.
