---
name: dig-into-components
description: Always read actual component source and CSS before writing overrides — never assume how sizing, rendering, or layout works
metadata:
  type: feedback
---

Always dig into the actual component implementation before writing overrides or integration code.

**Why:** `@transferwise/icons` renders SVGs with hardcoded `width`/`height` pixel values (default 16px). The Avatar CSS sets `font-size` per size class but icons don't use `em` units. Without reading the source, you'd assume CSS handles sizing and pass icons without a `size` prop, resulting in tiny 16px icons inside large avatars.

**How to apply:** Before writing overrides or using a component in a non-trivial way:
1. Read the actual JS/ESM source (not just `.d.ts` types)
2. Read the component's CSS to understand how sizing and layout actually work
3. Never assume — verify how the component renders in practice
