# Connor's Claude Brain

Shareable memory, skills, and setup for designers prototyping with Claude Code at Wise.

```
connors-claude-brain/
│
├── memory/                                 LEARNED BEHAVIOURS
│   ├── read-before-writing.md              Read source and docs before writing anything
│   ├── never-invent.md                     Verify APIs and props exist before using them
│   ├── diagnose-before-fixing.md           Understand first, test each change, never rush
│   ├── commit-after-progress.md            Save work frequently
│   ├── never-delete-without-preserving.md  Preserve before destroying
│   ├── dataset-awareness.md                Use hooks, never bypass filtering
│   └── audit-derived-values.md             Verify computed not hardcoded
│
├── skills/                                 DOMAIN KNOWLEDGE
│   ├── wise-designer.md                    Neptune DS + web/mobile prototypes
│   ├── dynamic-former.md                   Dynamic Flow constraint layer
│   ├── repo-cloner.md                      Clone, install, start, open
│   └── add-dataset.md                      Import real customer CSV data
│
├── plugins/                                CLAUDE CODE PLUGINS
│   ├── figma                               Read, generate, push designs
│   ├── github                              PRs, code search, @transferwise
│   ├── atlassian                           Jira + Confluence access
│   ├── claude-md-management                Manage CLAUDE.md files
│   └── code-simplifier                     Review + clean up code
│
├── mcps/                                   LIVE TOOL SERVERS
│   ├── wise-design-system                  storybook.wise.design/mcp
│   ├── figma                               mcp.figma.com/mcp
│   ├── github                              api.githubcopilot.com/mcp
│   └── atlassian                           mcp.atlassian.com/v1/mcp/authv2
│
├── npm/                                    PROTOTYPE DEPENDENCIES
│   ├── @transferwise/components            Neptune Design System
│   ├── @transferwise/icons                 Icon set
│   ├── @transferwise/neptune-css           CSS reset + utilities
│   ├── @transferwise/neptune-tokens        Design tokens
│   ├── @transferwise/formatting            Money, date, rate formatting
│   ├── @wise/art                           Flags + illustrations (CDN)
│   ├── agentation                          Visual feedback toolbar (dev)
│   ├── recharts                            Charts
│   ├── framer-motion                       Animations (mobile)
│   ├── web-haptics                         Haptic feedback (mobile)
│   └── @react-three/fiber + three          Liquid glass WebGL (mobile)
│
├── PROMPT.md                               Paste into Claude to import
└── README.md                               This file
```

## What this is

A portable "brain" built from months of prototyping with Claude Code on base-surfaces. The `memory/` folder contains hard-won lessons that prevent Claude from making the same mistakes other designers hit. The `skills/` folder contains comprehensive reference skills for building Wise prototypes.

## How to use

1. Clone this repo (or download the folder)
2. Open Claude Code in any project
3. Copy the prompt from `PROMPT.md` and paste it in
4. Claude will review each memory file and intelligently merge it into its own memory — skipping duplicates, replacing weaker versions, keeping what's new

For skills, copy the ones you want into `~/.claude/skills/<name>/SKILL.md`:
```bash
mkdir -p ~/.claude/skills/wise-designer && cp skills/wise-designer.md ~/.claude/skills/wise-designer/SKILL.md
mkdir -p ~/.claude/skills/dynamic-former && cp skills/dynamic-former.md ~/.claude/skills/dynamic-former/SKILL.md
mkdir -p ~/.claude/skills/repo-cloner && cp skills/repo-cloner.md ~/.claude/skills/repo-cloner/SKILL.md
mkdir -p ~/.claude/skills/add-dataset && cp skills/add-dataset.md ~/.claude/skills/add-dataset/SKILL.md
```

---

## Memory

| File | What it teaches Claude |
|------|----------------------|
| `read-before-writing.md` | Read source, docs, and component implementations before writing anything |
| `never-invent.md` | Verify APIs and props exist (via MCP) before using them — never fabricate |
| `diagnose-before-fixing.md` | Understand root cause first, test each change individually, never rush |
| `commit-after-progress.md` | Commit locally after meaningful work to avoid losing progress |
| `never-delete-without-preserving.md` | Always preserve before destructive operations |
| `dataset-awareness.md` | Use hook layer for data, never bypass dataset filtering |
| `audit-derived-values.md` | Verify computed values are actually computed, not hardcoded |

---

## Skills

| Skill | Purpose |
|-------|---------|
| `wise-designer.md` | Unified skill for building Wise web + mobile prototypes with Neptune DS |
| `dynamic-former.md` | Constraint layer for prototyping within Dynamic Flow limitations |
| `repo-cloner.md` | Clone a Wise repo, install deps, start dev server, open browser |
| `add-dataset.md` | Add real customer data to base-surfaces from a Wise CSV export |

---

## Plugins

| Plugin | Source | Purpose |
|--------|--------|---------|
| `figma` | claude-plugins-official | Read designs from Figma, generate designs, push to Figma |
| `github` | claude-plugins-official | Push code, manage repos, search @transferwise org |
| `atlassian` | claude-plugins-official | Access Jira/Confluence for context on tickets and specs |
| `claude-md-management` | claude-plugins-official | Skills for managing CLAUDE.md files |
| `code-simplifier` | claude-plugins-official | Agent for reviewing and simplifying code |

---

## MCPs

| MCP | URL / Source | Purpose |
|-----|-------------|---------|
| Wise Design System | `storybook.wise.design/mcp` | Live Neptune component docs and prop verification |
| GitHub | via `github` plugin | Code search, PR management, repo operations |
| Figma | via `figma` plugin | Design context, screenshots, generation, Code Connect |
| Atlassian | via `atlassian` plugin | Jira issues, Confluence pages, project context |

### Wise Design System MCP install

```bash
claude mcp add --transport http --client-id cdf3737dff9d485485968e50b63fd8b4 wise-design-system https://storybook.wise.design/mcp --scope project
```

---

## NPM packages (in prototypes)

| Package | Purpose |
|---------|---------|
| `agentation` | Dev-only visual feedback toolbar — annotations, design critique, two-session workflow |
| `@transferwise/components` | Neptune Design System component library |
| `@transferwise/icons` | Icon set |
| `@transferwise/neptune-css` | CSS reset + utility classes |
| `@transferwise/neptune-tokens` | Design tokens (CSS custom properties) |
| `@transferwise/formatting` | Money, date, rate formatting |
| `@transferwise/neptune-validation` | Form validation |
| `@wise/art` | Flags, illustrations (loaded from Wise CDN) |
| `@wise/components-theming` | Brand theming layer |
| `recharts` | Charts (rate history, insights) |
| `framer-motion` | Animations (mobile prototype) |
| `web-haptics` | Haptic feedback (mobile prototype) |
| `@react-three/fiber` + `drei` + `three` | Liquid glass WebGL effects (mobile prototype) |

---

## Permissions

No permission prompts. Claude can read, write, run commands, and access all MCPs without interruption:

```json
{
  "permissions": {
    "allow": [
      "Bash(*)",
      "Read",
      "Edit",
      "Write",
      "WebFetch(*)",
      "mcp__wise-design-system__*",
      "mcp__plugin_figma_figma__*",
      "mcp__plugin_github_github__*"
    ]
  }
}
```

---

## Philosophy

The memories in this repo encode one principle: **Claude should be precise, not fast.** Every memory traces back to a real incident where speed-over-correctness cost hours. The setup is designed so Claude:

1. Reads before writing (never guesses)
2. Verifies before using (never assumes APIs exist)
3. Diagnoses before fixing (never tries random solutions)
4. Tests before declaring done (never claims success without checking)
5. Preserves before destroying (never loses work)

If you're getting sloppy output from Claude, it's almost always because one of these five principles is being violated.
