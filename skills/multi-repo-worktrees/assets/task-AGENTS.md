# <task-id>: <short title>

This temporary workspace coordinates changes in independent Git worktrees.

## Participating repositories

List each task checkout, its source repository, branch, and responsibility. Only modify the repositories involved in this task.

## Agent workflow

- Read each participating repository's `AGENTS.md` and follow its coding and test conventions.
- Keep separate Git commits and PRs for every repository.
- Run component tests, then integration tests across the participating worktrees.
- Preserve backward compatibility when repositories must merge or deploy separately.
- Do not discard dirty worktrees or delete task branches during cleanup.

## Acceptance criteria / verification

Document task-specific verification commands and linked issues/PRs as appropriate.
