---
name: handoff
description: >-
  Use when wrapping up or pausing a work session, or when the user says
  "handoff", "save progress", "where did we leave off", "checkpoint", or
  "update the worklog". Records verified status and next steps to
  docs/WORKLOG.md so a later session (or person) resumes without re-deriving
  context.
---

# Handoff

Capture where we are so the next session picks up cold without re-deriving
context. The worklog is the durable, human-readable thread that survives a
session close, independent of any agent transcript.

**Announce at start:** "I'm using the handoff skill to update the worklog."

## Where it lives

`docs/WORKLOG.md` in the current project, committed. Create it if missing,
using the structure below. If the repo is public, keep the worklog secret-free,
same rule as any committed file.

## Process

1. **Gather verified status, evidence not vibes.** Pull real state, do not
   guess from memory:
   - current branch and recent commits (`git log --oneline -5`, `git status`)
   - open PRs with numbers and titles (`gh pr list --state open`)
   - test/build status you actually ran this session
2. **Read the existing worklog first** and reconcile. Refresh "Next up", do not
   leave stale items or duplicate entries.
3. **Write a new dated entry** at the top of the log section and update
   "Next up" to the concrete next actions.
4. Keep it terse. PR numbers, branch names, and file refs over prose. One
   screen, not an essay.

## Worklog structure

```markdown
# <Project> worklog

## Next up
- [ ] <next concrete action, with PR#/branch/file refs>
- [ ] <open decision awaiting the user>

## YYYY-MM-DD
- <what changed this session, with PR#s and verified status>
- <decisions made, and why>
```

## Rules

- **Verified only.** "Tests pass" means you ran them this session. Cite PR
  numbers and branch names, never "the auth work".
- **Next up is the payload.** The single most useful line is the next action,
  concrete enough to start from a cold session.
- **Don't dump the transcript.** The worklog is a resumable summary, not a log
  of every step.
- **Append, don't rewrite.** Keep prior dated entries; only "Next up" churns.

## Borrowed from

This is the lightweight daily cousin of superpowers `writing-plans` /
`executing-plans` (use those for multi-step feature work). It reuses their
committed-`docs/` location and `- [ ]` checkbox format, executing-plans'
"read and review before resuming" step, and verification-before-completion's
"evidence before assertions".
