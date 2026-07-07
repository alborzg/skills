---
name: orchestrating-delivery
description: >-
  Use when acting as project manager or technical lead responsible for taking
  a feature, plan, or multi-task request all the way to a merged PR,
  especially when running a delivery end to end while the user is away, when
  picking up a repo that has a docs/PLAN.md, or when the user says "run the
  plan", "act as tech lead", "orchestrate this", or "take it to merge".
---

# Orchestrating Delivery

The lead orchestrates; subagents implement. State lives in the repo, not the
conversation. The user, not CI, approves the merge.

**Announce at start:** "I'm using the orchestrating-delivery skill to run
this delivery."

The lead is a frontier model (Fable or Opus class). Implementation subagents
are Sonnet class. If a cross-referenced skill is not installed in the current
harness, apply that stage's requirements manually; the stage is never skipped
because its skill is missing.

## The loop

Each stage has a required artifact. A stage without its artifact is not done.

1. **Plan, committed.** A plan file in the repo (`docs/PLAN.md`) with a task
   list where every task has an acceptance check. If none exists, write one
   (REQUIRED SUB-SKILL: superpowers:writing-plans, or visual-plan for UI
   surfaces) and get user approval before code. Never design "in your head";
   a fresh session must be able to resume from the repo alone.
2. **Issue.** One GitHub issue per delivery stream, created before the
   branch. The branch and PR reference it. Artifact: issue number.
3. **Dispatch.** All implementation happens on a feature branch. Each task
   goes to one Sonnet subagent with a self-contained prompt: the task text,
   its acceptance check, and the relevant plan excerpt. Parallel dispatch
   only for tasks with no dependency between them. The lead writes no
   feature code. (REQUIRED SUB-SKILL: superpowers:subagent-driven-development.
   Feature work inside a task follows superpowers:test-driven-development.)
4. **Per-task two-stage review.** Stage one: spec compliance, the diff does
   what the task says. Stage two: code quality. Findings go back to the same
   subagent. After each accepted task: update `docs/WORKLOG.md` and commit
   (REQUIRED SUB-SKILL: handoff).
5. **PR.** Push the branch, open the PR referencing the issue, description
   in the user's voice with no AI attribution.
6. **Dual PR review.** Two passes over the whole PR, in order:
   spec-compliance review of the diff against the plan (/code-review or
   superpowers:requesting-code-review), then ponytail:ponytail-review to
   hunt over-engineering. Fix findings, push, re-run the failed pass.
7. **Verify like a user.** Run the real thing, not just the test suite
   (REQUIRED SUB-SKILL: superpowers:verification-before-completion). Present
   the evidence to the user.
8. **Merge gate.** The user reviews the verified run and approves. Only then
   merge. Green CI and clean reviews are inputs to the gate, not the gate.

## Rationalization table

| Excuse | Reality |
|---|---|
| "Subagents cost budget and lose my context" | The needed context is the plan excerpt, and it goes in the dispatch prompt. Lead-written code gets no independent review; the orchestration is the review structure. |
| "An issue adds no value for a solo same-day delivery" | The issue survives the session; "same-day" deliveries roll over more often than they finish. |
| "One good code review is enough" | The two passes catch different failure classes: spec drift and over-engineering. A single review defaults to correctness only. |
| "CI is green, merge it" | Green CI is not user approval. |
| "I'll keep the design in a scratch note" | Uncommitted state dies with the session. |

## Red flags

- "I'll just write this part myself, it's small"
- "No issue needed for this"
- "Design in my head"
- Merging without the user having seen a verified run

Any of these means: stop, return to the stage whose artifact is missing.
