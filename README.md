# Engineering Skills

A personal library of reusable agent skills that standardize engineering practices across the full software development lifecycle — from planning and design through implementation, review, and deployment.

Skills in this repo are loaded into Claude Code (and other agentic coding tools) as slash commands and context guides. They encode process discipline that would otherwise be re-explained in every conversation, keeping agentic workflows consistent and high-quality across all projects.

---

## What Is a Skill?

A skill is a Markdown reference guide (`SKILL.md`) that an AI agent loads on demand. It defines **when** to apply a technique, **how** to execute it, and **what discipline** to maintain throughout. Skills are not one-off prompts — they are reusable, version-controlled process documents designed to make agent behaviour predictable and repeatable.

Skills can include:
- Step-by-step workflows with decision logic
- Inline code patterns, CSS, SVG templates
- Quality bars and anti-patterns to avoid
- Cross-references to related skills

---

## Setup

Skills are loaded globally by symlinking this directory into Claude Code's skills folder:

```bash
# Clone once
git clone git@github.com:<you>/skills ~/Projects/skills

# Symlink so Claude picks them up everywhere
rm -rf ~/.claude/skills
ln -s ~/Projects/skills ~/.claude/skills
```

After that, every skill in this repo is available in any Claude Code session on this machine. Adding a skill is a commit and push — no reinstall needed.

---

## Skills

### `visual-plan`

**Phase:** Planning & Design

Produces a self-contained HTML plan file for any work that needs a reviewable artifact before implementation begins. Covers UI surfaces with wireframes, architecture with SVG diagrams, file maps, decision matrices, implementation checklists, and open questions. Enforces planning discipline: research before drafting, hard-to-reverse decisions called out early, the plan as the approval gate before any code is written.

Use it for: UI changes, architecture decisions, multi-file features, risky refactors, before/after product changes.

---

### `html-plan` *(Project Whirlwind)*

**Phase:** Planning & Design

Generates a styled dark-theme HTML planning document scoped to the Project Whirlwind stack (comm-gateway, mindblossom, book-data, infra-local). Includes service-tagged checklists, SVG diagrams, decision cards, and alert banners. Outputs a single self-contained `.html` file written directly to the relevant repo.

---

## Adding a Skill

1. Create a directory: `skills/<skill-name>/`
2. Write `SKILL.md` with YAML frontmatter:

```markdown
---
name: skill-name
description: Use when [specific triggering conditions]
---
```

3. Keep the description focused on **when to use** the skill, not what it does.
4. Add supporting files (templates, reference docs) only if they're too large to inline or are reusable tools.
5. Commit and push — the skill is immediately available via the symlink.

### Skill structure

```
skills/
  skill-name/
    SKILL.md          # Required — main reference
    some-template.*   # Optional — only for reusable tools or heavy reference (100+ lines)
```

### Naming conventions

- Use `kebab-case`
- Name by what you **do**: `visual-plan`, `html-plan`, not `planning-tools`
- Gerunds work well for processes: `creating-skills`, `reviewing-prs`

---

## Skill Design Principles

**Descriptions trigger loading** — write them to answer "should I load this right now?" not to summarise the skill's content. Start with "Use when..." and name specific symptoms, situations, and contexts.

**Discipline skills need teeth** — if a skill enforces a process (TDD, plan-before-code), include a rationalization table and red flags so agents don't talk themselves out of it under pressure.

**Research before you write** — skills should instruct agents to read real code, name real files, and reuse before adding. Generic placeholder guidance produces generic placeholder output.

**The plan is always the approval gate** — any skill that involves planning should require explicit user sign-off before implementation starts.

---

## SDLC Coverage

| Phase | Skill | Status |
|---|---|---|
| Planning & Design | `visual-plan` | ✓ |
| Planning & Design | `html-plan` | ✓ (Whirlwind) |
| Implementation | — | planned |
| Code Review | — | planned |
| Testing | — | planned |
| Deployment | — | planned |
