---
name: meta-workspace
description: Create, adopt, or maintain a development meta-workspace that coordinates several independently versioned Git repositories (applications, libraries, infrastructure). Use for initial workspace setup, adding/removing repositories, and maintaining the shared manifest, docs, and agent guidance; not for ordinary single-repository code changes.
compatibility: Git and a shell; optional Nix and direnv.
metadata:
  opencode/autoinvoke: "false"
---

# Meta-workspace

A **meta-workspace** is a small Git repository containing shared development configuration and documentation, with several **independent** Git repositories checked out below it. Do not convert them into a monorepo or Git submodules unless specifically asked.

## Source of truth

- The parent repository versions `AGENTS.md`, `repos.json`, `docs/`, and optionally `flake.nix`, `.envrc`, `justfile`, or integration tooling.
- `repos.json` records repository paths and clone URLs. Its format is a local convention, **not** a Git-standard lockfile: it does not pin commits or replace each repository's own dependency management.
- Nested checkouts and ephemeral `tasks/` directories are ignored by the parent Git repository.
- Every nested repository owns its Git history, branches, commits, PRs, CI, and releases.

Templates to adapt: [workspace instructions](assets/workspace-AGENTS.md), [manifest](assets/repos.json), and [ignore rules](assets/gitignore.example).

## Initialize or adopt

1. Inspect the intended workspace directory, existing `.git`, `AGENTS.md`, `.gitignore`, and Git repositories. If adopting an existing workspace, preserve its state and conventions.
2. Identify repositories by path, URL, and role. Prefer short, stable paths (e.g. `api`, `web`, `sdk`); don't nest one checkout inside another. Record them in `repos.json`, adapting the provided example. Confirm the manifest with the user if repository identity is ambiguous.
3. Initialize Git **only** for the parent if it is not already versioned. Do not replace nested Git directories or add submodules.
4. Clone missing repositories at the manifest paths. For existing paths, inspect `git -C <path> remote -v` and `git -C <path> status --short`; never overwrite, reset, move, or reclone a checkout to make it match the manifest.
5. Create or update parent `.gitignore` with **root-anchored** entries for each checkout (e.g. `/api/`, `/sdk/`), plus `/tasks/` and local build artifacts. Preserve unrelated rules. Verify `git status` at the parent never stages nested repository contents or gitlinks.
6. Create concise parent `AGENTS.md`, documenting repository roles, architecture pointers, integration commands, and the independent Git boundaries. Inspect and respect each nested repository's own `AGENTS.md`; explicitly open it when working from the parent rather than assuming it is automatically loaded.
7. Add `docs/architecture.md` or integration contracts **only** when supported by facts from the actual repositories. Optionally add Nix/direnv and orchestration commands if requested; prefer per-repository tooling otherwise.
8. Check parent and nested `git status` separately and summarize what was created, cloned, left untouched, and still needs configuration.

## Maintenance

- **Add a repository:** update `repos.json` and parent ignore rules, clone into a new path, update the repository map. Don't silently update or switch unrelated repositories.
- **Remove a repository:** remove the manifest entry and corresponding guidance. Do **not** delete its checkout or local worktrees without explicit authorization and dirty-state checks.
- **Synchronize:** inspect each repository's fetch status and branch separately; don't bulk pull/rebase/reset uncommitted work or treat the manifest as a version lock.
- **Cross-repo changes:** plan at workspace level, change/test repositories separately, and use linked commits/PRs. Validate local dependency overrides and compatibility across staggered deployments. Consider the `multi-repo-worktrees` skill for isolated tasks.

## Example

~~~text
myproject/                  # parent Git repository
  AGENTS.md
  repos.json
  .gitignore
  docs/architecture.md
  api/                      # independent Git repository; ignored by parent
  sdk/                      # independent Git repository; ignored by parent
  tasks/                    # ignored, optional task-specific worktrees
~~~

Do not assume an OpenCode session at the parent has atomic Git snapshot/undo or commit semantics across child repositories.
