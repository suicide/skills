# <project-name> workspace

This is a coordination repository for multiple **independent Git repositories**, not a monorepo. See `repos.json` for checkout paths and origins.

## Repository map

Describe each checkout and its responsibility here. Do not infer roles from names alone.

## Working agreements

- Plan cross-repository changes here; commit, test, and open PRs in each affected repository separately.
- Read the `AGENTS.md` inside every affected repository before editing its files. Follow its local build and test conventions.
- Do not commit nested checkouts, Git submodules, or temporary `tasks/` worktrees to the parent.
- Maintain compatibility when related changes cannot deploy atomically.
- For isolated tasks spanning repositories, use the `multi-repo-worktrees` skill if installed.

## Integration

Add actual integration setup, dependency overrides, and verification commands once known.
