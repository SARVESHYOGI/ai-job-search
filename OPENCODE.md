# OpenCode Adapter

This repository is a **Claude Code-native** framework. Per [AGENTS.md](AGENTS.md),
the files under [`.claude/`](.claude/) are the **single source of truth** for the
profile, the workflow specifications and the skills. This file does not restate
them. It only explains how to run the framework on [OpenCode](https://opencode.ai),
whose tool names and configuration differ from Claude Code's.

If you are the agent, treat everything in `.claude/` as authoritative and use the
mappings below to translate it. Do not duplicate workflow text into `.opencode/`.

---

## 1. Start OpenCode from the repository root

Open from **this directory** (the folder containing this file):

```powershell
cd E:\projects\job\ai-job-search
opencode
```

Project configuration, `.opencode/commands`, and skill discovery are all relative
to the directory OpenCode is opened in. Opened from a parent folder, none of the
`/apply`, `/scrape`, ... commands or the profile skills are found.

## 2. What OpenCode discovers automatically

| Piece | Location | How OpenCode picks it up |
| --- | --- | --- |
| `AGENTS.md` | repo root | automatic instructions |
| `CLAUDE.md` | repo root | loaded via `instructions` in `opencode.jsonc` |
| This file | repo root | loaded via `instructions` in `opencode.jsonc` |
| Profile + workflow skills | `.claude/skills/*/SKILL.md` | OpenCode's `.claude/skills` compatibility |
| Portal search skills | `.agents/skills/*/SKILL.md` | OpenCode's `.agents/skills` compatibility |
| Slash commands | `.opencode/commands/*.md` | thin pointers into `.claude/commands/` |
| Subagents | `.opencode/agents/*.md` | `posting-fetcher`, `posting-scorer`, `application-reviewer` |

Because `.claude/skills` is natively discovered, **the skills were not copied** --
`job-application-assistant`, `job-scraper` (skill ID `job-scraper`, display name
`scrape`) and `upskill` work as-is.

## 3. Tool-name mapping (Claude Code → OpenCode)

The canonical files in `.claude/` name Claude Code tools. Translate them:

| In `.claude/` | Use in OpenCode |
| --- | --- |
| `WebFetch` | the `webfetch` tool |
| `WebSearch` | the `websearch` tool |
| `Read` / `Glob` / `Grep` | the `read` / `glob` / `grep` tools |
| `Write` / `Edit` | the `write` / `edit` tools |
| `Bash` | the `shell` tool |
| `AskUserQuestion` | the `question` tool |
| `Skill(<name>)` | the `skill` tool with `id: "<name>"` |
| "the Agent tool (`subagent_type: X`)" | the `subagent` tool with `agent: "X"` |

Dispatch the three shipped subagents by their OpenCode IDs: `posting-fetcher`,
`posting-scorer`, `application-reviewer`.

## 4. The scratchpad (`${SCRATCHPAD}`)

The Claude Code specs pass a session scratchpad via
`${SCRATCHPAD:?set this to the session scratchpad directory from your system prompt}`
and `$SCRATCHPAD`. That variable does not exist in OpenCode's shell. Whenever a
spec mentions either, **substitute this directory** (create it if missing):

```text
%LOCALAPPDATA%\Temp\opencode
```

Example: `python tools/posting_guard.py --in "%LOCALAPPDATA%\Temp\opencode\posting.txt" --text --json`

## 5. Windows specifics

- Use **`python`**, not `python3` (a python.org install ships no `python3`, so the
  name reaches the Microsoft Store alias). The repo's own specs say this too.
- Prefer `python tools/install_portal_clis.py` over the `python3` spelling.

## 6. `/gmail-sync` and `/notion-sync` need MCP servers

These two commands look for Claude Code connectors (`mcp__claude_ai_Gmail__*`,
`mcp__notion__*`). Add the equivalent MCP server in OpenCode first:

```powershell
opencode mcp add notion --url https://mcp.notion.com/mcp
```

Gmail has no first-party OpenCode MCP server, so `/gmail-sync` will not work
unless you configure one (for example a Google Workspace MCP server). The other
commands are unaffected.

## 7. Permissions

`opencode.jsonc` mirrors the security posture of `.claude/settings.json`:
secrets are unreadable, framework/CI files are ask-before-edit, and shell
commands ask unless they are on the standing allow list (the portal CLIs, the
`tools/*.py` helpers, `pdftotext`, and the LaTeX engines).

If the prompts get noisy, narrow the change in `opencode.jsonc` -- for example
add `{ "action": "shell", "resource": "git status *", "effect": "allow" }`, or
change the blanket `shell * → ask` rule to `allow` to match OpenCode's default.
Keep the secret `deny` rules and the framework `ask` rules.

Upstream keeps its permission list deliberately per-file and CI-guarded. This
adapter is intentionally slightly looser (`tools/*.py`, all portal CLIs) so that
new tools and portals work without a config edit. New **Python** tools outside
`tools/`, or new shell commands, still need a rule here.

## 8. Keeping `.claude/` canonical

Edit profile and workflow content in `.claude/` (or via `/setup`), never in
`.opencode/`. The `.opencode/` files are pointers; if you edit the workflow, the
Claude Code path and OpenCode path stay in sync automatically.
