# npx skills Command Reference

## Core commands

| Command | Aliases | Use for |
| --- | --- | --- |
| `npx skills add <package>` | `a`, `i`, `install` | Install a skill source |
| `npx skills remove [skills...]` | `rm`, `r` | Remove installed skills |
| `npx skills list` | `ls` | List installed skills |
| `npx skills find [query]` | `search`, `f`, `s` | Search for skills |
| `npx skills check` | None | Check for updates |
| `npx skills update` | `upgrade` | Update installed skills |
| `npx skills init [name]` | None | Create a new `SKILL.md` template |
| `npx skills experimental_install` | None | Restore from skill lock state |
| `npx skills experimental_sync` | None | Sync from `node_modules` |
| `npx skills --help` | `-h` | Show help |
| `npx skills --version` | `-v` | Show version |

## `add`

Use for installation from GitHub shorthand, repo URLs, git URLs, direct skill paths, or local paths.

Common flags:
- `-g, --global`: install globally
- `-a, --agent <agents...>`: target specific agents
- `-s, --skill <skills...>`: install specific skills
- `-l, --list`: list available skills without installing
- `--copy`: copy instead of symlink
- `-y, --yes`: skip prompts
- `--all`: install all skills to all agents
- `--full-depth`: search all subdirectories even when a root `SKILL.md` exists

Examples:
```bash
npx skills add vercel-labs/agent-skills
npx skills add vercel-labs/agent-skills --list
npx skills add vercel-labs/agent-skills -a codex
npx skills add vercel-labs/agent-skills --skill frontend-design
npx skills add vercel-labs/agent-skills --all
```

## `remove`

Use for uninstalling one, many, or all installed skills.

Common flags:
- `-g, --global`: remove from global scope
- `-a, --agent <agents>`: remove only from specific agents
- `-s, --skill <skills>`: specify skills explicitly
- `-y, --yes`: skip prompts
- `--all`: remove all skills from all agents

Examples:
```bash
npx skills remove
npx skills remove my-skill
npx skills rm my-skill
npx skills remove --global my-skill
npx skills remove --skill '*' -a cursor
```

## `list`

Use for showing installed skills.

Common flags:
- `-g, --global`: show global skills
- `-a, --agent <agents>`: filter by agent
- `--json`: machine-readable output

Examples:
```bash
npx skills list
npx skills ls -g
npx skills ls -a codex
npx skills ls --json
```

## `find`

Use for skill discovery or keyword search.

Examples:
```bash
npx skills find
npx skills find typescript
npx skills search typescript
```

## `check` and `update`

Use `check` to inspect whether updates are available.
Use `update` to apply all available updates.

Examples:
```bash
npx skills check
npx skills update
npx skills upgrade
```

## `init`

Use for creating a new skill template in the current directory or a named subdirectory.

Examples:
```bash
npx skills init
npx skills init my-skill
```

## Experimental commands

Use only when the user explicitly needs these workflows.

### `experimental_install`

Restore skills from a lock file (`skills-lock.json`). Useful for migrating skills to a new machine or restoring a previous state.

**Lock file locations:**
- Global: `~/.agents/.skill-lock.json`
- Project: `<project>/.agents/skills-lock.json`

**Common flags:**
- `-g, --global`: restore global skills
- `-y, --yes`: skip confirmation prompts

**Examples:**
```bash
# Restore project-level skills (reads from current directory's lock file)
npx skills experimental_install

# Restore global skills
npx skills experimental_install -g

# Restore without prompts
npx skills experimental_install -g -y
```

**Migration workflow:**
```bash
# On old machine: backup the lock file
cp ~/.agents/.skill-lock.json ~/backup/

# On new machine: restore from lock file
cp ~/backup/.skill-lock.json ~/.agents/
npx skills experimental_install -g -y
```

### `experimental_sync`

Sync skills from `node_modules` to create symlinks. Used when skills exist in node_modules but symlinks are missing.

**Examples:**
```bash
npx skills experimental_sync
npx skills experimental_sync -a codex -y
```

## Scope guidance

- Project scope is the default.
- Global scope usually means adding `-g`.
- Agent-specific actions usually mean adding `-a <agent>`.
- Skill-specific actions usually mean adding `-s <skill>`.
- Repository-wide installation can omit `-s`.
