---
name: test-dont-rush
description: Don't rush multi-component work — test each change individually, prefer correctness over speed
metadata:
  type: feedback
---

When asked to verify or fix a set of components, go one-by-one and actually test each. Don't fan out agents for bulk changes when the task requires careful per-component verification.

**Why:** "I don't want fast, I want right." Bulk agent fan-out produced changes that weren't tested and removed props that shouldn't have been removed. Speed is worthless if you have to revert half of it.

**How to apply:** For component work — open each component, toggle every control, verify the output. Fix one at a time. Don't assume props are "not needed" without checking what they visually do in the live preview.
