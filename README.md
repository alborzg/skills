# Engineering Skills

A personal library of reusable agent skills that standardize engineering practices across the full software development lifecycle — from planning and design through implementation, review, and deployment.

Skills are loaded into Claude Code and other agentic coding tools as slash commands and context guides. They encode process discipline that would otherwise be re-explained in every conversation, keeping agentic workflows consistent across all projects.

---

## Setup

```bash
git clone git@github.com:<you>/skills ~/Projects/skills
rm -rf ~/.claude/skills
ln -s ~/Projects/skills ~/.claude/skills
```

After that, every skill is available in any Claude Code session on this machine. Adding a skill is a commit and push — no reinstall needed.

---

## Skills

### Planning & Design

| Skill | Description |
|---|---|
| `visual-plan` | Produces a self-contained HTML plan file with wireframes, SVG diagrams, file maps, decision matrices, checklists, and open questions. The plan is the approval gate — no code before sign-off. |
| `html-plan` | Dark-theme HTML planning document scoped to the Project Whirlwind stack (comm-gateway, mindblossom, book-data, infra-local). Service-tagged checklists, diagrams, and decision cards. |

---

### cmux — Terminal Multiplexer

Skills for operating [cmux](https://github.com/manaflow-ai/cmux) as a tool in agentic workflows.

| Skill | Description |
|---|---|
| `cmux` | Core topology control — windows, workspaces, panes, focus, moves, reorder, flash |
| `cmux-workspace` | Work inside the current workspace; socket targeting without stealing focus |
| `cmux-browser` | Browser automation via cmux webviews — open sites, interact, extract data |
| `cmux-markdown` | Open markdown in a formatted live-reload viewer panel alongside the terminal |
| `cmux-customization` | Configure `cmux.json` — actions, layouts, shortcuts, browser routing, Dock controls |
| `cmux-settings` | Read and write specific settings by JSON path in `~/.config/cmux/cmux.json` |
| `cmux-keyboard-shortcuts` | Rebind, audit, and create shortcut templates (tmux-style, Vim-style, etc.) |
| `cmux-custom-sidebar` | Author and hot-reload custom SwiftUI-style sidebars from `~/.config/cmux/sidebars/` |
| `cmux-diagnostics` | Health check for hooks, notifications, session restore, socket access, and CLI control |

---

## Adding a Skill

1. Create `skills/<skill-name>/SKILL.md` with YAML frontmatter:

```markdown
---
name: skill-name
description: Use when [specific triggering conditions]
---
```

2. The `description` field controls when the skill is loaded — write it to answer "should I load this right now?" not to summarise the content. Start with "Use when..." and name specific symptoms and situations.

3. Add supporting files only for reusable tools or reference docs over ~100 lines.

4. Commit and push — the skill is immediately available via the symlink.

### Where skills live

| Type | Location |
|---|---|
| General / cross-project skills | This repo (`~/Projects/skills`) |
| Project-specific skills | `<project>/.claude/skills/` in the project repo |
