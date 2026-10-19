# Claude Code findings

Behaviour of Claude Code we hit while running it for weeks of autonomous, multi-agent work. Each entry: what we saw, how to reproduce it, why it matters for people building on the tool, and the workaround we use. Each is also filed upstream as an issue.

| # | Finding | Kind | Upstream |
|---|---|---|---|
| 1 | [A subagent spawned without a model answers partly on an older model](01-subagent-model.md) | bug | not filed yet |
| 2 | [`--resume` reuses the first system prompt, so a changed `--append-system-prompt` never reaches the resumed session](02-resume-system-prompt.md) | behaviour / docs | not filed yet |
| 3 | [A subagent's report moved out of the tool result](03-handback-report.md) | breaking change | not filed yet |
| 4 | [Idle background watchers are reaped under memory pressure and may not be restarted](04-watchers-reaped.md) | feature request | not filed yet |
