---
framework_version: 1.1.0
---

# Agent Guidelines: AI Job Search

This workspace is structured to manage job search activities, scraper tools, CVs, cover letters, and interview preparation.

## Thin-Pointer Design (Single Source of Truth)

To prevent duplication and configuration drift across different AI agent frameworks (Claude Code, Google Antigravity, Codex, Cursor, Gemini CLI, etc.), this workspace uses a unified thin-pointer design. All agent runtimes should load the canonical specifications and candidate profiles from the files and directories below:

1. **Personal Candidate Profile:**
   - The candidate profile, contact details, education, and target preferences are defined in [CLAUDE.md](CLAUDE.md) and the individual profile methodology files under [.claude/skills/job-application-assistant/](.claude/skills/job-application-assistant/) (specifically `01-*.md` etc.).
2. **Canonical Workflow Specifications:**
   - The step-by-step instructions and triggers for tasks (setup, scrape, rank, apply, upskill, interview) are defined in the [.claude/](.claude/) directory (specifically under `.claude/skills/` and `.claude/commands/`).
   - Do not duplicate these rules or specifications. Treat `.claude/` files as the single source of truth.
3. **Portal Search Skills:**
   - Job-portal search CLIs live under [.agents/skills/](.agents/skills/) in the portable Agent Skills format (with a `SKILL.md` per portal). Codex and Antigravity discover these automatically; the `/scrape` workflow in [.claude/skills/job-scraper/](.claude/skills/job-scraper/) orchestrates them.

## OpenCode Runtime

This workspace also runs on [OpenCode](https://opencode.ai). OpenCode discovers the skills under `.claude/skills/` and `.agents/skills/` natively, loads `CLAUDE.md` as an instruction, and picks up the slash commands and sub-agents in `.opencode/`. The OpenCode adapter - tool-name mapping, the `${SCRATCHPAD}` substitution, Windows specifics, and the `/gmail-sync` + `/notion-sync` MCP requirements - is documented in [OPENCODE.md](OPENCODE.md), and the permissions mirroring `.claude/settings.json` live in [opencode.jsonc](opencode.jsonc). The `.opencode/` files are thin pointers into `.claude/`, so keep editing the canonical `.claude/` specs.
