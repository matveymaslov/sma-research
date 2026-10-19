# `--resume` reuses the first system prompt; a changed `--append-system-prompt` never reaches the resumed session

**Seen on Claude Code 2.1.288 (headless, `-p`). Not yet re-checked on 2.1.292; the reproduction below is to be re-run before filing.**

## What we saw
An orchestrator resumed a worker's session (`--resume <id>`) and passed new instructions for this round only in `--append-system-prompt`. The resumed session received the system prompt recorded on the conversation's first request (the system-prompt snapshot is on by default) and a byte-identical user message. The worker re-checked work it had already finished, changed nothing, and the independent check that did see the new words refused it. The run hung waiting for a human decision.

## How to reproduce
1. `claude -p "say A" --append-system-prompt "Always answer in English" --output-format stream-json` and note the session id.
2. `claude -p --resume <id> "say B" --append-system-prompt "Always answer in German"`.
3. The second answer follows the first round's add-on.

## Why it matters
Any orchestrator that resumes sessions and varies the system add-on between rounds (a revised contract, a reviewer's words, per-round limits) loses that change silently. Nothing in the output says the add-on was ignored.

## Expected
Either the new `--append-system-prompt` applies on resume, or the CLI warns that it is ignored, and the docs say so next to both flags.

## Workaround
Everything a resumed session must hear this round goes into the prompt itself, fenced as data. Or turn the snapshot off on resumed launches.
