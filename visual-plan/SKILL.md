---
name: visual-plan
description: >-
  Use when a plan needs to be a reviewable artifact before implementation —
  UI surfaces with states, architecture decisions, multi-file or risky work,
  before/after product changes, or anything where diagrams and open questions
  need to be visible before code is written.
---

# Visual Plan

Structured visual planning mode. The deliverable is a self-contained HTML file
written to disk — tabs for overview, wireframes (UI plans only), architecture,
file map, decisions, checklist, and open questions. No external dependencies.

## When To Use

Create a visual plan when the work benefits from a reviewable artifact:
- A UI surface with states, a workflow, a before/after product change
- Architecture, data-model, or API decisions that need alignment
- Multi-file, ambiguous, long-running, or risky work
- Any plan where options, tradeoffs, or open questions need to be visible before code

Skip for truly trivial, unambiguous work — typos, one-line fixes, a single
well-specified function, anything whose diff fits in one sentence. Just make the change.

## Plan Discipline

- **Gate thoughtfully.** Skip trivial work. Never pad a plan with filler. Never ship a single-step plan.
- **Research before you draft.** Read real files, actions, schema, and patterns first. Name actual files, symbols, and data shapes — not invented ones. Delegate wide exploration to a sub-agent. Lead with reuse: for each step, name what it reuses before what it adds.
- **Decide hard-to-reverse bets first.** For backend/data/API work, call out wire format, public ids, data-model shape, auth boundaries. Scope to the smallest first cut that proves the approach without foreclosing it.
- **Keep examples at the right altitude.** Don't collapse a broad framework change into the first concrete example. Separate the core abstraction from motivating examples; label examples as examples.
- **Publish standalone plans.** A reader who never saw the chat should understand the plan. No revision language ("unlike the previous version", "this revision changes...").
- **Planning is read-only.** No source edits while building or reviewing the plan. Start editing only after the user approves.
- **The plan is the approval gate.** Present the plan and ask the user to review and approve before writing any code. Do not ask a separate "does this look good?" question.
- **The file is the source of truth.** When scope shifts, update the HTML file — don't only change course in chat.

## Core Workflow

1. **Explore.** Read the real files. Delegate wide exploration to a sub-agent when the scope is broad. Gather enough to name actual symbols, files, and data shapes.
2. **Choose tabs.** Select only tabs with real content (see Tab Menu).
3. **Choose visual surface.** Determine whether wireframes apply (see Visual Surface Choice).
4. **Write the HTML file** to a sensible path:
   - Cross-service / infrastructure work: `~/Projects/infra-local/<topic>_plan.html`
   - Single-repo feature: `<repo-root>/<topic>_plan.html`
5. **Open it.** Run `open <path>` so the user can review immediately.
6. **Self-review** concurrently for high-stakes plans (see below) while the user reads.
7. **Update on feedback.** Re-read the file, apply changes, re-open. The file is the source of truth.
8. **Do not begin implementation** until the user explicitly approves the plan.

## Tab Menu

Pick only tabs that have real content. Never include an empty tab.

| Tab id | When to include |
|---|---|
| `overview` | Always — goal, scope, what changes and why |
| `wireframes` | UI/product plans only — screen states, before/after comparisons |
| `arch` | Architecture decisions, data flow, system boundaries |
| `files` | File map: what changes, what's new, what's reused |
| `decisions` | Options with tradeoffs when a call needs alignment |
| `checklist` | Always last — ordered implementation steps |
| `questions` | Open questions that must be resolved before implementation |

## Visual Surface Choice

**No wireframes** for architecture-only, backend-only, data migration, or copy-only plans. Use inline SVG diagrams in the `arch` tab for data flow and system relationships. Prefer grouped regions, quadrants, or before/after panels over a single-axis chain unless the relationship is truly sequential.

**Wireframes tab** for UI/product plans: show the real app chrome — existing sidebars, toolbars, app density. Don't invent a new shell. Model secondary surfaces (popover, sheet, side panel) as separate artboard-like divs, not embedded inside the primary screen.

**Before/after** when the change modifies an existing surface. Lay both states side by side so the reviewer sees exactly what changes.

**Multiple states** when states matter: default view, loading, error, empty, overflow menu. Each gets its own `wf-frame`.

## Wireframe Quality

Write wireframes as self-contained HTML blocks inside the `wireframes` panel. Rules:
- Match the real app's density, chrome, and layout — no invented shells
- Use `--wf-*` CSS variables (defined in `<style>`) for colours — no raw hex inside wireframes
- Real product content, not Lorem Ipsum or placeholder text
- Pinned bottom bars and nav if the real UI has them
- Do not use `<html>`, `<head>`, `<body>`, or `<style>` tags inside a wireframe block

## Self-Review Before Handoff

For high-stakes plans (architecture, backend, data-model, migration, multi-file, risky):
spawn one skeptical sub-agent reviewer whose only job is to find what is weak, missing, or wrong. Point it at:
- Hard-to-reverse decisions made implicitly or not at all (wire format, public ids, data-model shape, auth, ownership)
- Steps not anchored in real files or symbols
- A menu of options where the plan should commit to one
- Obvious missing decisions ("what happens when X?", "why not Y?")

Apply clear-cut fixes yourself (vague non-goals, unanchored claims, obvious missing decisions). Route genuine judgment calls to the Open Questions tab — do not silently decide them.

Surface the plan first; run the review concurrently. Never make the user wait on it.

## HTML Shell

Use the full shell from `html-plan` (including the theme toggle and CSS variable system). Add these extras inside the `<style>` block for visual-plan wireframes:

```css
/* Wireframe chrome — uses page tokens so it flips with the theme toggle */
.wf-frame { background: var(--surface-deep); border: 1px solid var(--border-sub); border-radius: 10px; overflow: hidden; margin-bottom: 24px; }
.wf-bar { height: 36px; background: var(--surface); border-bottom: 1px solid var(--border-sub); display: flex; align-items: center; padding: 0 14px; gap: 6px; }
.wf-dot { width: 9px; height: 9px; border-radius: 50%; background: var(--border); }
.wf-title { font-size: 11px; color: var(--text-faint); margin-left: 8px; font-weight: 500; }
.wf-body { padding: 20px; }

/* Before / after */
.before-after { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 24px; }
.ba-label { font-size: 11px; font-weight: 600; color: var(--text-faint); text-transform: uppercase; letter-spacing: 0.06em; padding: 0 0 8px; }
```

## SVG Diagram Palette

SVG diagrams live inside `<div class="diagram">` and use fixed colours (SVG cannot read CSS variables). Use these values — they read well against both themes:
- Background boxes: `fill="#1e293b" stroke="#334155"`
- Highlighted node: indigo `#818cf8`, sky `#38bdf8`, amber `#f59e0b`
- Arrow marker: `fill="#475569"` — define `#arr` once per SVG
- Async/enqueue arrows: `stroke-dasharray="4,3"`
- Annotation text: `fill="#94a3b8"`, box titles: `fill="#f1f5f9"`
- Font: `font-family="-apple-system, sans-serif" font-size="12"`
- Always use `width="100%"` with a `viewBox` (typically 700–900 × 250–400)

## Output Instructions

1. Read relevant source files first so the plan references real names and paths.
2. Write the complete HTML file — never truncate.
3. Run `open <path>` immediately after writing.
4. State the file path and which tabs it contains.
5. Do not commit the plan file unless asked.
6. Do not begin implementation until the user explicitly approves.

---

*Adapted from [BuilderIO/skills](https://github.com/BuilderIO/skills/tree/main/skills/visual-plan) — rewritten to produce self-contained local HTML without the Agent-Native dependency.*
