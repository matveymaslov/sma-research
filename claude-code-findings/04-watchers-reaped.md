# Idle background watchers are reaped under memory pressure, and the agent may not restart them

**Claude Code 2.1.292, Windows 11, interactive session.** A feature request rather than a bug.

## What we saw
Long jobs (live test rounds of 2–10 hours) run as detached processes. Beside them the agent keeps a tiny background shell loop that waits for a status line (a few megabytes). When the machine's free memory drops (here under about 3 GB of 13 GB, while the user's browser and the test stand were running), Claude Code stops such idle background tasks and tells the agent not to restart them without the user's word. The detached job keeps running, but the only thing that would wake the agent on a failure is gone. Overnight that means nobody notices a red run until morning. This happened four times in one day.

## Why it matters
For autonomous work the watcher *is* the safety net. Killing it saves almost no memory: the memory is held by other processes. What it actually removes is the wake-up path.

## Request
- Exempt tasks below a small memory footprint, or let a task be marked as a watcher that must not be reaped.
- When a task is reaped, keep a scheduled re-check (cron) or offer to re-arm it automatically.
- Report what holds the memory, so the user can act on the real cause.

## Workaround
Watch from detached processes started outside Claude Code, plus a session cron alarm as the second wake path. Or start Claude Code with `CLAUDE_CODE_DISABLE_BG_SHELL_PRESSURE_REAP=1`.
