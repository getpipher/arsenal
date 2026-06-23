---
name: workspace-init-claude
description: Create or improve CLAUDE.md with intelligent codebase analysis
argument-hint: ""
allowed-tools: ["bash", "read", "write", "edit"]
---

# CLAUDE.md Initialization

Bismillah! I'll analyze this codebase systematically and create (or improve) a CLAUDE.md file for future Claude Code instances working in this repository.

---

## Discovery Process

### 1. Start with existing documentation
- Check for existing CLAUDE.md (suggest improvements if present)
- Read README.md for project overview and setup instructions
- Look for .cursor/rules/, .cursorrules, or .github/copilot-instructions.md
- Find contribution guidelines (CONTRIBUTING.md)

### 2. Discover commands and workflows
- Check package.json "scripts" (Node.js)
- Check Makefile targets
- Look for justfile, Taskfile, or similar task runners
- Find language-specific files: Cargo.toml, pyproject.toml, build.gradle, etc.
- Identify CI/CD configs (.github/workflows/, .gitlab-ci.yml) for standard commands

### 3. Understand architecture
- Read entry points (main.ts, index.js, main.py, cmd/main.go, etc.)
- Identify directory structure and its purpose
- Find configuration files and their significance
- Understand data flow and key abstractions
- Note any unconventional patterns or design decisions

---

## What to Include

### Essential Commands
- **Build**: How to compile/bundle the project
- **Development**: How to run locally with hot reload/watch mode
- **Testing**: How to run all tests, single test file, or specific test
- **Linting/Formatting**: How to check and auto-fix code style
- **Type Checking**: For typed languages (TypeScript, Python with mypy, etc.)
- **Database**: Migrations, seeding, reset commands if applicable
- **Deployment**: Build for production, deployment commands

### Architecture Insights (High-Level Only)
Focus on information that requires reading **multiple files** to understand:
- Overall project structure and why it's organized that way
- Key architectural patterns (e.g., "Uses hexagonal architecture with ports/adapters")
- Data flow between major components
- Important design decisions or constraints
- Where different types of work should happen (e.g., "Business logic in services/, API routes in controllers/")
- Non-obvious relationships between modules

### Project-Specific Context
- Language/framework versions and constraints
- Important environment variables or configuration
- Authentication/authorization approach if applicable
- External dependencies or services the project integrates with
- Conventions that aren't obvious from code (naming, file placement, etc.)

---

## What to Exclude

❌ Generic advice ("Write clean code", "Handle errors gracefully", "Write tests")
❌ Obvious information easily discovered by reading one file
❌ Exhaustive file/directory listings
❌ Repeated information from README
❌ Made-up sections like "Tips for Development" unless explicitly documented
❌ Basic development practices universal to all projects

---

## Structure

Use this structure (omit sections if not applicable):

```
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview
[1-2 paragraphs: what this project does, primary language/framework]

## Architecture and Structure
[High-level architecture, key design patterns, major components and their relationships]

## Development Environment
[Prerequisites, setup steps, environment variables]

## Common Commands
[Grouped by purpose: Development, Testing, Building, Database, etc.]

## Technology Stack
[Key dependencies and why they're used, if non-obvious]

## Workflow Guidelines
[Project-specific workflows like deployment, migrations, feature development]

## Key Files and Locations
[Only mention files that are important to understand but hard to discover]
```

---

## Quality Checklist

Before finalizing, verify:
- [ ] Commands are copy-pasteable and tested
- [ ] Architecture section adds value beyond what's in README
- [ ] No generic development advice
- [ ] No repetition of README content (reference it instead)
- [ ] Focused on what makes THIS codebase unique
- [ ] Information is actionable and specific
- [ ] Would actually help a new developer be productive faster

---

## Important Notes

- **If CLAUDE.md exists**: Analyze current content and suggest specific improvements
- **Commands**: Test commands before documenting (use Bash tool to verify they work)
- **Architecture**: Only document high-level patterns that require understanding multiple files
- **Be concise**: Quality over quantity - focus on what makes this codebase unique
- **Reference, don't repeat**: If README already documents something well, reference it
- **Prefix requirement**: Always start with:
  ```
  # CLAUDE.md

  This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
  ```

---

Alhamdulillah! Let's create documentation that will make future work in this codebase smooth and productive. InshaAllah! 📝✨
