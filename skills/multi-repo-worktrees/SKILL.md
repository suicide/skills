---
name: multi-repo-worktrees
description: Create, reuse, inspect, and clean up task-isolated Git worktrees for features, issues, or fixes spanning one or several independent repositories. Use when separate branches or parallel agent sessions need isolated checkouts; also works in a single-repository project.
compatibility: Git 2.20+ and a shell; no workspace manager required.
---

# Task-scoped Git worktrees

Keep each Git repository independent. A task directory can collect worktrees from one or more repositories so a single agent session can inspect and test a cross-repo change without interfering with another task.

**Do not create worktrees automatically for every change.** First decide whether branch isolation, multiple affected repositories, concurrent work, or a durable per-issue session makes them useful.

## Locate repositories and decide scope

1. Detect whether the project has a parent `repos.json` (see `meta-workspace`), or is a standalone Git repository. If there is a manifest, resolve paths relative to its directory; don't assume all repositories are affected.
2. Identify the specific task/issue, participating repositories, current worktrees, branches, and base refs. Examine `git status --short` and `git worktree list --porcelain` for each repository.
3. Do not reuse a checkout with uncommitted unrelated edits. Reuse an existing task worktree when its branch/destination match the task; don't create duplicate branches.
4. Agree on a short task identifier and distinct destination, e.g. `tasks/123-auth/`. Keep task directories outside source repositories and ignored by the meta-workspace, or use a dedicated sibling directory in a standalone project.
5. Prefer to start a single issue in a fresh agent session. Keep its context, tests, and review focused on that task.

## Create

Example for a meta-workspace at `$root`, with default branches confirmed as `origin/main` in both repos:

~~~sh
root="$(pwd)"                # run from the meta-workspace root
mkdir -p "$root/tasks/123-auth"

git -C "$root/api" fetch origin
git -C "$root/sdk" fetch origin

git -C "$root/api" worktree add -b task/123-auth \
  "$root/tasks/123-auth/api" origin/main
git -C "$root/sdk" worktree add -b task/123-auth \
  "$root/tasks/123-auth/sdk" origin/main
~~~

- Check the actual base ref for **each** repository (`git branch -a`, `git remote show origin`). Never assume every repository uses `main`.
- Git branch names only need to be unique *within* each repository. If the task branch already exists, inspect where it is checked out and resume rather than force-create it.
- If a task only touches one repository, include only that repository's worktree.
- In a single-repository project, the same technique works with the task directory outside the main checkout.

## Work on the task

- Optionally adapt [task guidance](assets/task-AGENTS.md) into `tasks/<task>/AGENTS.md` with repository roles and commands. Never blindly overwrite an existing file.
- Start OpenCode from the task directory for cross-repo investigation; explicitly read each nested repo's `AGENTS.md` and check edit permissions. For substantial repo-local implementation, a session rooted inside that worktree may give better repo-specific tooling.
- Keep separate Git statuses, commits, and PRs per repository. Do not assume parent-level Git status, snapshots, undo, or an OpenCode built-in single-project worktree tool spans all repositories.
- Test changed repositories and their integration together. Link related PRs and define compatible release/deployment order.
- For concurrent agents, prefer distinct task worktrees and avoid assigning multiple writing agents to the same files/worktree.

## Inspect and finish

~~~sh
git -C "$root/api" worktree list --porcelain
git -C "$root/tasks/123-auth/api" status --short
git -C "$root/tasks/123-auth/sdk" status --short
~~~

Before cleanup, verify work is committed/pushed or explicitly retained, relevant PRs are accounted for, tasks are complete, and no active agent session still uses a worktree.

~~~sh
# Standard removal refuses unsafe dirty worktrees.
git -C "$root/api" worktree remove "$root/tasks/123-auth/api"
git -C "$root/sdk" worktree remove "$root/tasks/123-auth/sdk"
~~~

Never use `--force`, `git reset --hard`, `git clean -fd`, `git branch -D`, or recursive deletion as an automatic cleanup shortcut. Remove an empty task directory only after its checkouts are detached and its other files are reviewed. Branch deletion is a separate decision.

## Failure modes

- **Destination exists:** inspect it and reuse or choose another path; never overwrite it.
- **Branch already checked out:** inspect `worktree list` and return to that worktree or choose a different branch; do not bypass Git protections.
- **Missing base ref:** fetch/check remotes and select an actual branch; never guess.
- **Dirty worktree or unfinished PR:** leave it in place and report the reason cleanup was skipped.
- **Nested repo not accessible from the session:** use explicit paths or a repository-local session, rather than widening permissions silently.
