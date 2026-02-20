# Claude Code to Figma Experiments

Mobile app mockups generated with Claude Code and pushed to Figma via the [HTML-to-Design](https://www.figma.com/community/plugin/1159123024924461424) MCP integration.

## Design

Bold, vibrant aesthetic with a violet/blue/orange palette. Designed for visual impact with heavy typographic weight (800 display), 4px colored card borders as a signature element, and gradient accents.

## Screens

| File | Screen | Description |
|------|--------|-------------|
| `login.html` | Login | OAuth-first (Google/GitHub), violet branding, weight-800 heading |
| `task-list.html` | Task List | Date greeting, 4px colored left borders, gradient FAB, filled filter pills |
| `task-detail.html` | Task Detail | Violet progress bar (6px), bold metadata grid, violet checkboxes |
| `new-task.html` | New Task | Progressive disclosure form, violet action icons, bold Create CTA |

## Usage

Serve the files locally and capture into Figma:

```bash
# Start a local server
npx http-server . -p 8080

# Each file can be opened with Figma MCP capture parameters:
# http://localhost:8080/login.html#figmacapture=...&figmaselector=.phone-frame&figmadelay=2000
```

The Figma MCP `generate_figma_design` tool handles capture ID generation and polling.

## Constraints

Each HTML file must be **self-contained** (all CSS inline) because the Figma capture serializer processes a single page at a time. CSS duplication across files is intentional.

See [`docs/solutions/integration-issues/figma-mcp-overflow-scroll-height.md`](docs/solutions/integration-issues/figma-mcp-overflow-scroll-height.md) for serializer-specific CSS rules and gotchas.

## Key Serializer Rules

- Use `background-color` not `background` shorthand for solid colors
- Use `flex: 1` without `overflow-y: auto` on content areas
- Replace CSS pseudo-elements (`::before`/`::after`) with real DOM elements
- No `box-shadow` — use borders instead
- Content must physically fit within the phone frame (~795px content budget)
- Add `?v=N` cache busting when iterating

## Design System

| Token | Hex | Usage |
|-------|-----|-------|
| Primary | `#7c3aed` | Interactive elements, branding (violet) |
| Secondary | `#2563eb` | Supporting accents (electric blue) |
| Accent | `#f97316` | Urgent/attention states (orange) |
| Success | `#10b981` | Completed states (emerald) |
| Danger | `#ef4444` | Errors, overdue (red) |
| Primary subtle | `#ede9fe` | Accent backgrounds (violet-100) |
| Text primary | `#0f172a` | Headings, primary content |
| Text secondary | `#475569` | Body text, descriptions |
| Text tertiary | `#94a3b8` | Metadata, timestamps |
| Border | `#e2e8f0` | Hairline borders |
| Border strong | `#cbd5e1` | Card outlines |

Type scale (perfect fourth): 11, 13, 15, 18, 21, 28px. Display weight: 800.

## Phone Frame Template

```
Phone frame: 433x892px, 12px padding
  Phone screen: ~409x868px
    Status bar: ~54px
    Content area: ~760-795px
    Home indicator + spacer: ~33px
```

## Adding a New Screen

1. Copy any existing file as a template
2. Keep the phone frame shell (everything outside the screen content area)
3. Replace the content area with your screen's UI
4. Verify total content height stays within the ~795px budget
5. Use `#7c3aed` (violet) as primary accent, `#2563eb` (blue) and `#f97316` (orange) as supporting colors
