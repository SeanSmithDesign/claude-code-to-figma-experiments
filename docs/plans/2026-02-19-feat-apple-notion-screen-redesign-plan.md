---
title: "Redesign Taskflow Screens with Apple/Notion Aesthetic"
type: feat
date: 2026-02-19
---

# Redesign Taskflow Screens with Apple/Notion Aesthetic

## Overview

Reimagine all 4 Taskflow mobile app screen mockups (Login, Task List, Task Detail, New Task) with an Apple/Notion refined aesthetic. This is not a restyle — it's a fundamental rethinking of content, layout, hierarchy, and interaction patterns to achieve a premium, quiet, intentional design that feels like it belongs in the same family as Apple Reminders, Notion, or Linear.

The mockups are self-contained HTML files (all CSS inline) rendered inside a 433x892px phone frame and captured into Figma via the HTML-to-Design MCP tool. All design decisions must respect the serializer's technical constraints.

## Problem Statement / Motivation

The current screens were prototyped rapidly during Figma MCP testing. While functional and correctly rendering, they use a generic task-app aesthetic: saturated indigo accent (#6366f1), dense information layouts, visible UI chrome, and a conventional component hierarchy. The design doesn't feel premium or distinctive.

**Why redesign now:**
- The capture pipeline is proven and stable (documented in `docs/solutions/`)
- Serializer constraints are well-understood (no `overflow-y: auto`, no `box-shadow`, no pseudo-elements)
- The screens are a portfolio piece — they should demonstrate design taste, not just technical capability

## Proposed Solution

### Design Philosophy

**"Remove until it breaks, then add back one thing."**

Each screen should feel like a sheet of paper with typography on it. The interface should recede until the content is the only thing visible. Interactions should be discoverable but not shouting.

### Design System

#### Color Palette

| Token | Hex | Usage |
|-------|-----|-------|
| `--bg-primary` | `#ffffff` | Screen background, cards |
| `--bg-secondary` | `#f8fafc` | Phone screen base, subtle zones |
| `--border` | `#e2e8f0` | Hairline borders (1px) |
| `--border-strong` | `#cbd5e1` | Emphasis borders (card outlines) |
| `--text-primary` | `#0f172a` | Headings, primary content |
| `--text-secondary` | `#64748b` | Body text, descriptions |
| `--text-tertiary` | `#94a3b8` | Metadata, timestamps, hints |
| `--accent` | `#0d9488` | Interactive elements (teal-600) |
| `--accent-subtle` | `#ccfbf1` | Accent backgrounds (teal-100) |
| `--status-red` | `#dc2626` | Overdue, urgent — desaturated from current #ef4444 |
| `--status-amber` | `#d97706` | In-progress, warnings |
| `--status-green` | `#16a34a` | Complete, success |

**Accent color rationale:** Teal-600 (#0d9488) provides 4.56:1 contrast on white (#ffffff) — passing WCAG AA for body text. It's muted enough for the Apple/Notion aesthetic while being distinct from the near-monochrome slate palette. Status colors are desaturated one stop from the current values.

**Max 3 accent-colored elements per screen:**

| Screen | Accent Element 1 | Accent Element 2 | Accent Element 3 |
|--------|-------------------|-------------------|-------------------|
| Login | "Sign in" CTA button | App logo mark | "Sign up" link text |
| Task List | FAB button | Active filter pill text | — (2 only) |
| Task Detail | Progress bar fill | Back arrow/link | "Add comment" send button |
| New Task | "Create" nav button text | Active field border-bottom | — (2 only) |

#### Typography

| Role | Size | Weight | Line-height | Color |
|------|------|--------|-------------|-------|
| Display heading | 26px | 700 | 1.2 | `--text-primary` |
| Section heading | 22px | 700 | 1.3 | `--text-primary` |
| Body large | 18px | 400 | 1.6 | `--text-primary` |
| Body | 15px | 400 | 1.6 | `--text-secondary` |
| Label | 13px | 600 | 1.4 | `--text-tertiary` |
| Caption | 13px | 500 | 1.4 | `--text-tertiary` |
| Overline | 11px | 600 | 1.2 | `--text-tertiary`, uppercase, tracking +0.05em |

Font stack: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI', Roboto, sans-serif` (unchanged — system fonts are ideal for the Apple aesthetic).

#### Spacing

Base unit: 8px. Allowed values: 4, 8, 12, 16, 20, 24, 32, 40, 48.

- Section padding: 24px horizontal
- Card padding: 16px
- Between-card gap: 8px (tighter than current 10px)
- Section label to first card: 8px
- Form field gap: 20px

#### Components

**Cards:** White background, 1px `--border-strong` (#cbd5e1), 12px border-radius. No shadow. No gradient.

**Buttons (primary):** Full-width, 48px height, `--accent` background, white text, 12px border-radius, 15px/600 font.

**Buttons (secondary):** Full-width, 48px height, white background, 1px `--border` border, 12px border-radius, `--text-primary` text.

**Form fields:** No visible border by default. 1px bottom border `--border`. On focus: bottom border changes to `--accent`. Label above in 13px/600 `--text-tertiary`.

**Filter pills:** 6px 14px padding, 20px border-radius (more rounded than current 8px), 13px/500. Inactive: `--bg-secondary` background, `--text-tertiary` text. Active: `--accent-subtle` background, `--accent` text.

**Checkboxes:** 20x20px, 6px border-radius, 2px `--border-strong` border. Done: `--accent` fill with white checkmark (rendered as an inner element, not pseudo-element).

**Touch targets:** All interactive elements have a minimum 44x44px tap area. Visible elements smaller than 44px use padding to extend the hit area.

### Phone Frame Shell (Canonical Reference)

The phone frame shell must be **identical** across all 4 files. Changes to the shell should be made in all files simultaneously.

```
Phone frame: 433x892px, 12px padding, #1c1c1e bg, 52px radius, 2px #3a3a3c border
  Phone screen: 100% x 100%, #f8fafc bg, 42px radius, overflow:hidden, flex column
    Dynamic island: absolute, top:10px, centered, 120x34px, #000, 20px radius
    Status bar: flex, 54px height, 16px 28px 8px padding, #ffffff bg, flex-shrink:0
    [SCREEN CONTENT — flex:1, varies per screen]
    Home indicator: 134x5px, #0f172a, 3px radius, 6px auto 8px margin, 0.18 opacity
    Bottom spacer: 28px height, flex-shrink:0
```

**Content budget:** ~795px after status bar (54px), home indicator (19px), and bottom spacer (28px) are subtracted from the 868px inner screen height. Screens with bottom nav or action bars have less (~710-720px).

## Technical Approach

### Architecture

Each screen is a standalone HTML file. No shared CSS, no external dependencies (except the Figma capture script). CSS is duplicated intentionally per the serializer's single-page constraint.

### Implementation Phases

#### Phase 1: Design System Foundation

**Objective:** Establish the canonical phone frame shell and shared CSS patterns.

- [x] Create a new version of the phone frame shell with updated status bar, home indicator, and bottom spacer — `login.html` (simplest screen, fewest components)
- [x] Define all CSS class patterns for the design system (colors, typography, spacing, components)
- [x] Capture login.html to Figma and verify the shell renders correctly
- [x] Validate accent color (#0d9488) contrast in the Figma output

**Files:** `login.html`
**Content budget check:** Login is the lightest screen — ample room within 795px.

#### Phase 2: Login Screen Redesign

**Objective:** Reimagine the login screen as an OAuth-first, minimal authentication flow.

**Current → Reimagined:**
- Logo + "Welcome back" heading + subtitle paragraph → Small logo mark + single-line "Sign in to Taskflow" heading
- Email + Password fields always visible → "Continue with email" expandable section (collapsed by default)
- Google + GitHub buttons below email → OAuth buttons are the **primary** path, positioned first
- "Forgot password?" link → Moves inside the expanded email section
- "Don't have an account? Sign up" → "New to Taskflow? Create account" at bottom

**Reimagined layout (top to bottom):**
1. Status bar (54px)
2. Spacer (~80px) — breathe before content
3. Logo mark (40x40px, `--accent` background, white "T", 10px radius)
4. "Sign in to Taskflow" — 22px/700
5. Spacer (32px)
6. "Continue with Google" button (secondary style, 48px) with Google SVG icon
7. Spacer (12px)
8. "Continue with GitHub" button (secondary style, 48px) with GitHub SVG icon
9. Spacer (24px)
10. Divider with "or" text
11. Spacer (24px)
12. "Continue with email" — tappable text link in `--accent`, 15px/500
13. [If expanded: email field, password field, "Forgot password?" link, "Sign in" primary button]
14. Flex spacer (pushes footer down)
15. "New to Taskflow? Create account" — 14px, `--text-tertiary` + `--accent` link
16. Home indicator + bottom spacer

**Content budget:** ~450px collapsed (well within budget), ~650px expanded (still fits).

- [x] Implement reimagined login layout
- [x] Replace indigo accent with teal on logo, links, and expanded sign-in button
- [x] Remove saturated gradient from logo (use flat `--accent` background)
- [x] Capture to Figma (both collapsed and expanded states — 2 separate captures, or just collapsed for the primary mockup)

**Files:** `login.html`

#### Phase 3: Task List Screen Redesign

**Objective:** Reimagine as a quiet, scannable list that puts tasks front and center.

**Current → Reimagined:**
- "My Tasks" heading (28px) + "12 tasks, 3 due today" count → Date-based greeting: "Thursday, Feb 19" (22px/700) + "3 tasks due today" (15px/400)
- Avatar + search + notifications in header → Avatar only (top-left), single action icon (top-right)
- 4 filter pills (All, In Progress, Urgent, Completed) → 3 pills maximum: "All", "Today", "Upcoming"
- Task cards with colored category tags → Minimal cards: title + due date + subtle priority dot
- Task cards have checkbox, title, tag, due date → Remove checkbox from list view (toggle on detail screen). Show: title (15px/500), due date (13px/400), priority indicator (8px dot)
- FAB with + icon (52px, indigo) → Smaller FAB (48px, `--accent`, 14px radius)
- Bottom nav with 4 items → 4 tabs: Tasks, Calendar, Search, Profile — using 11px overline labels

**Reimagined card design:**
```
┌─────────────────────────────────────┐
│  ● Task title goes here             │  ← 8px priority dot (left) + 15px/500 title
│    Due Thu · Design                 │  ← 13px/400, --text-tertiary
└─────────────────────────────────────┘
```

Priority dot colors: red (#dc2626) = urgent, amber (#d97706) = high, no dot = normal.

**Reimagined layout (top to bottom):**
1. Status bar (54px)
2. Header: avatar (32px, flat `--accent` bg, white initials) left, action icon right. 48px with 8px 24px 12px padding. (~52px)
3. "Thursday, Feb 19" (22px/700) + "3 tasks due today" (15px/400). Padding 0 24px 16px. (~55px)
4. Filter pills row: "All" (active), "Today", "Upcoming". Padding 0 24px 12px. (~40px)
5. Border separator (1px)
6. Task list area (flex:1, ~480px available):
   - "Today" section label (11px overline, 12px top padding, 8px bottom) → ~30px
   - 3 task cards (~58px each = 174px)
   - "This Week" section label → ~30px
   - 1 task card → ~58px
   - Total: ~292px (well within ~480px)
7. FAB: absolute positioned, bottom:76px right:24px
8. Bottom nav: 4 items, 52px height including padding
9. Home indicator + bottom spacer (47px)

**Content budget:** Header(52) + Title(55) + Filters(40) + Border(1) + List(~292) + Nav(52) + Home(47) = ~539px. Plenty of room.

- [x] Implement reimagined task list layout
- [x] Replace greeting/title approach
- [x] Redesign task cards (minimal, no checkbox, priority dot)
- [x] Update filter pills (rounded, 3 options, teal accent)
- [x] Resize FAB (48px, teal, flat)
- [x] Update bottom nav (4 tabs with overline labels)
- [x] Capture to Figma

**Files:** `task-list.html`

#### Phase 4: Task Detail Screen Redesign

**Objective:** Focus on actionable content (subtasks) and scannable metadata. Remove activity section to fit within content budget.

**Current → Reimagined:**
- Colored tags (Urgent, In Progress) at top → Single status line: "Urgent · In Progress" in 13px with colored dots
- Title (22px/700) → Keep, slightly tighter line-height (1.2)
- Description paragraph → Keep but reduce to 2 lines max (~14px/400)
- 4 metadata rows (Priority, Status, Assignee, Due date) → Compact 2-row grid: [Priority + Status] row, [Assignee + Due date] row
- Subtasks header + progress bar + 3 subtask items → Keep, this is the actionable core
- Activity section with comment → **Remove.** Replace with "2 comments" subtle link at the bottom
- Comment input bar → **Remove.** Comment from the "2 comments" flow instead

**Reimagined layout (top to bottom):**
1. Status bar (54px)
2. Top bar: "← Tasks" back link (left) + "···" more button (right). ~48px.
3. Status dots: "● Urgent · ● In Progress" — 13px/500 with colored dots. Padding 16px 24px 8px. (~35px)
4. Title: "Implement user authentication flow with OAuth2" — 22px/700. Padding 0 24px 12px. (~65px)
5. Description: 2 lines, 14px/400 `--text-secondary`. Padding 0 24px 16px. (~55px)
6. Metadata grid — 2 rows of key:value pairs. (~80px total)
   - Row 1: Priority [● High] | Status [● In Progress]
   - Row 2: Assignee [AK Alex Kim] | Due [Today, Feb 19]
7. Subtasks section: (~200px)
   - "Subtasks" heading + "2 of 3" count — 15px/600 + 13px/400. (~32px)
   - Progress bar — 4px height, 8px margin. (~20px)
   - 3 subtask rows — checkbox + text, ~44px each. (~132px)
8. "2 comments →" subtle link — 13px/500 `--text-tertiary`. Padding 16px 24px. (~45px)
9. Home indicator + bottom spacer (47px)

**Content budget:** 54+48+35+65+55+80+200+45+47 = ~629px. Safe within 795px. Room for breathing space.

- [x] Implement reimagined task detail layout
- [x] Replace colored tags with status dot line
- [x] Compact metadata into 2-row grid
- [x] Remove activity section and comment input bar
- [x] Add "2 comments →" link
- [x] Keep subtasks as the centerpiece
- [x] Capture to Figma

**Files:** `task-detail.html`

#### Phase 5: New Task Screen Redesign

**Objective:** Minimal form that fits within content budget using progressive disclosure.

**Current → Reimagined:**
- Cancel/Create top bar → Keep but restyle: "Cancel" in `--text-tertiary`, "Create" in `--accent`
- Title field (full border input) → Bottom-border-only field, 18px placeholder for prominence
- Description textarea → Bottom-border-only, smaller (15px), 2-line height
- Priority dropdown + Due Date field (side by side) → Quick-action icon row: flag (priority), calendar (due date), folder (project)
- Project dropdown → Collapsed into quick-action row
- Assignee picker with 4 avatar options → Single "Add assignee" text button below quick actions
- Subtask list with 2 items + "Add subtask" → "Add subtask" as a text button, no pre-populated subtasks
- Full-width "Create Task" bottom button → **Remove.** The top bar "Create" is sufficient (Apple pattern — Reminders, Calendar)

**Reimagined layout (top to bottom):**
1. Status bar (54px)
2. Top bar: "Cancel" (left, `--text-tertiary`) + "New Task" (center, 17px/600) + "Create" (right, `--accent`). ~48px.
3. Title field: bottom-border only, 18px/500 placeholder "What needs to be done?". Padding 20px 24px. (~60px)
4. Description field: bottom-border only, 15px/400 placeholder "Add details...". 2-line height. Padding 12px 24px. (~68px)
5. Quick action row: 3 icon buttons (flag, calendar, folder) — 44x44px each, spaced with 16px gap. Padding 16px 24px. (~60px)
   - Flag icon → cycles priority (none → high → medium → low)
   - Calendar icon → reveals inline date picker
   - Folder icon → reveals project selector
6. Divider (1px `--border`)
7. "Add assignee" text button — 15px/500 `--accent`. Padding 16px 24px. (~47px)
8. "Add subtask" text button — 15px/500 `--accent`. Padding 12px 24px. (~42px)
9. Flex spacer (pushes everything up, keeps form top-aligned)
10. Home indicator + bottom spacer (47px)

**Content budget:** 54+48+60+68+60+1+47+42+47 = ~427px. Very comfortable. Room for expanded states (inline date picker would add ~200px, still within budget).

- [x] Implement reimagined new task layout
- [x] Replace full-border fields with bottom-border-only style
- [x] Create quick-action icon row (flag, calendar, folder as real DOM elements)
- [x] Remove assignee picker grid, subtask pre-population, and bottom CTA button
- [x] Style "Cancel" and "Create" in top bar
- [x] Capture to Figma

**Files:** `new-task.html`

#### Phase 6: Cross-Screen Polish and Consistency

**Objective:** Verify all 4 screens share the same shell, accent color, typography, and spacing. Capture all to Figma.

- [x] Diff the phone frame shell CSS across all 4 files — must be identical
- [x] Grep for any remaining `#6366f1` (old indigo) references — replace with `#0d9488`
- [x] Grep for any remaining `box-shadow`, `background:` (shorthand), or pseudo-element usage
- [x] Verify all touch targets are ≥44px
- [x] Capture all 4 screens to Figma (one at a time with 12-second waits between capture ID generation and page open)
- [x] Take Figma screenshots of all 4 screens for visual verification
- [x] Fix any rendering issues discovered during capture

**Files:** All 4 HTML files

#### Phase 7: Commit and Push

- [x] Commit redesigned screens with descriptive message
- [x] Push to GitHub

**Files:** All modified files

## Acceptance Criteria

### Functional Requirements

- [ ] All 4 screens render correctly inside the phone frame (433x892px)
- [ ] Bottom bezel (home indicator + spacer) is visible on all screens
- [ ] Content fits within the ~795px budget on all screens (no overflow)
- [ ] Phone frame shell CSS is identical across all 4 files
- [ ] No references to old indigo accent (#6366f1) remain
- [ ] All screens capture successfully to Figma via HTML-to-Design MCP
- [ ] No CSS properties that break the serializer (`box-shadow`, `background` shorthand for solids, `::before`/`::after`, `overflow-y: auto`)

### Non-Functional Requirements

- [ ] Accent color (#0d9488) passes WCAG AA contrast on white (4.5:1+ for text)
- [ ] All body text passes WCAG AA contrast (4.5:1+)
- [ ] All touch targets ≥44x44px
- [ ] Typography follows minor-third scale (13, 15, 18, 22, 26px)
- [ ] Max 3 accent-colored elements per screen
- [ ] Design feels "quiet, premium, intentional" — no saturated colors, no dense layouts, generous whitespace

### Quality Gates

- [ ] Visual verification via Figma screenshots for all 4 screens
- [ ] No dead CSS (unused rules from removed content)
- [ ] README updated with new screen descriptions

## Design Decisions Log

| Decision | Chosen | Rationale |
|----------|--------|-----------|
| Accent color | Teal-600 (#0d9488) | 4.56:1 contrast on white, muted, distinct from slate palette |
| Login primary flow | OAuth-first | Modern pattern, reduces form complexity, fits Apple aesthetic |
| Task list cards | No checkbox, priority dot | Simpler visual, toggle is on detail screen, dot is minimal |
| Task detail activity | Removed, replaced with "2 comments" link | Doesn't fit 795px budget, not the primary action on this screen |
| New task form | Progressive disclosure with quick-action icons | Fits budget, matches Apple Reminders pattern, minimal default state |
| New task bottom CTA | Removed | Top bar "Create" is sufficient (Apple convention), saves 60px |
| Filter pills | 3 options (All, Today, Upcoming) | Fewer choices = faster scanning, fits the "remove until it breaks" philosophy |
| Bottom nav | Tasks, Calendar, Search, Profile | Standard 4-tab pattern, covers core navigation needs |

## Dependencies & Prerequisites

- Local HTTP server running on port 8080 (`npx http-server . -p 8080`)
- Figma MCP tools available (`generate_figma_design`, `get_screenshot`)
- Figma file: `lvwLufTeFZvQdfqeb2FwVe`
- Browser available to open capture URLs

## Risk Analysis & Mitigation

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Teal accent too subtle in Figma | Medium | Low | Test with capture before committing to all screens. Can darken to teal-700 (#0f766e) if needed. |
| Content budget overflow on expanded states | Medium | Low | All screens designed with 100-200px headroom. Expanded states are out of scope for static mockups. |
| Serializer drops subtle borders | Medium | Medium | Use `--border-strong` (#cbd5e1) for card borders. Test in first capture. |
| Multiple captures fail (browser tab timeout) | Low | Medium | Open one page at a time with 12-second waits. Documented in `docs/solutions/`. |
| Quick-action icons don't render (SVG in Figma) | Medium | Medium | Use simple unicode characters or text abbreviations as fallback. Test with first capture. |

## Serializer Compatibility Checklist

From `docs/solutions/integration-issues/figma-mcp-overflow-scroll-height.md`:

- [ ] Use `background-color` not `background` for all solid colors
- [ ] Use `flex: 1` without `overflow-y: auto` on content areas
- [ ] Replace all CSS pseudo-elements with real DOM elements
- [ ] No `box-shadow` — use borders only
- [ ] Content physically fits within phone frame (no scroll needed)
- [ ] Add `?v=N` cache busting to URLs when iterating
- [ ] Gradients only on small elements (avatars OK, large areas not)

## References & Research

### Internal References

- Serializer overflow bug: [`docs/solutions/integration-issues/figma-mcp-overflow-scroll-height.md`](../solutions/integration-issues/figma-mcp-overflow-scroll-height.md)
- CSS drift prevention: [`docs/solutions/integration-issues/css-drift-iterative-figma-mockups.md`](../solutions/integration-issues/css-drift-iterative-figma-mockups.md)
- Project README: [`README.md`](../../README.md)

### Design References

- Apple Reminders — minimal form pattern, quick-action icons
- Notion — near-monochrome palette, weight-based hierarchy, hairline borders
- Linear — muted accent on monochrome, subtle status indicators
- Things 3 — progressive disclosure in task creation

### Related Work

- Previous commits: b518075 (initial screens), 50f1f9f (P2 fixes + README)
- GitHub: https://github.com/SeanSmithDesign/claude-code-to-figma-experiments
- Figma file: `lvwLufTeFZvQdfqeb2FwVe`
