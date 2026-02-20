# Twitter Post Drafts — Claude Code to Figma Workflow

## Post 1 — The Workflow (Thread Starter)

I used Claude Code to generate a full 4-screen mobile app UI and push it directly into Figma — no design tool, no Figma plugin UI, just conversation.

The workflow: describe what you want → Claude writes self-contained HTML → Figma MCP captures it as native Figma layers.

Login, task list, task detail, new task form. All from a terminal.

---

## Post 2 — The "Why This Matters" Take

The gap between "idea" and "visual design" just collapsed.

I described a task management app to Claude Code. It generated pixel-accurate phone frame mockups as HTML, then pushed them straight into Figma via MCP.

No wireframing. No dragging rectangles. Conversation to Figma in minutes.

---

## Post 3 — Technical Learnings / Dev Audience

If you're using Figma's HTML-to-Design with Claude Code, here's what I learned the hard way:

- Don't use `overflow-y: auto` — the serializer measures scroll height, not client height, and your frames will clip
- Use `background-color`, not `background` shorthand — solid colors get silently dropped
- CSS `::before`/`::after` are completely ignored — use real DOM elements
- `overflow: hidden` doesn't map to Figma's "Clip content"

Documented it all here: [link to repo]

---

## Post 4 — Short Punchy Version

Claude Code → HTML mockups → Figma MCP → native Figma layers.

4 mobile screens. Zero design tool interaction. The whole thing lives in a terminal conversation.

---

## Post 5 — The Iterative Process Angle

The most underrated part of using Claude Code for design mockups isn't the generation — it's the iteration.

"Make the cards tighter." "Add a progress bar." "Remove the attachments section, it doesn't fit."

Each change → recapture → updated Figma frames. The feedback loop is seconds, not minutes.

---

## Post 6 — For the Design Community

Designers: Claude Code + Figma MCP is worth paying attention to.

I generated a 4-screen mobile app flow entirely through conversation. The output isn't screenshots or images — it's real Figma layers with proper auto-layout, text nodes, and fills.

You can select individual elements, edit text, change colors. It's native.

---

## Post 7 — The Gotchas / Honest Take

Spent a few hours getting Claude Code → Figma MCP working smoothly. It's powerful but has sharp edges:

The Figma serializer has its own CSS dialect. Some properties just don't translate. I built a full checklist of what works and what silently breaks.

The payoff: once you learn the constraints, iteration speed is wild.

---

## Post 8 — Quick Demo Thread Idea

Here's what "AI-generated design" actually looks like in 2026:

1. Open terminal
2. Tell Claude Code what screens you need
3. It writes HTML mockups inside phone frames
4. Figma MCP captures them as native layers
5. Open Figma — your screens are there, fully editable

No export. No copy-paste. Conversation → design file.

---

## Post 9 — Addressing Skeptics

"AI can't do design" — sure, but it can get you to 80% in 5 minutes.

I used Claude Code to generate 4 mobile app screens and push them into Figma as native, editable layers. Is it portfolio-ready? No. Is it the fastest way to go from idea to tangible mockup? Absolutely.

The last 20% is still your job. But you're starting from something real, not a blank canvas.

---

## Post 10 — The MCP Angle Specifically

MCP is what makes this work.

Claude Code doesn't just generate HTML and hand it off — it uses the Figma MCP server to capture the mockup, poll for completion, and land it in your Figma file. The whole loop is automated.

This is what tool-use in AI actually looks like: not chat, but integrated workflows.
