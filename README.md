# Claude Code to Figma Experiments

Mobile app mockups generated with Claude Code and pushed to Figma via the [HTML-to-Design](https://www.figma.com/community/plugin/1159123024924461424) MCP integration.

## Design

Gen Z / Bump-inspired aesthetic with electric blue (`#0000FF`), neon lime (`#BFFF00`), and hot pink (`#FF69B4`) accents. Uses the **Honk** variable font from Google Fonts for playful display text, paired with system fonts for body content. Designed for maximum energy with bubble-shaped cards and bold neon status rings.

## Screens

| File | Screen | Description |
|------|--------|-------------|
| `login.html` | Login | OAuth-first (Google/GitHub) with pill-shaped buttons, neon-rimmed logo |
| `task-list.html` | Task List | Blue header zone, Honk greeting, neon card rings by status, FAB, blue bottom nav |
| `task-detail.html` | Task Detail | Blue topbar, pink/yellow status dots, neon lime progress bar, Honk meta labels |
| `new-task.html` | New Task | Blue topbar with Honk title, neon lime create button, electric blue actions |

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
| Electric Blue | `#0000FF` | Headers, nav bar, primary accent, links on white |
| Neon Lime | `#BFFF00` | Done/success, avatar rings, FAB, progress fill, checkboxes |
| Hot Pink | `#FF69B4` | Urgent states, overdue text, attention |
| Yellow | `#FFE500` | In-progress, warnings, callout pills |
| White | `#FFFFFF` | Content backgrounds, text on blue |
| Dark Text | `#1a1a2e` | Primary text on white backgrounds |
| Muted Text | `#6b7280` | Secondary/meta text on white |
| Muted Nav | `#8888FF` | Inactive nav items on blue background |

### Typography

- **Honk** (Google Fonts, variable: MORF 15, SHLN 50): brand logo, screen titles, filter pills, status tags, section labels, nav labels
- **System font** (-apple-system / SF Pro): card titles, body text, descriptions, metadata values

### Card Status Ring Colors

| Status | Border Color |
|--------|-------------|
| Urgent | `#FF69B4` (hot pink) |
| In-progress | `#FFE500` (yellow) |
| Done | `#BFFF00` (neon lime) |
| Normal | `#0000FF` (electric blue) |

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
5. Use the color palette above — status rings determine card border color
