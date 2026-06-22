# arsenal

CIPHER's public skill library for [pi](https://pi.dev) — the shareable half of the toolkit.

## Contents

Pi-package of Agent Skills (standard `SKILL.md`), organized by domain:

- `git-tools/` — git workflow automation (commit, solve, pr-audit, create-pr, merge-pr, local-sync, standup, create-issue, get-pr-review, label-issues)
- `solana-defi/` — Solana DeFi protocol SDK references (planned)
- `solana-dev/` — end-to-end Solana dev playbook (planned)
- `quality/` — code quality gates (planned)
- `design/` — UI/UX analysis (planned)
- `vps-deploy/` — VPS deployment playbooks (planned)
- `x-api/` — X/Twitter API v2 reference (planned)
- `work/` — LOC counter, project-story generator (planned)

Personal/private skills live in `getpipher/arsenal-private`.

## Install

```bash
pi install git:github.com/getpipher/arsenal
```

## Consume in pi

Global (`~/.pi/agent/settings.json`):
```json
{ "packages": ["git:github.com/getpipher/arsenal"] }
```

Or for local development:
```json
{ "packages": ["/Users/rector/local-dev/arsenal"] }
```

## License

MIT — shared as sadaqah jariyah.