@AGENTS.md

## Notes for Claude

- Keep explanations short and plain.
- Run long jobs (full builds, staging test runs, anything that waits a long time) in background agents, not in shell background jobs. Shell jobs die with the session.
- The owner's memory is in `/root/.claude/projects/-var-www/memory/` (start with `MEMORY.md`). Check it for current production state before acting.
