---
title: "Figma MCP HTML-to-Design: Bottom bezel clipping caused by overflow-y scroll-height measurement"
date: 2026-02-19
category: integration-issues
tags:
  - figma-mcp
  - html-to-design
  - css-overflow
  - scroll-height
  - mobile-frames
  - serializer-quirks
severity: moderate
component: figma-mcp-html-to-design-serializer
symptoms:
  - "Bottom bezel of phone frame cut off or invisible"
  - "Figma frame expands beyond phone-screen parent boundary"
  - "Only screens with scrollable content containers are affected"
  - "background shorthand silently dropped by serializer"
  - "CSS pseudo-elements (::before/::after) not captured in Figma output"
  - "overflow: hidden does not map to Figma 'Clip content' setting"
root_cause: >
  CSS overflow-y: auto on content containers causes the Figma capture serializer
  to measure the element's scroll height (full content height) instead of its
  client height (visible constrained height), expanding the Figma frame beyond
  the phone-screen parent and hiding the bottom bezel.
resolution_status: resolved
affected_files:
  - figma-flow/login.html
  - figma-flow/task-list.html
  - figma-flow/task-detail.html
  - figma-flow/new-task.html
---

# Figma MCP HTML-to-Design: Bottom Bezel Clipping

## Problem

When using the Figma MCP HTML-to-Design tool to capture phone frame mockups (4-screen "Taskflow" mobile app), 3 of 4 screens had broken bottom bezels. The phone frame's rounded corners and dark bezel at the bottom were invisible or cut off. Only the Login screen (which had no scrollable content area) rendered correctly.

### Symptoms

- Bottom bezel of phone frame cut off or invisible on Task List, Task Detail, and New Task screens
- Figma frame height exceeded the phone-screen parent boundary
- Login screen (reference) rendered correctly because it had no `overflow-y: auto`

## Solution

### Root Cause

The Figma MCP HTML-to-Design serializer measures element heights differently than a browser when `overflow-y: auto` is present. In a browser, a flex child with `flex: 1` and `overflow-y: auto` is visually constrained to its parent's bounds, and content scrolls within it. The serializer, however, reads the **scroll height** (the full content height) rather than the **rendered height** (the flex-constrained height). This causes the content container to expand beyond the phone screen boundary, pushing the bottom bezel and rounded corners out of frame.

A secondary contributing factor is that the serializer does not translate `overflow: hidden` into Figma's "Clip content" property, so CSS-based clipping offers no fallback.

### Working Fix

Remove `overflow-y: auto` from content containers while keeping `flex: 1`. The flex constraint alone limits the container to the remaining space inside the phone screen. Without the overflow property, the serializer has no scroll height to measure and respects the flex-imposed boundary.

For screens where content naturally exceeds the phone frame even under flex constraints, **reduce the content** (fewer list items, tighter margins) until it physically fits within the available space (~795px for the content area inside the standard phone frame).

### Code Examples

**Phone frame structure (standard template):**

```css
.phone-frame {
  width: 433px;
  height: 892px;
  background-color: #1c1c1e;
  border-radius: 52px;
  padding: 12px;
  border: 2px solid #3a3a3c;
}

.phone-screen {
  width: 100%;
  height: 100%;
  background-color: #ffffff;
  border-radius: 42px;
  display: flex;
  flex-direction: column;
}
```

**Content area (correct):**

```css
.content-area {
  flex: 1;
  padding: 16px 24px;
  background-color: #ffffff;
  /* Do NOT add overflow-y: auto */
}
```

**Content area (broken -- do not use):**

```css
.content-area {
  flex: 1;
  overflow-y: auto;  /* Serializer measures scroll height, breaks bottom bezel */
  padding: 16px 24px;
  background-color: #ffffff;
}
```

**Bottom spacer pattern:**

```html
<div class="phone-screen">
  <div class="status-bar">...</div>
  <div class="content-area">...</div>
  <div class="home-indicator"></div>
  <div class="bottom-spacer" style="height: 28px; flex-shrink: 0;"></div>
</div>
```

### What Didn't Work

| Attempt | Why It Failed |
|---|---|
| Adding `overflow: hidden` to `.phone-screen` | The serializer does not translate `overflow: hidden` into Figma's "Clip content" setting. Content still overflows the frame boundary. |
| Trimming content alone (without removing `overflow-y`) | Helped partially but the serializer still measured scroll height. The bottom bezel remained inconsistent. |
| Removing both `flex: 1` and `overflow-y: auto` | Made the problem worse. Without `flex: 1`, the content container expanded to its natural (intrinsic) height, which exceeded the phone screen bounds by even more. |

## Prevention & Best Practices

### HTML Template Checklist

Before passing any HTML to the Figma MCP capture tool, verify:

- [ ] Outermost container has explicit `width` and `height` in pixels
- [ ] All containers use `overflow: visible` or no overflow declaration -- never `auto`, `scroll`, or `hidden`
- [ ] Background colors use `background-color`, not `background` shorthand
- [ ] No CSS pseudo-elements (`::before`, `::after`) for visual content -- use real HTML elements
- [ ] All content fits within declared container dimensions without scrolling
- [ ] Phone frame wrapper uses fixed height, not `min-height` or `max-height`
- [ ] A `bottom-spacer` div (~28px) exists as the last child of phone-screen

### CSS Properties to Avoid

| Property | Problem |
|---|---|
| `overflow-y: auto` / `scroll` | Serializer measures scroll height instead of client height |
| `overflow: hidden` | Does not translate to Figma's "Clip content" |
| `background` (shorthand, solid colors) | Solid color values silently dropped |
| `::before` / `::after` | Pseudo-elements completely ignored |
| `box-shadow` | Unreliable rendering |
| `position: sticky` | No Figma equivalent in static capture |

### CSS Properties That Work

| Property | Notes |
|---|---|
| `background-color` | Correctly maps to Figma fills |
| `background: linear-gradient(...)` | Works for gradients |
| `border-radius` | Well supported |
| `display: flex` and flex properties | Fully supported |
| `border` | Solid and dashed both work |
| `font-family`, `font-size`, `font-weight`, `color` | All reliable |
| Inline `<svg>` elements | Preferred for icons |

### Content Sizing Guidelines

```
Phone container (433 x 892, 12px padding)
  Phone screen (~409 x 868)
    Status bar: ~54px
    Content area: ~760-795px (your budget)
    Home indicator + spacer: ~33px
```

Sum all child element heights (including margins and padding) and verify total does not exceed the content area budget. If content is too tall, reduce list items rather than shrinking spacing.

### Capture Workflow Best Practices

- **Cache busting**: Append `?v=N` query params when iterating on HTML files
- **`figmaselector`**: Use `.phone-frame` to capture only the phone element, not the page wrapper
- **`figmadelay`**: Set to 2000ms to allow fonts and layout to settle
- **Parallel captures**: Generate multiple capture IDs upfront, open pages staggered 2s apart
- **One screen per file**: Most reliable approach for multi-screen flows

### Quick Validation After Capture

1. Check frame dimensions in Figma -- should match phone frame size (433x892 or similar)
2. Verify container backgrounds have fill colors (missing = `background` shorthand issue)
3. Compare browser preview with Figma capture for missing pseudo-element content
4. Manually enable "Clip content" on frames if needed (serializer doesn't set this)

## Related Documentation

- Memory file: `.claude/projects/-Users-seansmith-Code/memory/figma-mcp.md` -- Primary knowledge base for Figma MCP learnings
- `docs/solutions/` -- This is the first entry in the solutions directory
- The html.to.design serializer is proprietary (divRIOTS) with no public issue tracker for this behavior
