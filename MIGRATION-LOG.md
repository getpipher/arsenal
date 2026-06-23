# Migration Log — arsenal (CC cipher-kit → pi)

Tracks every adaptation made when porting skills from `RECTOR-LABS/cipher-kit` (Claude Code marketplace) into this pi package. Preserves the **original CC intent** so nothing essential is lost, and so future cipher-kit syncs know which pi-specific deviations exist (and can be reverted when pi gains the capability).

## Common adaptations (applied to ALL migrated skills)

| Aspect | CC original | pi adaptation | Rationale | Revert when |
|---|---|---|---|---|
| `name` | `<skill>` (flat) | `<plugin>-<skill>` hyphen-namespaced (namesakes kept clean, e.g. `solana-dev`) | pi has no `plugin:` namespace; avoids collisions | n/a (convention) |
| `allowed-tools` | CC tools (`Bash`/`Read`/`Write`/`Edit`/`Glob`/`Grep`/`Task`/`TodoWrite`/`WebFetch`/`AskUserQuestion`/`ToolSearch`/`LS`) | pi tools `["bash","read","write","edit"]` | pi has no Task/TodoWrite/WebFetch/AskUserQuestion/Glob/Grep-as-tools | pi gains those tools |
| `argument-hint` | often unquoted brackets (`[--x] [--y]`) — invalid YAML, silently skipped by pi | whole value double-quoted | pi YAML parser rejected flow-seq + trailing content | n/a (always valid now) |

## Per-skill body rewords

| Skill | Original (CC) | pi adaptation | Rationale | Revert/upgrade when |
|---|---|---|---|---|
| `git-tools/solve` | `AskUserQuestion` for issue pick; `TodoWrite` plan | ask user inline; outline steps inline | no AskUserQuestion/TodoWrite in pi | pi gains those tools |
| `git-tools/label-issues` | `AskUserQuestion` confirm | ask user inline | no AskUserQuestion | pi gains AskUserQuestion |
| `git-tools/pr-audit` | `AskUserQuestion` repo select | ask user inline | no AskUserQuestion | pi gains AskUserQuestion |
| `git-tools/merge-pr` | "within Claude Code" | "within the agent session" | tool-agnostic | n/a |
| `design/analyze` | `WebFetch` URL | `bash (curl)` | no WebFetch; pi uses bash | pi gains a fetch tool |
| `quality/type-check-strict` | `TodoWrite` plan | outline inline | no TodoWrite | pi gains TodoWrite/plan mode |
| `x-api/api` | "Claude Code sandbox strips bearer tokens" | "the agent sandbox may strip bearer tokens" | tool-agnostic | n/a |
| `solana-defi/phantom-wallet-mcp` | drives `phantom` MCP server | **reference-only** + pi-note header; use `solana-defi-phantom-connect` (SDK) instead | pi has no MCP | MCP-bridge extension exists (see issue #1 Path B) |
| `quality/qa` | parallel `Task` sub-agents (dev-QA fan-out) | **sequential** personas in one session | pi has no sub-agents | pi gains sub-agents/parallelism |
| `quality/qa` | "Load Chrome MCP tools" section (browser screenshots for end-user UX) | manual screenshots (deferred to browser-use) | pi has no Chrome MCP | browser-use integrated (see issue #1) |

## Pending (tracked elsewhere)

- **Chrome MCP → browser-use**: `quality/qa` (and `arsenal-private/upwork-bid`) still carry Chrome MCP intent — global replacement pass deferred to [issue #1](https://github.com/getpipher/arsenal/issues/1) (browser-use integration).
- **Sub-agent/parallelism**: `quality/qa` reworded to sequential (degraded for large scopes). Parallelism is a separate gap, not solved by browser-use.

## Syncing from cipher-kit

When a cipher-kit skill updates, diff against this log's row to see if the pi adaptation still applies. Re-apply the adaptation (don't blindly copy the new CC body — it will reintroduce CC-only tool refs).