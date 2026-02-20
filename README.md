# Claude Code to Figma Experiments

Mobile app mockups generated with Claude Code and pushed to Figma via the [HTML-to-Design](https://www.figma.com/community/plugin/1159123024924461424) MCP integration.

## Design

Apple/Notion-inspired aesthetic with a near-monochrome slate palette and a single teal accent (#0d9488). Designed for clarity, generous whitespace, and weight-based typographic hierarchy.

## Screens

| File | Screen | Description |
|------|--------|-------------|
| `login.html` | Login | OAuth-first (Google/GitHub) with collapsible email option |
| `task-list.html` | Task List | Date greeting, priority dots, filter pills, FAB, bottom nav |
| `task-detail.html` | Task Detail | Status dots, compact metadata grid, subtasks, comments link |
| `new-task.html` | New Task | Progressive disclosure form with quick-action icons |

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
| Accent | `#0d9488` | Interactive elements (teal-600) |
| Accent subtle | `#ccfbf1` | Accent backgrounds (teal-100) |
| Text primary | `#0f172a` | Headings, primary content |
| Text secondary | `#64748b` | Body text, descriptions |
| Text tertiary | `#94a3b8` | Metadata, timestamps |
| Border | `#e2e8f0` | Hairline borders |
| Border strong | `#cbd5e1` | Card outlines |

Type scale (minor third): 11, 13, 15, 18, 22, 26px.

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
5. Use `#0d9488` for accent color, max 3 accent elements per screen
