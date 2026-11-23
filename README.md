# Podman Desktop agent skills

Agent Skills are reusable instructions and resources that help coding agents work
with the Podman ecosystem. Install skills from this repository with the
[skills CLI](https://github.com/vercel-labs/skills) or
[GitHub CLI](https://cli.github.com/manual/gh_skill_install).

## Prerequisites

For the `npx skills` commands below, install:

- [Node.js](https://nodejs.org/) 22.20.0 or later, with npm and npx.
- [Git](https://git-scm.com/downloads).
- A [supported coding agent](https://github.com/vercel-labs/skills/blob/main/docs/agents.md),
  such as Claude Code or Codex.

Individual skills may have additional requirements described in their `SKILL.md`.

## Discover skills

Run these commands from the project where you want to use the skills.

List the available skills without installing them:

```sh
npx skills add podman-desktop/agent-skills --list
```

## Install skills

Start an interactive installation, then select the skills and agents:

```sh
npx skills add podman-desktop/agent-skills
```

Install one skill, replacing `<name>` with a skill name returned by the discovery
command:

```sh
npx skills add podman-desktop/agent-skills --skill "<name>"
```

### Choose an agent

Use `-a` to select an agent. Repeat it to install for multiple agents:

```sh
npx skills add podman-desktop/agent-skills --skill "<name>" -a claude-code
npx skills add podman-desktop/agent-skills --skill "<name>" -a codex
npx skills add podman-desktop/agent-skills --skill "<name>" -a claude-code -a codex
```

Add `-y` to skip confirmation prompts when you have selected the skills and agents.

### Choose project or global scope

Installation defaults to the current project. Add `-g` to make a skill available
across projects for your user:

```sh
npx skills add podman-desktop/agent-skills --skill "<name>" -a claude-code -g
```

| Agent       | Project scope     | Global scope (`-g`) |
| ----------- | ----------------- | ------------------- |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Codex       | `.agents/skills/` | `~/.agents/skills/` |

The installer manages placement for each agent. Pass `--copy` to request copies
instead of symlinks where symlinking is supported.

## Check for updates

Check installed skills for changes without installing updates:

```sh
npx skills check
```

Apply available updates:

```sh
npx skills update
```

Choose the scope when prompted, or specify it explicitly:

```sh
# Skills in the current project
npx skills check -p
npx skills update -p

# Skills installed globally
npx skills check -g
npx skills update -g
```

These commands operate on installed skills in the selected scope, including skills
from other repositories. To target one skill, use `npx skills update "<name>" -p`
or `npx skills update "<name>" -g`.

With the bare `podman-desktop/agent-skills` source, unpinned `npx skills`
installations follow the repository's default branch. Changes to a skill merged
into that branch can be installed with `update`; a release is not required.

## Pin a revision

To install skills from a specific revision, replace `<full-commit-sha>` with the
full 40-character Git commit SHA:

```sh
npx skills add "podman-desktop/agent-skills#<full-commit-sha>" --skill "<name>" -a claude-code
```

A commit pin keeps the installation on that revision when checking or applying
updates. Run `add` again with another SHA to change the pin, or without the `#…`
suffix to follow the default branch again. Keep the same agent and scope options
when replacing an installation.

## Install with GitHub CLI

[GitHub CLI 2.90 or later](https://github.com/cli/cli/releases/tag/v2.90.0) provides
an alternative installer. Select a skill interactively:

```sh
gh skill install podman-desktop/agent-skills
```

To select a skill, agent, and scope explicitly:

```sh
gh skill install podman-desktop/agent-skills "<name>" --agent claude-code --scope project
gh skill install podman-desktop/agent-skills "<name>" --agent codex --scope user
```

GitHub CLI uses `user` for global scope. Its default project scope requires a Git
repository. Use the same installer to manage subsequent updates:

```sh
gh skill update --dry-run
gh skill update
```

Without an explicit version, `gh skill install` selects the latest tagged release
and falls back to the default branch HEAD when no tagged release is available.
Use `--pin <tag-or-sha>` to install a specific revision:

```sh
gh skill install podman-desktop/agent-skills "<name>" --agent claude-code --scope project --pin "<tag-or-sha>"
```

Because unpinned `npx skills` installations from the bare repository name follow
the default branch, the two installers may select different revisions when tagged
releases are available. GitHub CLI records its own source metadata for updates.
See its [installation](https://cli.github.com/manual/gh_skill_install) and
[update](https://cli.github.com/manual/gh_skill_update) documentation for details.
