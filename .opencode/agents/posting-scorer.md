---
description: Fetches a batch of scraped postings, runs the integrity gate on each, and scores them against the candidate rubric the orchestrator passes inline. Dispatched by /rank Step 2. Reads and fetches only; cannot edit or write files. (OpenCode adapter for .claude/agents/posting-scorer.md.)
mode: subagent
permissions:
  - { action: "edit", resource: "*", effect: "deny" }
  - { action: "subagent", resource: "*", effect: "deny" }
  - { action: "shell", resource: "*", effect: "deny" }
  - { action: "shell", resource: "bun run .agents/skills/*/cli/src/cli.ts *", effect: "allow" }
  - { action: "shell", resource: "python tools/fetch_page.py *", effect: "allow" }
  - { action: "shell", resource: "python tools/robots_check.py *", effect: "allow" }
  - { action: "shell", resource: "python tools/posting_guard.py *", effect: "allow" }
---

Your operating instructions are the canonical, Claude Code-native definition in
`.claude/agents/posting-scorer.md`. That file is the single source of truth:
read it now and follow it exactly. This OpenCode file only records the
environment differences.

Where the canonical file and this list disagree, this list wins:

- You have no Edit or Write tool.
- Shell access is limited to the portal CLIs' `detail` command,
  `tools/fetch_page.py`, `tools/robots_check.py` and `tools/posting_guard.py`,
  exactly as the canonical file describes.
- Claude Code tool names map as: `WebFetch` → `webfetch`, `WebSearch` →
  `websearch`, `Glob` → `glob`, `Read` → `read`.
- Replace `${SCRATCHPAD:?set this to the session scratchpad directory from your
  system prompt}` and `$SCRATCHPAD` with the scratchpad path given in
  `OPENCODE.md` (create the directory if it is missing).
- On Windows run the Python tools as `python`, not `python3`.

The orchestrator's prompt carries the job list, the scoring rubric, the JSON
shape to return and the status rules; follow it exactly.
