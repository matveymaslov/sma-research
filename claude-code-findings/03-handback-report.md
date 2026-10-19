# A subagent's report moved out of the tool result

**Claude Code 2.1.292, headless stream (`--output-format stream-json`).** The behaviour changed between 12.10 and 15.10.2026 with the same CLI version and flags.

## What we saw
Until 12.10 a subagent's final report arrived inside the tool result of the Agent/Task call. From about 15.10, the tool result carries only a pointer: "This agent's report was delivered to you as a message from "<id>" (its SubagentHandback call). Read it there; it is not repeated here." The report text now sits in `tool_use_result.handbackReport.text` of that frame and in an earlier `task_notification` frame (`handback_report.text`).

## How to reproduce
1. Run a headless session that spawns a subagent which ends with a short report.
2. Read the stream: the Agent tool result no longer contains the report; look for `handbackReport` and `task_notification`.

## Why it matters
Programs that judge agent work from the stream (gates, reviewers, orchestrators) read the tool result. After the change they saw «no report» and silently refused correct work. Same version number, no changelog line: there was nothing to catch it with.

## Expected
A documented, versioned stream contract, and a changelog entry when the place of the report moves; ideally the report stays in the tool result as well.

## Workaround
Read the report from all three places (tool result, `handbackReport`, `task_notification`) and replay real streams through the reader in tests.
