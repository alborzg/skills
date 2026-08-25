---
name: next-issue
description: >-
  Use when picking up a GitHub issue to deliver end to end, when the user says
  "work on issue #N", "pick up the next issue", or pastes an issue URL to
  implement, or when a session-handoff prompt names this skill and an issue
  number.
---

# Next Issue

One issue in, one PR out, a clean checkout, and a paste-ready prompt for the
next session. State lives in the tracker and the repo, never in the
conversation.

**Announce at start:** "I'm using the next-issue skill on issue #N."
**Create a todo per numbered step.** Each step has an artifact; a step without
its artifact is not done.

## The loop

1. **Read, then claim.** Read the issue in full, including discussion, linked
   issues/PRs, and recent commits touching the affected area, before any
   sizing or code:
   ```bash
   gh issue view <N> --comments
   git log --oneline -15 -- <affected paths>
   ```
   Check for an existing claim first: assignee, a claim comment, an open PR
   referencing the issue, or its branch already existing
   (`gh pr list --search "<N> in:body"`). Any of these means coordinate, not
   duplicate. Otherwise claim it:
   ```bash
   gh issue comment <N> --body "Taken: <who/session>, <YYYY-MM-DD>"
   ```
   If requirements are ambiguous, ask the developer now, not after the diff
   exists. Artifact: the claim comment.
2. **Size it, out loud.** One line: "Direct: single-focus change to X" or
   "Orchestrating: multi-task/cross-layer because Y". Judgment call: issues
   that need a plan, several independent tasks, or more than one layer go to
   orchestrating-delivery; small single-focus issues go direct. Artifact: the
   stated decision.
3. **Isolate.** If this session is a dispatched agent, or anything else (the
   human, another agent) may use this checkout, a worktree is REQUIRED
   (REQUIRED SUB-SKILL: superpowers:using-git-worktrees): the primary checkout
   belongs to the human, never switch its branch. Branching in place
   (`issue-<N>-<slug>`) is allowed only in a human-attended session working
   alone. Never on main. The checkout is shared state: verify the branch
   immediately before every commit. Artifact: the worktree path (or, attended
   sessions only, the branch name).
4. **Build.**
   - Orchestrated path: REQUIRED SUB-SKILL: orchestrating-delivery. It owns
     plan, dispatch, review, and PR; rejoin this loop at step 7. Dispatched
     agents' replies may not route back to you (observed when this session
     is itself an agent): the completion signal is repo state (commits and
     findings files in the worktree), never an awaited message. Have
     reviewers write findings to a file; poll the tree, don't stall.
   - Direct path: superpowers:test-driven-development; for bugs, reproduce
     end-to-end before fixing; superpowers:verification-before-completion
     before claiming done.
5. **Two-lens review** (direct path only; orchestrating-delivery already
   includes it). Fresh eyes, never self-review, the implementing context is
   biased toward its own diff. Lens one: spec compliance against the issue
   (/code-review or superpowers:requesting-code-review). Lens two:
   ponytail:ponytail-review for over-engineering. Fix findings, re-run the
   failed lens. Artifact: both lenses passed.
6. **PR.** Push and open a PR that closes the issue. Reference style:
   `owner/repo#N (short title)` plus the URL. The user's voice, no AI
   attribution. The PR is the deliverable; the user merges. Artifact: PR URL.
7. **Record.** REQUIRED SUB-SKILL: handoff, update `docs/WORKLOG.md` with
   issue#, PR#, and verified status. Comment the PR link on the issue.
   Artifact: the worklog commit.
8. **Clean up.** Primary checkout back to main. Remove worktrees you created
   (`git worktree remove <path> && git worktree prune`) and stop everything
   you started: background processes (dev servers, watchers, log tails) and,
   on iOS work, simulators you booted (`xcrun simctl shutdown <udid>`).
   Then sweep orphans from dead sessions in every repo you touched, under one
   safety rule:
   - Removable = clean tree AND merged PR. Squash merges defeat
     `git branch --merged` and ancestry checks, so verify merge state via
     `gh pr list --head <branch> --state merged`, never via git alone.
   - A dirty tree, an unmerged branch, or an open PR may be another session
     mid-flight: leave it and note it in the worklog instead.
   Delete local branches whose PRs have merged. Never delete the unmerged
   feature branch. Artifact: `git worktree list` shows only the primary plus
   known-active worktrees; `git status` clean.
9. **Handoff prompt.** Pick the next issue from the tracker, not from memory:
   ```bash
   gh issue list --state open --json number,title,labels,milestone,assignees,url
   ```
   Skip anything claimed (assignee, claim comment, open PR, existing branch).
   Order: priority labels, then the active milestone, then oldest open. Say
   which rule you applied. Emit exactly this template, filled in:
   ```
   Use the next-issue skill on issue #<N> <issue-url> in the <repo> repo.
   Previous session: issue #<M> delivered via <owner/repo#P (PR title)>
   <pr-url>, awaiting merge.
   Selection: <rule applied, e.g. "highest priority label" or "oldest open
   unclaimed">. Notes: <one line of gotchas from the worklog, or "none">.
   ```
   Artifact: the filled template, last thing in the final message.

## Rationalizations

| Excuse | Reality |
|---|---|
| "I'm the only agent, no claim needed" | Claims live where the issue lives; other sessions and machines cannot see your checkout. |
| "A branch is isolation enough" | A branch in the primary checkout still occupies it; the human comes back to a stolen checkout. Agents get worktrees, always. |
| "The diff is tiny, skip the second lens" | The lenses catch different failure classes; small diffs over-engineer too. |
| "I'll review it myself, it's faster" | Self-review misses what fresh context catches. |
| "Leave the worktree, next session might reuse it" | Stale worktrees and branches are the top cause of confused cold starts. |
| "I remember which issue is next" | Priority lives in the tracker; query it at wrap-up, not from memory. |

## Red flags

- Sizing or coding before reading the linked issues/PRs and recent commits
- No claim comment on the issue
- Review run in the same context that wrote the code
- A handoff prompt missing an issue URL or the previous PR reference

Any of these means: stop, return to the step whose artifact is missing.
