---
name: workspace-ecosystem-claude
description: Create ecosystem-wide CLAUDE.md architecture for multi-repo organizations
argument-hint: "[org-name]"
allowed-tools: ["bash", "read", "write", "edit"]
---

# Ecosystem CLAUDE.md Architecture

Bismillah! I'll analyze your repository and help set up ecosystem-wide CLAUDE.md architecture.

**This command is always safe to run.** It detects first, shows analysis, and asks before any changes.

---

## STEP 1: DETECT (Always runs first - completely safe)

### 1.1 Get Repository Info

```bash
# Get organization from git remote
git remote get-url origin 2>/dev/null

# Get current branch
git branch --show-current

# Check if CLAUDE.md exists and count lines
ls -la CLAUDE.md 2>/dev/null
wc -l CLAUDE.md 2>/dev/null
```

### 1.2 Determine CLAUDE.md Type

If CLAUDE.md exists, read it and check for these markers:

| Marker Found | Type |
|--------------|------|
| `## ECOSYSTEM OVERVIEW` | Ecosystem Hub |
| `## REPOSITORY INDEX` | Ecosystem Hub |
| `> **Ecosystem Hub:**` | Satellite |
| None of above | Standard (single-repo) |
| File doesn't exist | None |

### 1.3 Scan for Related Repositories

Look for related repos:
- Check README.md for mentions of other repos
- Check existing CLAUDE.md for repo references
- Look for common local paths (same parent directory)
- Parse organization from git remote URL

For each related repo found, check if it has CLAUDE.md and what type.

---

## STEP 2: SHOW ANALYSIS REPORT

**Always show this report before any action:**

```
┌─────────────────────────────────────────────────────────────┐
│ ECOSYSTEM CLAUDE.md ANALYSIS                                │
├─────────────────────────────────────────────────────────────┤
│ Organization: [org-name]                                    │
│ Current Repo: [repo-name]                                   │
│ Branch: [branch]                                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ THIS REPOSITORY:                                            │
│   CLAUDE.md: [Exists / Missing]                             │
│   Type: [Ecosystem Hub / Satellite / Standard / None]       │
│   Lines: [N]                                                │
│                                                             │
│ RELATED REPOSITORIES:                                       │
│   [repo-1]: [Hub ✅ / Satellite ✅ / Standard ⚠️ / None ❌]  │
│   [repo-2]: [Hub ✅ / Satellite ✅ / Standard ⚠️ / None ❌]  │
│   ...                                                       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ ARCHITECTURE STATUS: [✅ Complete / ⚠️ Partial / ❌ Missing]│
│                                                             │
│ [Details about what exists and what's missing]              │
├─────────────────────────────────────────────────────────────┤
│ RECOMMENDATION:                                             │
│                                                             │
│ [Specific actionable suggestion]                            │
└─────────────────────────────────────────────────────────────┘
```

---

## STEP 3: ASK USER (Interactive decision)

Based on analysis, use **AskUserQuestion** tool with appropriate options:

### If No CLAUDE.md exists:

```
Question: "What would you like to create?"
Options:
- Ecosystem Hub (this is the main repo for the org)
- Satellite (this links to a hub elsewhere)
- Nothing (just wanted to check status)
```

### If Standard CLAUDE.md exists:

```
Question: "Upgrade to ecosystem architecture?"
Options:
- Upgrade to Hub (add ecosystem sections, keep existing content)
- Convert to Satellite (lightweight, links to hub)
- Keep as-is (no changes)
```

### If Ecosystem Hub exists:

```
Question: "Ecosystem Hub exists. What would you like to do?"
Options:
- Update entries (refresh repo info, versions, test counts)
- Nothing (just checking status)
```

### If Satellite exists:

```
Question: "Satellite exists. What would you like to do?"
Options:
- Update content (refresh info)
- Nothing (just checking status)
```

### If Architecture is Complete:

```
Just report: "Architecture is complete. All repos have proper CLAUDE.md files."
No action needed.
```

---

## STEP 4: EXECUTE (Only after user choice)

### 4.1 Commit Strategy

CLAUDE.md lives in each repo as a regular file. Commit directly:

```bash
git add CLAUDE.md
git commit -m "docs: update CLAUDE.md"
git push
```

### 4.2 Creating Ecosystem Hub

Add these sections at the top, preserve existing content below:

```markdown
# CLAUDE.md - [Project] Ecosystem

**Organization:** https://github.com/[org]
**Website:** [url]
**Purpose:** This file contains ecosystem-wide context for AI assistants working across all [org] repositories

---

## ECOSYSTEM OVERVIEW

[Brief description]

### Related Repositories

| Repo | Purpose | Tech Stack | Version |
|------|---------|------------|---------|
| `org/repo1` | **Core** - [desc] | [stack] | vX.X.X |
| `org/repo2` | [desc] | [stack] | vX.X.X |

**Organization Mission:** [mission]

---

## CROSS-REPO STANDARDS

### Shared Coding Standards
[formatting, quality gates, git workflow]

### Shared AI Assistant Guidelines

**DO:**
- [best practices]

**DON'T:**
- [anti-patterns]

### Licenses
[license info]

---

## REPOSITORY INDEX

### 1. [main-repo] (Core) - **YOU ARE HERE**

**Purpose:** [description]
**Tech Stack:** [tech]
**Key Commands:**
\`\`\`bash
[commands]
\`\`\`
**Key Files:**
- [files]

**Full details:** See "[PROJECT] CORE REPOSITORY" section below

---

### 2. [other-repo]

**Purpose:** [description]
**Tech Stack:** [tech]
**Key Commands:**
\`\`\`bash
[commands]
\`\`\`
**Key Files:**
- [files]

**CLAUDE.md:** [link]

---

## CURRENT FOCUS

See [ROADMAP.md](ROADMAP.md) for detailed tracking.

**Cross-Repo Coordination:**
- [notes]

---

# [PROJECT] CORE REPOSITORY

> **Note:** Sections below are specific to this repository.

[EXISTING CONTENT PRESERVED HERE]
```

### 4.3 Creating Satellite

```markdown
# CLAUDE.md - [Repo Name]

> **Ecosystem Hub:** See [hub-repo/CLAUDE.md](link) for full ecosystem context

**Repository:** https://github.com/[org]/[repo]
**Purpose:** [description]

---

## Quick Reference

**Tech Stack:** [tech]
**Deployment:** [if applicable]

**Key Commands:**
\`\`\`bash
[commands]
\`\`\`

---

## Key Files

| Path | Description |
|------|-------------|
| [path] | [desc] |

---

## Repo-Specific Guidelines

**DO:**
- [guidelines]

**DON'T:**
- [anti-patterns]

---

**Last Updated:** [date]
```

---

## DISCOVERY HELPERS

When gathering info for a repo:

**From package.json / Cargo.toml / pyproject.toml:**
- name, version, description
- scripts/commands

**From README.md:**
- Project purpose
- Related repos

**From directory structure:**
- Entry points (src/index.ts, main.py, cmd/main.go)
- Key directories

**From git:**
- Organization name (from remote URL)
- Current branch

---

## KEY PRINCIPLES

1. **Always safe** - Detect first, ask before write
2. **User decides** - Never assume, always ask
3. **Hub = full context** - AI can work anywhere with just the hub
4. **Satellite = lightweight** - Only repo-specific info
5. **No status fields** - Avoid things that need constant updates
6. **Simple table** - `| Repo | Purpose | Tech Stack | Version |`
7. **Regular files** - CLAUDE.md lives in each repo, committed to git normally

---

Alhamdulillah! Run this command anytime - it's safe and will guide you through the process.
