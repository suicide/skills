# Skill repository

This repository contains independent, installable Agent Skills, not a multi-repository workspace.

- Put each skill at `skills/<name>/SKILL.md`, with matching frontmatter `name` and a specific `description` describing when it applies.
- Follow the [Agent Skills specification](https://agentskills.io/specification). Keep instructions concise and actionable; prefer skill-local `assets/` or `references/` for optional detail.
- Make skills useful with Git and standard shell tools; don't assume Nix, direnv, GitHub, or a particular application stack unless essential.
- Preserve the distinction between a workspace Git repository and its independent nested Git repositories. Never prescribe destructive Git operations by default.
- Templates are examples to adapt, not files to copy over an existing user's configuration without inspection.
- Prefer normal Git commands before implementing wrappers. If adding scripts later, make operations idempotent where possible and safe with dirty checkouts.
- Don't add a project-specific configuration or credentials to this reusable skills repository.
