# Agent skills

Reusable [Agent Skills](https://agentskills.io/specification) for software development, primarily with [OpenCode](https://opencode.ai/). Each skill is independently installable; this repository is not itself a project workspace.

## Skills

| Skill | When to use |
| --- | --- |
| [meta-workspace](skills/meta-workspace/SKILL.md) | Create or maintain a parent Git repository coordinating independent application/library repositories. |
| [multi-repo-worktrees](skills/multi-repo-worktrees/SKILL.md) | Isolate features or fixes in Git worktrees across one or several repositories (also works for a single repository). |

## Install

With the GitHub CLI's `gh skill` command (authentication is needed for this private repository):

~~~sh
# Available in all OpenCode projects
gh skill install suicide/skills meta-workspace --agent opencode --scope user
gh skill install suicide/skills multi-repo-worktrees --agent opencode --scope user

# Or install only one skill into the current project
gh skill install suicide/skills multi-repo-worktrees --agent opencode --scope project
~~~

`meta-workspace` is intentionally excluded from OpenCode's automatic skill suggestions: explicitly load or request it when setting up a workspace. `multi-repo-worktrees` can be selected when appropriate. Installation copies the skill and its bundled templates; this repository need not remain checked out.

See [`gh skill` documentation](https://cli.github.com/manual/gh_skill_install) for installation and version pinning.

## Layout

~~~text
skills/
  meta-workspace/
    SKILL.md
    assets/                # Example files to adapt in a target workspace
  multi-repo-worktrees/
    SKILL.md
    assets/                # Optional task instructions template
~~~

The repository follows the portable `SKILL.md` format. Add each new skill at `skills/<name>/SKILL.md`, with any templates under that skill's `assets/`. Keep behavior in skills, durable project rules in each project's `AGENTS.md`, and deterministic automation in scripts only when genuinely needed.

For contributors, see [AGENTS.md](AGENTS.md).
