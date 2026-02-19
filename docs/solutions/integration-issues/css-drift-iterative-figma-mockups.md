---
title: "CSS Drift Across Phone Frame Mockups During Iterative Figma MCP Development"
date: 2026-02-19
category: integration-issues
tags:
  - figma-mcp
  - css-normalization
  - html-mockups
  - technical-debt
  - code-review
severity: low
component:
  - login.html
  - new-task.html
  - task-detail.html
symptoms:
  - "Inconsistent phone-frame shell CSS across 4 mockup files"
  - "Dead CSS from removed UI sections (attachments)"
  - "Misleading dead property value (width: 40% always overridden)"
  - "No README or project documentation"
root_cause: >
  Iterative development with Figma MCP created CSS drift between login.html
  (the reference screen that worked first) and the other 3 screens. When
  overflow/sizing fixes were applied to task-list, task-detail, and new-task,
  login.html was left behind since it already rendered correctly.
resolution_status: resolved
---

# CSS Drift in Iterative Figma MCP Mockup Development

## Problem

After building 4 HTML phone frame mockups through multiple Figma MCP capture iterations, a code review identified CSS inconsistencies that accumulated during rapid prototyping:

1. **login.html diverged from the other 3 files** — missing `overflow: hidden` on `.phone-screen`, missing explicit `background-color` on `.status-bar`, different `.home-indicator` margin, and no `.bottom-spacer` div
2. **Dead CSS in new-task.html** — `.attachments`, `.attach-btn`, `.attach-icon` rules left behind from a removed attachments section during content trimming
3. **Misleading dead CSS in task-detail.html** — `.progress-fill` declared `width: 40%` in CSS but the HTML always overrode it with `style="width:66%"`, making the CSS value misleading
4. **No README** — no documentation explaining the project's purpose, constraints, or how to add new screens

## Solution

### Root Cause

CSS drift accumulated due to iterative development with the Figma MCP capture tool:

- **login.html was the reference screen** — it worked correctly from the start, so when overflow and sizing fixes were applied to the other 3 files (adding `overflow: hidden`, explicit `background-color` on `.status-bar`, consistent `.home-indicator` margins, `.bottom-spacer` divs), login.html was never updated to match.
- **Content trimming left dead CSS** — the attachments section was removed from new-task.html's HTML to fit content within the phone frame, but its CSS rules were not cleaned up.
- **Property override created dead value** — the progress bar width was changed via inline style during iteration, but the original CSS value was never removed.

### Fixes Applied

**1. Normalized login.html:**
- Added `overflow: hidden` to `.phone-screen` (matches other 3 files)
- Added `background-color: #ffffff` to `.status-bar`
- Changed `.home-indicator` margin from `8px auto 8px` to `6px auto 8px`
- Added `.bottom-spacer { height: 28px; flex-shrink: 0; }` class and div

**2. Removed dead CSS from new-task.html:**
- Deleted `.attachments`, `.attach-btn`, `.attach-icon` rules (~250 bytes)

**3. Removed dead width from task-detail.html:**
- Removed `width: 40%` from `.progress-fill` (inline style `width:66%` is the actual value)

**4. Added README.md:**
- Project purpose and screen inventory
- Usage instructions for local serving and Figma capture
- Self-contained CSS constraint explanation
- Serializer rules quick reference
- Phone frame template dimensions
- Guide for adding new screens

## Prevention

### For Figma MCP HTML Mockup Projects

1. **Normalize the phone frame shell after each iteration** — when fixing CSS on one file, apply the same fix to all files. The shell (`.phone-frame`, `.phone-screen`, `.dynamic-island`, `.status-bar`, `.status-icons`, `.home-indicator`, `.bottom-spacer`) should be identical across all files.

2. **Clean up CSS when removing HTML** — when trimming content to fit the phone frame, delete the corresponding CSS rules at the same time.

3. **Remove dead CSS defaults when using inline overrides** — if a property is always overridden by an inline style, delete the CSS default to avoid confusion.

4. **Create documentation early** — a README with constraints and a phone frame template reference prevents drift from the start.

### Quick Consistency Check

When maintaining multiple self-contained HTML mockup files, grep for key shell properties:

```bash
# All files should have identical phone-frame shell
grep "overflow: hidden" *.html        # should appear in all files
grep "background-color: #ffffff" *.html  # status-bar should have this
grep "bottom-spacer" *.html           # all files should have this
```

## Related Documentation

- [Figma MCP Overflow Scroll-Height Bug](./figma-mcp-overflow-scroll-height.md) — companion doc covering the serializer's scroll-height measurement issue that prompted the overflow/sizing changes which caused this drift
