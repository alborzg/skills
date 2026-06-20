---
description: Generate a styled HTML planning document for Project Whirlwind features and infrastructure
allowed-tools: Read, Write, Bash(find:*), Bash(ls:*)
---

Generate a self-contained HTML planning document for: **$ARGUMENTS**

## What to produce

A single `.html` file written to a sensible path in the relevant repo (e.g. `infra-local/` for infrastructure, the affected repo root for feature plans). The filename should be `<kebab-topic>_plan.html`.

Before writing, read any relevant source files to ground the plan in real module names, existing patterns, and actual file paths — not hypothetical ones.

## File placement

| Plan type | Where to write |
|---|---|
| Infrastructure / cross-service | `~/Projects/infra-local/<topic>_plan.html` |
| Feature in a single repo | `~/Projects/<repo>/<topic>_plan.html` |
| Multi-repo feature | `~/Projects/infra-local/<topic>_plan.html` |

## HTML structure

Use this exact shell — replace only the title, subtitle, tab list, and panel content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>PLAN TITLE</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; font-size: 14px; background: #0f0f13; color: #e2e8f0; line-height: 1.6; }
  .page { max-width: 980px; margin: 0 auto; padding: 40px 24px; }
  h1 { font-size: 22px; font-weight: 700; color: #f8fafc; margin-bottom: 4px; }
  .subtitle { color: #64748b; font-size: 13px; margin-bottom: 36px; }
  h2 { font-size: 13px; font-weight: 600; color: #94a3b8; text-transform: uppercase; letter-spacing: 0.08em; margin: 28px 0 12px; }
  h3 { font-size: 14px; font-weight: 600; color: #cbd5e1; margin: 18px 0 8px; }
  p { color: #94a3b8; margin-bottom: 10px; font-size: 13px; }

  .tabs { display: flex; gap: 2px; border-bottom: 1px solid #1e293b; margin-bottom: 24px; flex-wrap: wrap; }
  .tab { padding: 8px 16px; cursor: pointer; font-size: 13px; color: #64748b; border-bottom: 2px solid transparent; transition: all 0.15s; white-space: nowrap; }
  .tab:hover { color: #cbd5e1; }
  .tab.active { color: #818cf8; border-bottom-color: #818cf8; }
  .panel { display: none; }
  .panel.active { display: block; }

  pre { background: #0d1117; border: 1px solid #1e293b; border-radius: 6px; padding: 16px; font-family: monospace; font-size: 12px; color: #93c5fd; overflow-x: auto; margin-bottom: 14px; line-height: 1.7; }
  .comment { color: #475569; }
  .key { color: #86efac; }
  .val { color: #fde68a; }
  code { font-family: monospace; font-size: 12px; background: #1e293b; padding: 1px 5px; border-radius: 3px; color: #93c5fd; }

  table { width: 100%; border-collapse: collapse; font-size: 13px; margin-bottom: 16px; }
  th { text-align: left; padding: 8px 12px; background: #0d1117; color: #64748b; font-weight: 600; border-bottom: 1px solid #1e293b; font-size: 12px; }
  td { padding: 9px 12px; border-bottom: 1px solid #1a2332; vertical-align: top; color: #94a3b8; }
  tr:last-child td { border-bottom: none; }
  tr.rec { background: #0f2d1a; }
  tr.rec td:first-child::after { content: " ★"; color: #4ade80; font-size: 11px; }

  .steps { display: grid; gap: 12px; counter-reset: steps; }
  .step { background: #0d1117; border: 1px solid #1e293b; border-radius: 8px; padding: 16px 18px; display: grid; grid-template-columns: 28px 1fr; gap: 14px; align-items: start; }
  .step-num { width: 28px; height: 28px; border-radius: 50%; background: #1e293b; color: #818cf8; font-size: 12px; font-weight: 700; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .step-body h3 { margin-top: 0; margin-bottom: 6px; }

  .checklist { list-style: none; display: grid; gap: 8px; }
  .checklist li { display: flex; gap: 10px; padding: 10px 14px; background: #0d1117; border: 1px solid #1e293b; border-radius: 6px; font-size: 13px; color: #94a3b8; align-items: flex-start; }
  .checklist li .box { width: 16px; height: 16px; border-radius: 3px; border: 1px solid #334155; flex-shrink: 0; margin-top: 2px; }

  /* Service / status tags */
  .tag { display: inline-block; padding: 1px 7px; border-radius: 4px; font-size: 11px; font-weight: 600; margin-left: 6px; vertical-align: middle; }
  .tag.cg      { background: #0f172a; color: #a78bfa; border: 1px solid #4c1d95; }        /* comm-gateway */
  .tag.mb      { background: #0f1f2a; color: #38bdf8; border: 1px solid #0c4a6e; }        /* mindblossom */
  .tag.bd      { background: #1a1a05; color: #fbbf24; border: 1px solid #78350f; }        /* book-data */
  .tag.infra   { background: #1a1205; color: #fb923c; border: 1px solid #7c2d12; }        /* infra-local */
  .tag.both    { background: #0f1a12; color: #4ade80; border: 1px solid #14532d; }        /* multiple services */
  .tag.new     { background: #0f172a; color: #818cf8; border: 1px solid #312e81; }        /* new file/feature */
  .tag.extend  { background: #0f2414; color: #4ade80; border: 1px solid #166534; }        /* extends existing */
  .tag.done    { background: #0f2d1a; color: #86efac; border: 1px solid #166534; }        /* already done */
  .tag.blocker { background: #2d0f0f; color: #ef4444; border: 1px solid #7f1d1d; }        /* blocked */
  .tag.one-time{ background: #1a0f05; color: #fb923c; border: 1px solid #7c2d12; }        /* one-time setup */

  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
  .card { background: #0d1117; border: 1px solid #1e293b; border-radius: 8px; padding: 18px; }
  .card h3 { margin-top: 0; font-size: 13px; }
  .card.highlight { border-color: #818cf8; }
  .card .badge { font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 4px; background: #1e1b4b; color: #a5b4fc; border: 1px solid #312e81; margin-left: 8px; vertical-align: middle; }

  .decisions { display: grid; gap: 16px; }
  .decision { background: #0d1117; border: 1px solid #1e293b; border-radius: 8px; padding: 20px; }
  .decision h3 { margin-top: 0; }
  .options { display: grid; gap: 8px; margin-top: 12px; }
  .option { padding: 10px 14px; border-radius: 6px; border: 1px solid #1e293b; font-size: 13px; color: #94a3b8; }
  .option.chosen { border-color: #4ade80; background: #0f2d1a; color: #86efac; }
  .option .label { font-weight: 600; color: #e2e8f0; font-size: 12px; margin-bottom: 3px; }
  .option.chosen .label::after { content: " — recommended"; color: #4ade80; font-weight: 400; }
  .tradeoff { font-size: 12px; color: #64748b; margin-top: 4px; }

  .diagram { background: #0d1117; border: 1px solid #1e293b; border-radius: 8px; padding: 24px; overflow-x: auto; margin-bottom: 20px; }

  .alert { background: #0f1a0a; border: 1px solid #166534; border-left: 3px solid #4ade80; border-radius: 6px; padding: 12px 16px; font-size: 13px; color: #86efac; margin-bottom: 16px; }
  .alert.warn { background: #1a1205; border-color: #92400e; border-left-color: #f59e0b; color: #fcd34d; }
  .alert.info { background: #0f172a; border-color: #1e3a5f; border-left-color: #38bdf8; color: #7dd3fc; }

  .questions { display: grid; gap: 12px; }
  .question { background: #0d1117; border: 1px solid #2d1a0f; border-left: 3px solid #f59e0b; border-radius: 6px; padding: 14px 16px; }
  .question .q { font-size: 13px; color: #fcd34d; font-weight: 600; margin-bottom: 6px; }
  .question .context { font-size: 13px; color: #78716c; }
</style>
</head>
<body>
<div class="page">
  <h1>PLAN TITLE</h1>
  <p class="subtitle">One-sentence description of what this plan covers and why.</p>

  <div class="tabs">
    <div class="tab active" onclick="show('overview')">Overview</div>
    <!-- add/remove tabs as needed -->
  </div>

  <div id="overview" class="panel active">
    <!-- content -->
  </div>

</div>
<script>
function show(id) {
  document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  event.target.classList.add('active');
}
</script>
</body>
</html>
```

## Tab menu — choose what applies

Pick the tabs that are relevant to the topic. Don't include tabs that would be empty.

| Tab id | When to include |
|---|---|
| `overview` / `arch` | Always — big-picture summary, how it fits the stack |
| `flow` | Multi-step processes, async pipelines, request flows |
| `setup` | Manual one-time steps (cloud console, credentials) |
| `envvars` | Any new environment variables across services |
| `decisions` | Architecture choices with tradeoffs to evaluate |
| `elixir` / `typescript` / `node` | Per-language implementation details and code snippets |
| `localdev` | Local development setup changes (docker-compose, MinIO, etc.) |
| `checklist` | Always last — ordered implementation steps with service tags |
| `questions` | Open questions that need answers before implementation |

## Service tags

Use these in checklist items and tables to show which repo owns each task:

- `.tag.cg` — comm-gateway (purple)
- `.tag.mb` — mindblossom (blue)
- `.tag.bd` — book-data (amber)
- `.tag.infra` — infra-local (orange)
- `.tag.both` — multiple services (green)
- `.tag.new` — new file/feature (indigo)
- `.tag.extend` — extends existing code (green)
- `.tag.done` — already completed (muted green)
- `.tag.blocker` — hard dependency, must be done first (red)
- `.tag.one-time` — Cloudflare console, Dokploy UI, etc. (orange)

## SVG diagrams

When including a flow or architecture diagram, use SVG inside a `<div class="diagram">`. Follow this palette:

- Background boxes: `fill="#1e293b" stroke="#334155"` (neutral)
- Highlighted service: use service accent — indigo `#818cf8` for comm-gateway, sky `#38bdf8` for mindblossom, amber `#f59e0b` for book-data / R2
- Arrow markers: `fill="#334155"` (define `#arr` marker once per SVG)
- Async / enqueue arrows: `stroke-dasharray="4,3"`
- Label text: `fill="#94a3b8"` for annotations, `fill="#e2e8f0"` for box titles
- Font: `font-family="-apple-system, sans-serif" font-size="12"`

Keep diagrams readable at `width="100%"` with a `viewBox` wide enough for the content (typically 700–900px wide, 250–400px tall).

## Design decisions format

Use `.decisions > .decision > .options` with `.option.chosen` on the recommended option. The chosen option's `.label` auto-appends "— recommended" via CSS. Always include a `.tradeoff` for non-chosen options.

## Checklist format

Group by repo or phase. Each `<li>` has a `.box` div followed by the task description. Append `.tag` spans for service ownership and status. Items that are blockers for everything else go in a separate "Blockers" section at the top.

## Alert banners

Use `.alert` (green) for "good to know", `.alert.warn` (amber) for caution, `.alert.info` (blue) for important context. Place at the top of a panel when there's something the reader needs to see before diving in.

## Style rules

- No emojis except in user-facing reply string examples
- Recommended options marked with ★ in tables (via `tr.rec`) or `.option.chosen` in decision cards
- Code snippets in `<pre>` with `.comment`, `.key`, `.val` spans for syntax colouring
- Inline code in `<code>` tags
- Grid layouts use `.grid2` (two-column) for side-by-side comparisons
- Numbered steps use `.steps > .step` with `.step-num` circle and `.step-body`

## Project Whirlwind service registry

Reference these when describing what each service does or who owns a task:

| Service | Language | Role |
|---|---|---|
| `comm-gateway` | Elixir/Phoenix | Normalises inbound SMS (Twilio) and email (Mailgun); dispatches events to mindblossom |
| `mindblossom` | Elixir/Phoenix | Main product — user feeds, enrichment, AI second brain |
| `book-data` | TypeScript/Fastify | Internal ISBN/barcode/cover-image lookup service |
| `infra-local` | Docker Compose | Local dev environment for all services |
| Cloudflare R2 | — | Shared object storage (bucket: `whirlwind-media`) |
| Dokploy | — | Self-hosted deployment platform; services share `dokploy-network` |

## Output instructions

1. Read relevant source files first so the plan references real module/function names.
2. Write the complete HTML file — never truncate it.
3. After writing, print a one-line summary of where the file was written and which tabs it contains.
4. Do not commit the file unless asked.

---

*Inspired by [The Unreasonable Effectiveness of HTML](https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html)*
