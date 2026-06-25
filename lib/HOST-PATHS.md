# Host path map — for skill authors

pi skills (arsenal / arsenal-private) must resolve memory/cache/context paths
**per host**, not hardcode `~/.claude/...`. These skills are currently loaded
only by pi, but are written host-agnostically for portability.

There are two classes of path — handle them differently:

- **Host-variant** (config/memory/cache — differs CC vs pi): resolve via the
  host map + `PI_CODING_AGENT` below.
- **Host-invariant** (the iCloud vault `~/Documents/secret/...` — same on every
  host): use the **single canonical literal**, declared once in the global
  context (`shared.md`) and here. Do NOT introduce an env var for these (see
  the decision record at the bottom). A dotfiles pre-commit linter enforces the
  canonical spelling.

## Host-invariant paths (canonical literals)

| concept | canonical path |
|---|---|
| secret vault root | `~/Documents/secret/` |
| strategy docs | `~/Documents/secret/strategy/{project}/` (renamed from `claude-strategy` 2026-06-24) |
| secrets file | `~/Documents/secret/.env` |

These are the only spellings to use in skills/docs. The pre-commit linter bans
the old `claude-strategy` name and enforces `secret/strategy`.

## Host detection

| env var | CC | pi |
|---|---|---|
| `PI_CODING_AGENT` | (unset) | `true` |

**Bash:** `[ "$PI_CODING_AGENT" = "true" ]` → pi, else CC.
**Model logic:** the host addendum already names the host + its path map.

## Canonical paths

| concept | Claude Code | Pi Coding Agent |
|---|---|---|
| agent home | `~/.claude` | `~/.pi/agent` |
| global context file | `~/.claude/CLAUDE.md` | `~/.pi/agent/AGENTS.md` |
| project context file | `AGENTS.md` / `CLAUDE.md` in repo root/parents | same (`AGENTS.md` / `CLAUDE.md`) |
| memory dir | `~/.claude/projects/<cwd-slug>/memory/` | `~/.pi/agent/memory/<context>/` |
| cache dir | `~/.claude/cache/` | `~/.pi/agent/cache/` |
| skill source dir | `~/.claude/skills/` or `~/.claude/plugins/…` | `~/local-dev/arsenal*/skills/<name>/` |
| per-project commands/hooks | `.claude/commands/`, `.claude/hooks/` | none (pi: skills via packages / `.pi/skills/`) |

## How to write a host-agnostic path in a SKILL.md

Show both, let the model pick by host (it knows its host from the addendum):

```markdown
Store results in your host's cache dir:
- Claude Code: `~/.claude/cache/<file>`
- pi: `~/.pi/agent/cache/<file>`
```

For **bash** inside a skill, branch on the env var:

```bash
if [ "$PI_CODING_AGENT" = "true" ]; then
  MEM="$HOME/.pi/agent/memory/${CONTEXT}"
else
  MEM="$HOME/.claude/projects/$(pwd | ... slug ...)/memory"
fi
```

## Notes

- The agent-home concept differs: CC keys memory by **cwd slug** (`projects/<slug>/`);
  pi keys by **named context** (`memory/<context>/`). When migrating a CC memory
  skill to host-agnostic, pick a stable `<context>` name (e.g. the project or
  workflow: `hackathon`, `upwork`).
- Memory migration (moving the actual files CC→pi) is tracked in TODO #4 — do
  that before relying on the pi memory path in the 2 memory skills
  (`hackathon/research`, `upwork/bid`).

## Decision record: why NOT an env var ($STRATEGY_DIR)?

Considered `STRATEGY_DIR` in `.env` for the host-invariant paths. Rejected
(tested 2026-06-24):

1. **~80% of refs are prose** an LLM reads to build a path ("Write to: …").
   Models don't expand shell variables, so `$STRATEGY_DIR` in prose stalls
   the agent. Env vars are a code/bash pattern, an anti-pattern for prose skills.
2. **`.env` is sourced only by interactive `.zshrc`**, not `.zshenv`. Vars are
   empty in non-interactive shells (daemon/GUI/cron launches) — fragile even in
   the bash snippets.

For a host-**invariant** value consumed by prose-reading models, the proper fix
is a single canonical literal enforced by a commit-time linter (DRY-by-contract),
not indirection. The dotfiles pre-commit hook enforces it.
