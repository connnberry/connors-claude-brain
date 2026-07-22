---
name: dynamic-former
description: Prototype within Dynamic Flow constraints. Extends the wise-designer skill with DF-specific limitations. Use when the user mentions "dynamic forms", "dynamic flow", "DF", "DF compliant", "DF compatible", "dynamic-former", or wants to build a flow/form that must work within Dynamic Flow. Checks components against DF availability and warns about non-compliant choices.
---

# Dynamic Flow Prototyping

> **This skill extends wise-designer.** Follow all wise-designer patterns (Neptune DS, tokens, icons, formatting, per-component CSS), but operate within these additional constraints.

Dynamic Flow (DF) is a server-driven UI framework for building cross-platform step-based flows and forms (Android, iOS, Web) through backend-controlled components. When prototyping for DF, only certain components, screen types, and interactions are available.

Full DF reference: https://df.wise.com

---

## What DF Is For

DF builds **forms and flows** — multi-step experiences where users input data, make choices, and review information. Each screen is a "step" with a fixed set of available components.

**DF is NOT for:** dashboards, account overviews, freeform pages, or anything requiring custom page composition.

---

## DF Layout Types

These are the visual building blocks available in DF:

| Layout | Purpose |
|--------|---------|
| **Alert** | Attention messages (positive/negative/warning/neutral) with optional CTA |
| **Box** | Container with optional border |
| **Columns** | Two-column vertical layout |
| **Decision** | Rich multi-option selection with media and descriptions (filterable) |
| **Divider** | Visual separation (content-level or section-level) |
| **Form** | Data collection with schema-driven inputs |
| **Heading** | Titles with hierarchical sizing |
| **Image** | HTTP(S) or base64 display |
| **Instructions** | Positive/negative instruction lists |
| **List** | Non-interactive items with avatars and supporting values |
| **Loading Indicator** | Processing feedback |
| **Markdown** | Rich text blocks with links |
| **Media** | Avatar (URI or text) with optional badges |
| **Modal** | Dialogs/bottom sheets — **info-only, no forms inside** |
| **Paragraph** | Plain text blocks |
| **Progress** | Progress tracking with descriptive text |
| **Review** | Read-only label-value pairs for confirmation screens |
| **Search** | Async remote data lookup and selection (server-driven) |
| **Section** | Headers with optional actions |
| **Status List** | Items with done/pending/not-done states |
| **Tabs** | Tabular content organisation (layout-only and form variants) |
| **Upsell** | Dismissible promotional block with image and CTA |

**Layout helpers:** Align (center/left/right), Margin (XS to LG spacing)

---

## DF Form Inputs

When a step has a Form layout, these input types are available:

| Input Type | Notes |
|------------|-------|
| **Text** | Standard text field (also email, password, phone, numeric modes) |
| **TextArea** | Multi-line text |
| **Select** | Dropdown (single and multi-select) |
| **Checkbox** | Zero to multiple selections |
| **Radio** | Single selection (also segmented/tab style) |
| **Switch** | Toggle boolean |
| **SegmentedControl** | Tabular selection |
| **DatePicker** | Calendar-based and text entry |
| **Integer** | Whole numbers |
| **Number** | Decimal support |
| **MoneyInput** | Amount + currency selection as one component |
| **Upload** | Single and multi-file with MIME/size constraints |
| **Repeating Items** | Editable/deletable collections (add/edit/delete) |

**Form features:** autocomplete hints, disabled state, sections with grouping, contextual help (markdown), pre-filled data, placeholder text, required marking, persist async (token-based async submissions).

**Validation:** client-side (minLength, maxLength, pattern) + server-side (field-level and global errors). Errors clear on edit.

---

## DF Behaviors

What happens when users interact with elements:

| Behavior | What it does |
|----------|-------------|
| **Action** | Form submission or URL navigation |
| **Copy** | Place content on clipboard |
| **Dismiss** | Close top-most modal |
| **Download** | Trigger file download |
| **Link** | Open external URL |
| **Modal** | Open dialog/bottom sheet |
| **Refresh** | Reload current step via refreshUrl |
| **Subflow** | Launch nested flow (modal, push, native, or dynamic) |

---

## DF Features Designers Should Leverage

| Feature | What it enables |
|---------|----------------|
| **Subflows** | A flow within a flow — use instead of modals with forms |
| **Polling** | Auto-refresh while waiting for async results (configurable delays/timeouts) |
| **Persist async** | Resume flow after leaving and returning (token-based) |
| **Stack behavior** | Controls whether steps stack, replace, or clear history |
| **Toolbar** | Persistent action bar at bottom of steps |
| **Back navigation** | Automatic, managed by DF — no custom nav needed |
| **Analytics** | Built-in event tracking |
| **Camera** | Device camera access for photo capture |
| **Success splash** | Centered vertical layout with display heading for confirmations |

---

## Screen Types

### Works in DF

- Form screens (text inputs, dropdowns, radios, checkboxes, toggles, uploads)
- Selection/decision screens (pick from options with media + descriptions)
- Review/confirmation screens (key-value summaries)
- Status/progress screens (workflow tracking)
- Search screens (server-driven with results)
- Informational screens (headings, paragraphs, markdown, images, alerts)
- Loading/processing screens (spinners, polling)
- Promotional/upsell screens (dismissible callouts)
- Tabbed screens (content or form variants)
- Modal overlays (info-only)
- Sub-flows (flow within a flow)

### Does NOT Work in DF

- Dashboards (account overview, balance cards, activity feeds)
- Transaction lists / history views
- Settings pages with complex interactions
- Custom navigation (sidebars, bottom tabs, top bars)
- Charts, graphs, or data visualisations
- Card management screens
- Freeform page composition

---

## Component Mapping: Neptune → DF

### Available in DF

| Neptune Component | DF Equivalent | Notes |
|-------------------|---------------|-------|
| `Button` | Action behavior | Must have text label; can add leading icon |
| `Alert` | Alert layout | positive, negative, warning, neutral + optional CTA |
| `Divider` | Divider layout | Content-level and section-level |
| `Modal` | Modal behavior | **Info-only** — no forms inside |
| `Tabs` | Tabs layout | Also chips/segmented variant |
| `SegmentedControl` | Tabs (segmented) or Form radio (segmented style) | |
| `TextInput` | Form > Text/Numeric/Phone/Password | |
| `TextArea` | Form > TextArea | |
| `DateLookup` | Form > DatePicker | Calendar picker |
| `Select` | Form > Select (single/multi) | |
| `Checkbox` | Form > Checkbox | |
| `Switch` | Form > Switch | |
| `Radio` | Form > Radio | Also segmented/tab style |
| `SearchInput` | Search layout | **Server-driven only** — no client-side filtering |
| `Upload` | Form > Upload | MIME type + size constraints |
| `Loader` | Loading Indicator | |
| `ProgressBar` | Progress layout | |
| `ListItem` (read-only) | List layout | Display only — no interactive controls |
| `NavigationOption` | Decision layout | Branching options with icons + descriptions |
| `Summary` / `DefinitionList` | Review layout | Key-value pairs for confirmation |
| `Nudge` / `PromoCard` | Upsell layout | Dismissible promo with CTA |
| `MoneyInput` | Form > MoneyInput | Amount + currency as one component |
| Heading / text | Heading / Paragraph / Markdown | |
| `Image` | Image / Media layout | |
| `InlinePrompt` | Alert layout | Map to Alert with appropriate sentiment |

### NOT Available in DF

| Neptune Component | Why Not | DF Alternative |
|-------------------|---------|----------------|
| `IconButton` | Buttons must have text | Action with text + optional leading icon |
| `CircularButton` | Buttons must have text | Action with text label |
| `Snackbar` (toast) | No ephemeral feedback | Alert (inline, persistent) |
| `BottomSheet` | Not a DF surface type | Modal (info-only) or Subflow (if interactive) |
| `Drawer` | Not a DF surface type | Modal or Subflow |
| `Popover` | Not in DF | Help feature (contextual help text) |
| `Accordion` | Not in DF | Tabs or Sections |
| `Tooltip` | Not in DF | Help feature |
| `Avatar` (standalone) | Not a DF component | Use Media on List/Decision items |
| `Chip` (standalone) | Not in DF | Tabs (chips variant) for filters |
| `Badge` | Not in DF | Use text or omit |
| `Card` | Not in DF | Box layout (optional border) |
| `Table` | Not in DF | Review layout (key-value) or List layout |
| `DateInput` (day/month/year) | Not in DF | DatePicker (calendar) which IS available |
| `ListItem.Navigation` | Interactive lists need Decision | Decision layout (tappable options) |
| `ListItem.Button` | Not in DF | Separate Button (Action behavior) |
| `ListItem.IconButton` | Not in DF | Separate Button with text |
| `FlowNavigation` | DF manages its own step indicators | Built-in (automatic) |

---

## Flagging Non-Compliant Components

When a prototype uses something unavailable in DF, use this format:

> **`[Component]` is not available in Dynamic Flow.**
> [Brief reason]. Use **[DF alternative]** instead.
> Reference: https://df.wise.com

Example:

> **`IconButton` is not available in Dynamic Flow.**
> DF buttons require a text label. Use a **Button (Action)** with text and an optional leading icon instead.
> Reference: https://df.wise.com

---

## Key Principles

1. **DF = forms and flows, not dashboards.** Multi-step experiences with inputs, decisions, and confirmations work. Dashboards, account overviews, and data-heavy pages don't.
2. **Each screen is a "step"** with a fixed set of layout components. No freeform page composition.
3. **Modals are info-only.** Need a form in an overlay? Use a **Subflow** instead.
4. **Search is server-driven.** It calls an API endpoint — no client-side filtering. For small option sets, use Decision with filtering.
5. **No custom navigation.** DF manages forward/back between steps automatically.
6. **Buttons need text.** No icon-only buttons. You can add an icon alongside the text label.
7. **MoneyInput IS available.** Amount + currency as a combined component — don't split into separate fields.
8. **Repeating items** enable dynamic lists of inputs that users can add to, edit, and delete.
9. **Subflows** are powerful — use them for prerequisite tasks, secondary forms, or anything that needs its own step sequence within a parent flow.
10. **Persist async** lets users leave and resume — design with interruption in mind.
