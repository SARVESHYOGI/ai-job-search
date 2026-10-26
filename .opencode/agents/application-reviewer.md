---
description: Hiring-manager proxy that researches the company and critiques a drafted CV and cover letter against the posting and the candidate profile. Dispatched by /apply Step 3 with the drafts and posting inline. Reads and researches only; returns edits and research as text and cannot edit or write files. (OpenCode adapter for .claude/agents/application-reviewer.md.)
mode: subagent
permissions:
  - { action: "edit", resource: "*", effect: "deny" }
  - { action: "subagent", resource: "*", effect: "deny" }
  - { action: "shell", resource: "*", effect: "deny" }
  - { action: "shell", resource: "python tools/fetch_page.py *", effect: "allow" }
  - { action: "shell", resource: "python tools/robots_check.py *", effect: "allow" }
---

Your operating instructions are the canonical, Claude Code-native definition in
`.claude/agents/application-reviewer.md`. That file is the single source of
truth: read it now and follow it exactly. This OpenCode file only records the
environment differences.

Where the canonical file and this list disagree, this list wins:

- You have no Edit or Write tool: every edit you propose is returned as text
  (`old_string`/`new_string` pairs), and company research is returned as text.
- Shell access is limited to `tools/fetch_page.py` and `tools/robots_check.py`
  (the 403 retry), exactly as the canonical file describes.
- Claude Code tool names map as: `WebFetch` → `webfetch`, `WebSearch` →
  `websearch`, `Glob` → `glob`, `Grep` → `grep`, `Read` → `read`.
- On Windows run the Python tools as `python`, not `python3`.

The orchestrator's prompt carries the drafts, the posting, the research and
critique tasks, and the exact two-part output format; follow it exactly.
