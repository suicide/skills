---
name: long-running-work
description: Use for coding tasks that span worker contexts, require costly or stateful verification, or must resume after interruption. Skip for ordinary small changes and routine test loops.
---

# Long-running work

## When to use it

Use this for work that spans worker contexts, needs costly or stateful verification,
or needs a durable restart point. Ordinary changes and routine test loops do not
need a checkpoint, task graph, or additional agent.

## Define bounded work packages

1. Start from the original requirements, approved decisions, and actual repo state.
2. Split only when useful. Give each package a bounded deliverable, acceptance
   criteria, and required evidence. Get approval for material design or scope changes.
3. Record dependencies and check for conflicts in checkouts and shared resources.
   Parallelize only independent work; let unaffected packages continue past blockers.
4. End each package with evidence that meets its criteria, a specific blocker,
   or a resumable checkpoint.

The coordinator owns decomposition and scheduling. Workers own their assigned
package and report when further coordination or splitting is needed.

For workspace setup or checkout isolation, see `meta-workspace` or
`multi-repo-worktrees` as needed. Neither is required.

## Verify, checkpoint, and resume

- Run the cheapest reliable checks first. Record who owns shared environments
  and reuse them only when safe. If retries give the same failure with no new
  evidence, stop retrying and diagnose, change strategy, or report the blocker.
  Follow any budgets in the task brief; there is no universal retry or time limit.
- When you need a checkpoint, keep it in a task-local directory in the
  meta-workspace if available, otherwise within the repository (e.g.
  `./.tmp/<task>/`). Never stage or commit these temporary files; do not
  modify `.gitignore` just to store them.
- Update the checkpoint at milestones or handoff; chat and compaction alone
  are not durable. Adapt the [handoff example](assets/handoff.md) to capture
  actual status, scope, evidence, and the next action. For multi-package work,
  preserve overall package statuses and dependencies too. Link to raw evidence
  stored locally or securely; keep credentials and large logs out of the notes.
- On resume, verify branches, working trees, changes, and environment before
  relying on the handoff. Recheck stale evidence and resolve discrepancies
  without overwriting unexpected edits or blindly replaying commands.
- Preserve dirty work and independent repo histories. Never commit, push,
  reset, or clean without explicit authorization.

## Finish

Check the original acceptance criteria against the combined evidence. Report
unverified items and pending decisions using the agent's existing instructions.
Stop updating the checkpoint once complete; keep cleanup separate from delivery.
