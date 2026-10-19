# A subagent spawned without a model answers partly on an older model

**Claude Code 2.1.292, Windows 11.** Parent session on `claude-opus-5-5`.

## What we saw
A background subagent was spawned through the Agent tool with a custom agent type whose definition has no `model:` line, and with no `model` parameter in the call. The tool description says such an agent inherits the parent's model. Its transcript shows a mix: across one long run (about 22 hours, resumed several times with SendMessage), **136 answers came from `claude-opus-4-8` and 1,383 from `claude-opus-5-5`**. The same pattern appears in two earlier sessions of the same project (106 and 30 answers on `claude-opus-4-8`). Nothing in the environment names that model: no `CLAUDE_CODE_SUBAGENT_MODEL`, `ANTHROPIC_MODEL` or `ANTHROPIC_DEFAULT_OPUS_MODEL`; user settings say `"model": "opus"`.

The user noticed it in the subscription usage statistics, not in the tool.

## How to reproduce
1. In a session on Opus 5.5, define a project agent without a `model:` line.
2. Spawn it in the background with the Agent tool, without `model`.
3. Resume it a few times with SendMessage over several hours.
4. Count the `"model"` field per answer in `~/.claude/projects/<project>/<session>/subagents/agent-<id>.jsonl`.

## Why it matters
Teams pin quality and cost to a model per role (for example, executors on Sonnet, reviewers on Opus). A silent fallback to another model changes both, and nothing in the session shows it.

## Expected
The subagent answers on the parent's model (or a documented default), every time, including after a resume; or the session shows which model each answer used.

## Workaround
Pass `model` explicitly on every spawn and give every agent definition an explicit `model:` line.
