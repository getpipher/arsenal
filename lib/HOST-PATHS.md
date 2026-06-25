# Host path map — for skill authors

pi skills (arsenal / arsenal-private) must resolve memory/cache/context paths
**per host**, not hardcode `~/.claude/...`. These skills are currently loaded
only by pi, but are written host-agnostically for portability.

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
