---
name: npx-skills-cli
description: Summarize, explain, and choose the correct `npx skills` command for installing, listing, searching, removing, checking, updating, initializing, or syncing agent skills. Use when a user asks how to use the `skills` CLI, wants the right command or flags, needs alias coverage, or needs help deciding between project scope, global scope, specific agents, or specific skills.
---

# Npx Skills Cli

Explain `npx skills` commands at the level the user needs.

Read [references/commands.md](references/commands.md) when you need the command catalog, aliases, options, or examples.

Prefer exact commands over abstract descriptions. Keep answers compact unless the user asks for a full walkthrough.

## Workflow

1. Identify the user's goal.
2. Map it to the nearest `npx skills` command.
3. Add the smallest set of flags needed for scope, agents, or skill selection.
4. Return one or more ready-to-run commands.
5. Mention aliases only when they help the user scan or compare commands.

## Decision Guide

- Use `add` when the user wants to install skills from a repo, URL, git source, or local path.
- Use `list` when the user wants to inspect what is already installed.
- Use `find` when the user wants discovery or keyword search.
- Use `remove` when the user wants to uninstall skills.
- Use `check` when the user wants to know whether updates exist.
- Use `update` when the user wants to upgrade installed skills.
- Use `init` when the user wants to scaffold a new `SKILL.md`.
- Use `experimental_install` when the user wants to restore from a lock file.
- Use `experimental_sync` when the user wants to sync skills from `node_modules`.

## Response Rules

- Distinguish project scope from global scope.
- Mention `-g` for global installs or removals.
- Mention `-a` or `--agent` when the user cares about specific agents such as `codex` or `claude-code`.
- Mention `-s` or `--skill` when the user wants specific skills rather than a whole repository.
- Mention `--all` when the user explicitly wants every skill on every agent.
- Mention aliases when they are present in the CLI and materially useful.
- If the user asks for "all commands", provide the full command list from the reference.
- If the user asks for a recommendation, provide the exact command first, then a one-line reason.

## Examples

- "How do I install a skill repo for Codex only?"
- "Show me all `npx skills` commands."
- "How do I remove one skill globally?"
- "What is the difference between `check` and `update`?"
- "How do I create a new skill template?"

## Reference

Use [references/commands.md](references/commands.md) for:
- canonical commands
- aliases
- common flags
- example invocations
- experimental commands
