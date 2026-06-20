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
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>PLAN TITLE</title>
<style>
  /* ── Theme tokens ── */
  :root {
    --bg:           #111827;
    --surface:      #1e293b;
    --surface-deep: #0f172a;
    --border:       #334155;
    --border-sub:   #1e293b;
    --text:         #e2e8f0;
    --text-muted:   #94a3b8;
    --text-faint:   #64748b;
    --heading:      #f8fafc;
    --accent:       #818cf8;
    --accent-dim:   #1e1b4b;
    --green:        #4ade80;
    --green-bg:     #052e16;
    --amber:        #fbbf24;
    --amber-bg:     #1c1007;
    --red:          #f87171;
    --red-bg:       #1c0808;
    --blue:         #38bdf8;
    --blue-bg:      #0c1a2e;
    --code-text:    #93c5fd;
    --code-key:     #86efac;
    --code-val:     #fde68a;
    --code-comment: #475569;
  }
  body.light {
    --bg:           #ffffff;
    --surface:      #f8fafc;
    --surface-deep: #f1f5f9;
    --border:       #cbd5e1;
    --border-sub:   #e2e8f0;
    --text:         #1e293b;
    --text-muted:   #475569;
    --text-faint:   #94a3b8;
    --heading:      #0f172a;
    --accent:       #6366f1;
    --accent-dim:   #eef2ff;
    --green:        #16a34a;
    --green-bg:     #f0fdf4;
    --amber:        #d97706;
    --amber-bg:     #fffbeb;
    --red:          #dc2626;
    --red-bg:       #fef2f2;
    --blue:         #0284c7;
    --blue-bg:      #f0f9ff;
    --code-text:    #4f46e5;
    --code-key:     #16a34a;
    --code-val:     #b45309;
    --code-comment: #94a3b8;
  }

  /* ── Base ── */
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; font-size: 15px; background: var(--bg); color: var(--text); line-height: 1.7; transition: background 0.2s, color 0.2s; }
  .page { max-width: 980px; margin: 0 auto; padding: 48px 24px 80px; }

  /* ── Toggle ── */
  .theme-toggle { position: fixed; top: 16px; right: 20px; background: var(--surface); border: 1px solid var(--border); border-radius: 6px; padding: 5px 12px; font-size: 12px; font-weight: 600; color: var(--text-muted); cursor: pointer; transition: all 0.15s; z-index: 100; }
  .theme-toggle:hover { color: var(--text); border-color: var(--accent); }

  /* ── Typography ── */
  h1 { font-size: 24px; font-weight: 700; color: var(--heading); margin-bottom: 6px; }
  .subtitle { color: var(--text-muted); font-size: 14px; margin-bottom: 40px; }
  h2 { font-size: 16px; font-weight: 600; color: var(--heading); margin: 36px 0 14px; padding-bottom: 8px; border-bottom: 1px solid var(--border-sub); }
  h3 { font-size: 15px; font-weight: 600; color: var(--text); margin: 20px 0 8px; }
  p { color: var(--text-muted); margin-bottom: 12px; }

  /* ── Tabs ── */
  .tabs { display: flex; gap: 2px; border-bottom: 1px solid var(--border-sub); margin-bottom: 28px; flex-wrap: wrap; }
  .tab { padding: 9px 18px; cursor: pointer; font-size: 14px; color: var(--text-faint); border-bottom: 2px solid transparent; transition: all 0.15s; white-space: nowrap; }
  .tab:hover { color: var(--text); }
  .tab.active { color: var(--accent); border-bottom-color: var(--accent); }
  .panel { display: none; }
  .panel.active { display: block; }

  /* ── Code ── */
  pre { background: var(--surface-deep); border: 1px solid var(--border-sub); border-radius: 8px; padding: 18px; font-family: ui-monospace, "SF Mono", monospace; font-size: 13px; color: var(--code-text); overflow-x: auto; margin-bottom: 16px; line-height: 1.75; }
  .comment { color: var(--code-comment); }
  .key { color: var(--code-key); }
  .val { color: var(--code-val); }
  code { font-family: ui-monospace, "SF Mono", monospace; font-size: 13px; background: var(--surface); padding: 2px 6px; border-radius: 4px; color: var(--code-text); border: 1px solid var(--border-sub); }

  /* ── Tables ── */
  table { width: 100%; border-collapse: collapse; font-size: 14px; margin-bottom: 20px; }
  th { text-align: left; padding: 10px 14px; background: var(--surface-deep); color: var(--text-faint); font-weight: 600; border-bottom: 1px solid var(--border); font-size: 12px; text-transform: uppercase; letter-spacing: 0.05em; }
  td { padding: 10px 14px; border-bottom: 1px solid var(--border-sub); vertical-align: top; color: var(--text-muted); }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--surface); }
  tr.rec td { background: var(--green-bg); }
  tr.rec td:first-child::after { content: " ★"; color: var(--green); font-size: 11px; }

  /* ── Steps ── */
  .steps { display: grid; gap: 12px; }
  .step { background: var(--surface); border: 1px solid var(--border-sub); border-radius: 10px; padding: 18px 20px; display: grid; grid-template-columns: 32px 1fr; gap: 16px; align-items: start; }
  .step-num { width: 32px; height: 32px; border-radius: 50%; background: var(--accent-dim); color: var(--accent); font-size: 13px; font-weight: 700; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .step-body h3 { margin-top: 0; margin-bottom: 6px; }

  /* ── Checklist ── */
  .checklist { list-style: none; display: grid; gap: 8px; }
  .checklist li { display: flex; gap: 12px; padding: 11px 16px; background: var(--surface); border: 1px solid var(--border-sub); border-radius: 8px; font-size: 14px; color: var(--text-muted); align-items: flex-start; }
  .checklist li .box { width: 16px; height: 16px; border-radius: 4px; border: 1.5px solid var(--border); flex-shrink: 0; margin-top: 3px; }

  /* ── Tags ── */
  .tag { display: inline-block; padding: 2px 8px; border-radius: 4px; font-size: 11px; font-weight: 600; margin-left: 6px; vertical-align: middle; }
  .tag.cg      { background: var(--accent-dim); color: #a78bfa; border: 1px solid #4c1d95; }
  .tag.mb      { background: var(--blue-bg);    color: var(--blue); border: 1px solid #0c4a6e; }
  .tag.bd      { background: var(--amber-bg);   color: var(--amber); border: 1px solid #78350f; }
  .tag.infra   { background: var(--amber-bg);   color: #fb923c; border: 1px solid #7c2d12; }
  .tag.both    { background: var(--green-bg);   color: var(--green); border: 1px solid #166534; }
  .tag.new     { background: var(--accent-dim); color: var(--accent); border: 1px solid #312e81; }
  .tag.extend  { background: var(--green-bg);   color: var(--green); border: 1px solid #166534; }
  .tag.done    { background: var(--green-bg);   color: var(--green); border: 1px solid #166534; opacity: 0.7; }
  .tag.blocker { background: var(--red-bg);     color: var(--red); border: 1px solid #7f1d1d; }
  .tag.one-time{ background: var(--amber-bg);   color: #fb923c; border: 1px solid #7c2d12; }

  /* ── Cards ── */
  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
  .card { background: var(--surface); border: 1px solid var(--border-sub); border-radius: 10px; padding: 20px; }
  .card h3 { margin-top: 0; font-size: 14px; }
  .card.highlight { border-color: var(--accent); }
  .card .badge { font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 4px; background: var(--accent-dim); color: var(--accent); border: 1px solid #312e81; margin-left: 8px; vertical-align: middle; }

  /* ── Decisions ── */
  .decisions { display: grid; gap: 16px; }
  .decision { background: var(--surface); border: 1px solid var(--border-sub); border-radius: 10px; padding: 22px; }
  .decision h3 { margin-top: 0; }
  .options { display: grid; gap: 10px; margin-top: 14px; }
  .option { padding: 12px 16px; border-radius: 8px; border: 1px solid var(--border-sub); font-size: 14px; color: var(--text-muted); background: var(--surface-deep); }
  .option.chosen { border-color: var(--green); background: var(--green-bg); color: var(--text); }
  .option .label { font-weight: 600; color: var(--text); font-size: 14px; margin-bottom: 4px; }
  .option.chosen .label::after { content: " — recommended"; color: var(--green); font-weight: 400; font-size: 13px; }
  .tradeoff { font-size: 13px; color: var(--text-faint); margin-top: 4px; }

  /* ── Diagrams ── */
  .diagram { background: var(--surface-deep); border: 1px solid var(--border-sub); border-radius: 10px; padding: 28px; overflow-x: auto; margin-bottom: 24px; }

  /* ── Alerts ── */
  .alert { background: var(--green-bg); border: 1px solid var(--green); border-left: 3px solid var(--green); border-radius: 8px; padding: 14px 18px; font-size: 14px; color: var(--green); margin-bottom: 18px; }
  .alert.warn { background: var(--amber-bg); border-color: var(--amber); border-left-color: var(--amber); color: var(--amber); }
  .alert.info { background: var(--blue-bg); border-color: var(--blue); border-left-color: var(--blue); color: var(--blue); }

  /* ── Questions ── */
  .questions { display: grid; gap: 14px; }
  .question { background: var(--surface); border: 1px solid var(--border-sub); border-left: 3px solid var(--amber); border-radius: 8px; padding: 16px 18px; }
  .question .q { font-size: 14px; color: var(--amber); font-weight: 600; margin-bottom: 6px; }
  .question .context { font-size: 14px; color: var(--text-muted); }
</style>
</head>
<body>
<button class="theme-toggle" onclick="document.body.classList.toggle('light');this.textContent=document.body.classList.contains('light')?'Dark':'Light'">Light</button>
<div class="page">
  <h1>PLAN TITLE</h1>
  <p class="subtitle">One-sentence description of what this plan covers and why.</p>

  <div class="tabs">
    <div class="tab active" onclick="show('overview',this)">Overview</div>
    <!-- add/remove tabs as needed -->
  </div>

  <div id="overview" class="panel active">
    <!-- content -->
  </div>

</div>
<script>
function show(id, el) {
  document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  el.classList.add('active');
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
