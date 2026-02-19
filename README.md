# Claude Code to Figma Experiments

Mobile app mockups generated with Claude Code and pushed to Figma via the [HTML-to-Design](https://www.figma.com/community/plugin/1159123024924461424) MCP integration.

## Screens

| File | Screen | Description |
|------|--------|-------------|
| `login.html` | Login | Email/password + Google & GitHub OAuth |
| `task-list.html` | Task List | Dashboard with filters, task cards, bottom nav |
| `task-detail.html` | Task Detail | Task metadata, subtasks, activity feed |
| `new-task.html` | New Task | Creation form with assignees, subtasks |

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
- Content must physically fit within the phone frame (~795px content budget)
- Add `?v=N` cache busting when iterating

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
2. Keep the phone frame shell (everything outside `<!-- SCREEN CONTENT -->`)
3. Replace the content area with your screen's UI
4. Verify total content height stays within the ~795px budget
