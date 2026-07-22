---
name: wise-designer
description: Build high-fidelity Wise prototypes using the Neptune Design System with React + TypeScript + Vite. Use when the user asks to create pages, components, flows, or UI for any Wise project — web or mobile. Covers both desktop web (port 3002) and mobile-in-browser (port 3017, fakes iOS with an iPhone shell DeviceFrame). Includes Neptune component APIs, design tokens, layout patterns, icons, flags, formatting, account registry, dataset awareness, and Wise product conventions.
---

# Wise Designer

Build production-fidelity Wise interfaces using the Neptune Design System, React + TypeScript + Vite.

## When to Use

- Building pages, components, or flows for any Wise prototype (web or mobile)
- Implementing designs from Figma using Neptune DS components
- Creating responsive web layouts or mobile-first iOS-style interfaces
- Any UI work that should look and feel like the Wise product

---

## Stack

- **React 18** + **TypeScript** + **Vite**
- **No Tailwind** — uses Neptune tokens + custom CSS
- **No router library** — state-driven navigation with History API URL sync

### Two Modes

| Mode | Port | Layout |
|------|------|--------|
| Web | 3002 | Responsive desktop/tablet/mobile, SideNav + TopBar |
| Mobile | 3017 | Single-column (max 440px), iPhone DeviceFrame shell, IOSTopBar + MobileNav |

Both share the same React codebase, Neptune components, and design tokens. The difference is the navigation shell and layout constraints.

### Key Packages

| Package | Purpose |
|---------|---------|
| `@transferwise/components` | Neptune DS component library |
| `@transferwise/icons` | Icon set |
| `@transferwise/neptune-css` | CSS reset + utility classes |
| `@transferwise/neptune-tokens` | CSS custom properties (design tokens) |
| `@transferwise/formatting` | Money, date, rate formatting |
| `@transferwise/neptune-validation` | Form validation |
| `@wise/art` | Flags, illustrations (CDN) |
| `@wise/components-theming` | Brand theming layer |
| `recharts` | Charts (rate charts, insights) |
| `framer-motion` | Animations (mobile) |
| `web-haptics` | Haptic feedback (mobile) |
| `@react-three/fiber` + `drei` + `three` | Liquid glass WebGL effects (mobile) |
| `agentation` | Dev-only annotation toolbar |

---

## CSS Setup

### Import Order (Critical)

Order matters — import in this sequence in the entry CSS file:

```css
@import '@transferwise/neptune-css/dist/css/neptune.css';     /* 1. Reset + base */
@import '@transferwise/components/build/main.css';             /* 2. Component styles */
@import '@transferwise/neptune-tokens/tokens.css';             /* 3. Design tokens */
@import '@wise/components-theming/wise.css';                   /* 4. Brand theming */
```

### Provider Setup

Root must wrap in `<Provider>` with i18n:

```tsx
import { Provider } from '@transferwise/components';
import en from '@transferwise/components/build/i18n/en.json';

<Provider i18n={{ locale: 'en-UK', messages: en }}>
  <App />
</Provider>
```

### Per-Component CSS

Every custom component has a co-located `.css` file imported at the top of the `.tsx`. Global tokens/resets in `src/styles/global.css` only. When creating a new component: create `ComponentName.css` alongside the `.tsx`.

---

## Design System MCP

**ALWAYS** verify Neptune component props before use:

1. Call `list-all-documentation` from the Wise Design System MCP
2. Call `get-documentation` with the component ID
3. Never guess props — if MCP unavailable, tell the user

---

## Components

> Source: `@transferwise/components`
> Always import from top-level package. Never import from internal paths.

```tsx
import { Button, ListItem, Avatar, SearchInput, SegmentedControl, Modal } from '@transferwise/components';
```

### Buttons

```tsx
<Button priority="primary" size="md" onClick={fn}>Send money</Button>
<Button priority="secondary" size="md">Cancel</Button>
<Button priority="tertiary" size="sm">Learn more</Button>
<Button priority="link" size="md">View details</Button>
```

| Prop | Values |
|------|--------|
| `priority` | `"primary"` \| `"secondary"` \| `"tertiary"` \| `"link"` |
| `size` | `"sm"` \| `"md"` \| `"lg"` |
| `disabled` | `boolean` |
| `loading` | `boolean` |
| `block` | `boolean` (full-width) |
| `icon` | `ReactNode` (leading icon) |

**IconButton** — icon-only:
```tsx
<IconButton aria-label="Send" size={24} priority="secondary"><Send size={24} /></IconButton>
```

**CircularButton** — icon + label in circle:
```tsx
<CircularButton icon={<Send size={24} />} priority="primary">Send</CircularButton>
```

### List Items

The most-used component. Always pair with a control sub-component:

```tsx
<ListItem
  title="Bank transfer"
  subtitle="Free, 1-2 business days"
  media={<Avatar type="icon" size="md"><Bank size={24} /></Avatar>}
>
  <ListItem.Navigation onClick={handleClick} />
</ListItem>

// With right-aligned values
<ListItem
  title="GBP"
  subtitle="British Pound"
  media={<Flag code="GB" size="sm" />}
  valueTitle="1,234.56"
  valueSubtitle="British Pound"
>
  <ListItem.Navigation onClick={handleClick} />
</ListItem>

// Other controls
<ListItem title="Email notifications"><ListItem.Checkbox checked={checked} onChange={setChecked} /></ListItem>
<ListItem title="Bank transfer"><ListItem.Radio checked={selected === 'bank'} onChange={fn} name="method" /></ListItem>
<ListItem title="Dark mode"><ListItem.Switch checked={darkMode} onClick={toggle} /></ListItem>
<ListItem title="Download"><ListItem.Button onClick={fn}>Download</ListItem.Button></ListItem>
```

| Prop | Type | Description |
|------|------|-------------|
| `title` | `ReactNode` | Primary text (required) |
| `subtitle` | `ReactNode` | Secondary text |
| `valueTitle` | `ReactNode` | Right-aligned primary value |
| `valueSubtitle` | `ReactNode` | Right-aligned secondary value |
| `media` | `ReactNode` | Leading visual (Avatar, Flag, Icon) |
| `spotlight` | `"active"` \| `"inactive"` | Highlight animation |
| `disabled` | `boolean` | Disabled state |

> Don't mix different control types in the same list.

### Form / Input

```tsx
<TextInput label="Email" value={val} onChange={fn} placeholder="you@example.com" error="Required" />
<SearchInput placeholder="Search" value={query} onChange={fn} size="md" shape="pill" />
<TextArea value={note} onChange={fn} placeholder="Add a reference" rows={4} />
<Checkbox label="I agree" checked={checked} onChange={setChecked} />
<Radio label="Option A" checked={selected === 'a'} onChange={() => setSelected('a')} />
<Select label="Country" options={[{ value: 'GB', label: 'UK' }]} selected={sel} onChange={fn} />
<MoneyInput amount={1000} onAmountChange={fn} currencies={list} selectedCurrency={cur} onCurrencyChange={fn} />
<DateInput value={date} onChange={fn} label="Date of birth" />
<DateLookup value={date} onChange={fn} label="Transfer date" min={new Date()} />
<Switch checked={enabled} onClick={() => setEnabled(!enabled)} aria-label="Toggle" />
```

### Navigation

```tsx
<Tabs selected={tab} onTabSelect={setTab}><Tab title="Overview" /><Tab title="Activity" /></Tabs>
<SegmentedControl options={[{ value: 'day', label: 'Day' }]} selected="day" onChange={fn} />
<NavigationOption title="Bank transfer" content="Free" icon={<Bank size={24} />} onClick={fn} />
```

### Feedback

```tsx
<Alert type="positive" message="Transfer complete!" />
<Nudge title="Enable 2FA" mediaName="LOCK" action={{ text: 'Enable', onClick: fn }} onDismiss={fn} />
<InlinePrompt sentiment="positive" media={<CheckCircle size={24} />}>Verified.</InlinePrompt>
```

**Snackbar** (via hook):
```tsx
const createSnackbar = useSnackbar();
createSnackbar({ text: 'Copied!', timeout: 3000, timestamp: Date.now() });
```

### Containers

```tsx
<Modal open={isOpen} onClose={fn} title="Confirm"><Modal.Body>Sure?</Modal.Body><Modal.Footer><Button onClick={fn}>Confirm</Button></Modal.Footer></Modal>
<BottomSheet open={isOpen} onClose={fn} title="Select currency">{content}</BottomSheet>
<Drawer open={isOpen} headerTitle="Settings" position="right" onClose={fn}>{content}</Drawer>
<Accordion title="How long?">{answer}</Accordion>
<Popover content="More info" preferredPlacement="top"><Button priority="tertiary">Info</Button></Popover>
```

### Display

```tsx
<Avatar type="initials" size="md">CB</Avatar>
<Avatar type="icon" size="md"><Bank size={24} /></Avatar>
<Badge count={3} />
<ProgressBar percentage={75} />
<Divider level="section" />
<Loader size="sm" />
<Tooltip label="Includes our fee"><span>Rate</span></Tooltip>
<DefinitionList items={[{ title: 'From', value: 'GBP 1,000' }]} />
<FlowNavigation steps={['Amount', 'Recipient', 'Review', 'Pay']} activeStep={1} />
```

---

## Design Tokens

> Source: `@transferwise/neptune-tokens`
> Use CSS custom properties. Never hardcode colors or spacing.

### Colors — Semantic Tokens

**Content:**
| Token | Role |
|-------|------|
| `--color-content-primary` | Primary body text |
| `--color-content-secondary` | Supporting text |
| `--color-content-tertiary` | Muted text |
| `--color-content-link` | Link text |

**Interactive:**
| Token | Role |
|-------|------|
| `--color-interactive-primary` | Primary buttons (forest green in light) |
| `--color-interactive-primary-hover` / `-active` | Hover/pressed |
| `--color-interactive-secondary` / `-hover` / `-active` | Secondary interactive |
| `--color-interactive-accent` / `-hover` / `-active` | Accent (bright green) |
| `--color-interactive-control` / `-hover` / `-active` | Form controls |
| `--color-interactive-neutral` / `-hover` / `-active` | Neutral interactive |

**Background:**
| Token | Role |
|-------|------|
| `--color-background-screen` | Page background |
| `--color-background-elevated` | Cards, modals |
| `--color-background-neutral` / `-hover` / `-active` | Subtle neutral |
| `--color-background-overlay` | Scrim/overlay |

**Border:**
| Token | Role |
|-------|------|
| `--color-border-neutral` | Default border |
| `--color-border-overlay` | Overlay border |

**Sentiment:**
| Token | Role |
|-------|------|
| `--color-sentiment-positive` / `-hover` / `-active` | Success |
| `--color-sentiment-negative` / `-hover` / `-active` | Error |
| `--color-sentiment-warning` / `-hover` / `-active` | Warning |

**Brand:**
| Token | Hex | Usage |
|-------|-----|-------|
| `bright-green` | `#9FE870` | Primary accent |
| `forest-green` | `#163300` | Primary interactive (light) |
| `bright-yellow` | `#FFEB69` | Expressive |
| `bright-orange` | `#FFC091` | Expressive |
| `bright-blue` | `#A0E1E1` | Expressive |

### Spacing

| Token | Value |
|-------|-------|
| `--space-small` | 16px |
| `--space-medium` | 32px |
| `--space-large` | 40px |
| `--space-x-large` | 56px |

### Padding

| Token | Value |
|-------|-------|
| `--padding-x-small` | 8px |
| `--padding-small` | 16px |
| `--padding-medium` | 24px |
| `--padding-large` | 32px |

### Border Radius

| Token | Value |
|-------|-------|
| `--radius-small` | 10px |
| `--radius-medium` | 16px |
| `--radius-large` | 24px |
| `--radius-full` | 9999px |

### Typography

**Font Families:**
| Token | Value |
|-------|-------|
| `--font-family-regular` | `'Inter', Helvetica, Arial, sans-serif` |
| `--font-family-display` | `'Wise Sans', 'Inter', sans-serif` |

**Font Sizes:** `--font-size-12` through `--font-size-32`

**Font Weights:** `--font-weight-regular` (400), `--font-weight-medium` (500), `--font-weight-semi-bold` (600), `--font-weight-bold` (700), `--font-weight-black` (900)

**Typography Presets:**

| Role | Font | Weight | Size | Line Height |
|------|------|--------|------|-------------|
| Title Screen | Inter | 600 | 30px | 34px |
| Title Section | Inter | 600 | 26px | 32px |
| Title Subsection | Inter | 600 | 22px | 28px |
| Title Body | Inter | 600 | 18px | 24px |
| Title Group | Inter | 500 | 14px | 20px |
| Body Large | Inter | 400 | 16px | 24px |
| Body Default | Inter | 400 | 14px | 22px |
| Link Large | Inter | 600 | 16px | 24px |

**Wise Sans** is reserved for display text only: success confirmations, hero numbers, account name. Not body text.

### Theme Variants

Applied via class on `<html>`: `np-theme-personal--dark`, `np-theme-dark`, etc.

| Theme | Product |
|-------|---------|
| Personal / Personal (dark) | Consumer |
| Business / Business (dark) | Business |

### Responsive Breakpoints (Web Only)

| Name | Min Width |
|------|-----------|
| XS | 0px |
| S | 480px |
| M | 768px |
| L | 992px |
| XL | 1200px |
| XXL | 1440px |

---

## Icons

> Source: `@transferwise/icons`
> PascalCase import, no "Icon" suffix. Icons inherit `currentColor`.

```tsx
import { Send, Bank, ChevronRight, Plus, Settings } from '@transferwise/icons';
<Send size={24} />
```

**Sizes:** 16 (inline), 24 (standard), 32 (prominent)

**Common:** `Send`, `Receive`, `Convert`, `Card`, `Bank`, `TrendUp`, `AlertCircle`, `CheckCircle`, `ChevronRight`, `Close`, `Search`, `Plus`, `Settings`, `Profile`, `Edit`, `Copy`, `Download`, `Globe`

---

## Flags & Art

> Source: `@wise/art` — loads from Wise CDN, no local files.

```tsx
import { Flag, Illustration, Illustration3D } from '@wise/art';

<Flag code="GBP" />
<Illustration name="confetti" size="large" />
<Illustration3D name="confetti" size="medium" />
```

**3D names:** `lock`, `globe`, `confetti`, `check-mark`, `flower`, `graph`, `jars`, `magnifying-glass`, `marble`, `marble-card`, `multi-currency`, `plane`, `interest`

---

## Formatting

> Source: `@transferwise/formatting`

```tsx
import { formatMoney, formatDate, formatRate } from '@transferwise/formatting';

formatMoney(1234.56, 'GBP', 'en-GB')  // "£1,234.56"
formatRate(1.2345, 4)                   // "1.2345"
```

---

## Web Layout

Desktop web uses a responsive shell:

```
┌─────────────────────────────────────────┐
│ TopBar (fixed top)                      │
├──────────┬──────────────────────────────┤
│ SideNav  │ Main content                 │
│ (>992px) │ (responsive, scrollable)     │
│          │                              │
└──────────┴──────────────────────────────┘
```

- Desktop (≥992px): SideNav visible, main content beside it
- Tablet (768–991px): SideNav collapsed to icons
- Mobile (<768px): No SideNav, bottom nav or hamburger

---

## Mobile Layout

Mobile wraps the app in an iPhone DeviceFrame via iframe. The content is a single-column layout constrained to max-width 440px.

```
DeviceFrame (iPhone 17 Pro/Air/Pro Max shell, 85% scale on desktop)
└── column-layout-main
    ├── IOSTopBar (fixed top, liquid glass buttons)
    ├── container-content (scrollable page area)
    │   └── PageTransition (push/pop slide animations, 380ms spring)
    │       └── Page content (Neptune components)
    └── MobileNav (fixed bottom tab bar, liquid glass + WebGL)
```

**CSS custom properties on `.column-layout-main`:**
- `--content-pad-top: 120px` (IOSTopBar clearance)
- `--content-pad-bottom: 80px` (MobileNav clearance)
- `--content-pad-x: 16px` (horizontal padding)

**Flow Overlays** — full-viewport overlays that slide up from bottom for Send, Request, Convert, Add Money. Managed with `flowVisible`/`flowAnimating` state.

**Haptic Feedback** — `triggerHaptic()` from `useHaptics.ts` provides vibration via `web-haptics`. Only works from direct user gesture handlers (onClick, touchmove). Not from async callbacks.

---

## Liquid Glass (Mobile Only)

Standalone liquid glass components for the navigation chrome. These replicate iOS 26's Liquid Glass design language.

### When to Use

| Context | Use Liquid Glass? | Component |
|---------|------------------|-----------|
| IOSTopBar (fixed top) | Yes | `LiquidGlassButton`, `LiquidGlassIconButton` |
| FlowHeader (flow overlays) | Yes | `LiquidGlassButton`, `LiquidGlassIconButton` |
| MobileNav (bottom tab bar) | Yes | Built-in WebGL glass |
| Glass container surfaces | Yes | `LiquidGlassSegmentedControl`, `LiquidGlassSwitch` |
| Page content (scrollable) | **No** — use Neptune | `<Button>`, `<SegmentedControl>` |
| BottomSheet actions | **No** — use Neptune | `<Button>` |

### Two Surface Recipes

**Button Recipe** (LiquidGlassButton, LiquidGlassIconButton, TabBar thumb):
- Surface: 65% white, backdrop blur 4px
- Border: asymmetric 0.75px top/left, 0.5px bottom/right
- Shadow: `0 2px 28px rgba(0,0,0,0.065)`
- Spring: snappy (pressScale 0.11, pressSpring 0.25)

**Container Recipe** (LiquidGlassContainer, LiquidGlassTransform):
- Surface: 86% white (light), rgba(42,44,41,0.55) (dark)
- Backdrop blur: 8px, blend layers (color-burn + darken)
- Shadow: `0 4px 16px rgba(0,0,0,0.1)`
- Spring: soft (pressScale 0.04, pressSpring 0.05)

### Components

| Component | Purpose |
|-----------|---------|
| `LiquidGlassButton` | Pill-shaped glass button |
| `LiquidGlassIconButton` | Circle/capsule icon button |
| `LiquidGlassSegmentedControl` | Segmented control with glass thumb |
| `LiquidGlassSwitch` | Toggle with glass thumb |
| `LiquidGlassContainer` | Interactive glass card/panel |
| `LiquidGlassTransform` | Morphing glass surface |
| `LiquidGlassTabBar` | Tab bar with sliding glass thumb |

---

## Architecture Patterns

### State-Driven Navigation

No router library. Navigation state in App.tsx drives rendering:
- `activeNavItem` (e.g. `'Home'`, `'Cards'`) + `subPage` union type
- `parseUrl()` reads URL → sets state
- `stateToPath()` generates URL from state
- History API `pushState`/`popstate` for browser nav
- All URLs use 8-digit numeric IDs (no slugs or query params)

### Account Registry

Single source of truth for all account definitions (`shared-resources/data/account-registry.ts`). Everything is data-driven from this file.

**Lookup helpers:**
- `getAccountBySubPageType(type)` — find account by route type
- `getAccountById(groupId)` — find by GROUP_ID
- `getVisibleAccounts(accountType)` — all visible for personal/business

### Dataset Awareness

Never directly import currency/transaction data. Use hooks:
- `useActiveCurrencies()` — dataset-filtered currencies
- `useDatasetData()` — active dataset info
- `useVisibleAccounts(accountType)` — dataset-aware accounts

Always ask: "Does this work if I switch datasets?"

### Context Providers

1. `DatasetProvider` — active dataset selection (mobile)
2. `LanguageProvider` — current language, `t(key, vars?)` translation helper
3. `PrototypeNamesProvider` — editable consumer/business names
4. `LiveRatesProvider` — simulated live exchange rates
5. `ShimmerProvider` — skeleton loading mode

### Shared Data

All data lives in `shared-resources/data/`, imported via `@shared/data/` Vite alias. Only `src/data/nav.tsx` is platform-specific.

---

## Content & Writing

When writing UI copy, read `shared-resources/content/writing-guidelines.md` first. Wise tone: concise, modern, energetic, British English.

Component-specific rules in `shared-resources/content/components/` (buttons, modals, snackbars, inputs, etc.).

---

## Anti-Patterns

- **Don't use Tailwind** — Neptune tokens + custom CSS only
- **Don't hardcode colors** — use `var(--color-*)` tokens
- **Don't import from internal package paths** — always `@transferwise/components` top-level
- **Don't mix ListItem control types** in the same list
- **Don't use Wise Sans for body text** — display only
- **Don't create components that already exist** — check `design-system/custom-components.md` first
- **Don't guess Neptune props** — verify via Design System MCP
- **Don't hardcode account types or navigation** — use Account Registry
- **Don't import data directly** — use dataset-aware hooks
- **Don't use liquid glass in scrollable content** (mobile) — Neptune components only in page body
- **Don't add responsive breakpoints** (mobile) — single-column only, max 440px
- **Don't skip per-component CSS** — always co-locate `.css` with `.tsx`

---

## Checklists

### New Page/Component
1. Check existing custom-components docs — it may already exist
2. Verify Neptune component props via MCP before use
3. Use semantic tokens for all colors and spacing
4. Create co-located `.css` file
5. Ensure it works with Account Registry (if data-driven)
6. Ensure it respects active dataset
7. Test both personal and business account types

### Figma-to-Code
1. Use Figma MCP `get_design_context` to pull the design
2. Map Figma layers to Neptune components first (ListItem, Avatar, Button, etc.)
3. Map colors to `var(--color-*)` tokens (not hex from Figma)
4. Use `@transferwise/icons` for icons, `@wise/art` for flags
5. Follow the layout pattern for the target mode (web responsive or mobile single-column)
