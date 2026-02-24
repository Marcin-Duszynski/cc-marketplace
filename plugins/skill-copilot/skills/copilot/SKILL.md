---
name: copilot
description: This skill should be used when the user asks to run GitHub Copilot CLI, "gh copilot", "copilot run", "copilot resume", "copilot continue", "use copilot for", "ask copilot to", "send to copilot", "let copilot handle", or references GitHub Copilot for code analysis, refactoring, code review, or automated editing. Also triggers on requests to delegate tasks to Copilot or compare Copilot's output with Claude's own analysis.
version: 1.0.0
---

# Copilot Skill Guide

## Running a Task

1. Ask the user (via `AskUserQuestion`) which model to run in a **single prompt with one question**. Present only these model categories:
   - **GPT models**: `gpt-5.3-codex` (default), `gpt-5.2-codex`, `gpt-5.2`, `gpt-5.1-codex-max`, `gpt-5.1-codex`, `gpt-5.1`, `gpt-5.1-codex-mini`, `gpt-5-mini`, `gpt-4.1`
   - **Claude models**: `claude-opus-4.6`, `claude-opus-4.6-fast`, `claude-opus-4.5`, `claude-sonnet-4.6`, `claude-sonnet-4.5`, `claude-sonnet-4`, `claude-haiku-4.5`
   - **Gemini models**: `gemini-3-pro-preview`
2. Determine the permission level required for the task. Default to **no permission flags** (read-only, restricted to CWD). Escalate only when needed:
   - Read-only analysis: no flags needed (default)
   - File edits required: `--allow-tool 'write'`
   - Shell commands required: `--allow-tool 'shell(command:*)'` (scope to specific commands where possible, e.g. `--allow-tool 'shell(git:*)'`)
   - Full access (network, all paths, all tools): `--yolo` — **ask user permission via `AskUserQuestion` before using**
3. Assemble the command:
   ```
   gh copilot -- -p "PROMPT" --model <MODEL> [permission flags] -s
   ```
   Key flags:
   - `-p "PROMPT"` — non-interactive, single prompt, exits after completion
   - `--model <MODEL>` — selected model
   - `-s` / `--silent` — output only the agent response (no stats), cleaner for parsing
   - `--allow-tool` / `--deny-tool` — granular tool permissions
   - `--allow-all-tools` — allow all tools without confirmation
   - `--add-dir <DIR>` — grant access to additional directories beyond CWD
   - `--yolo` / `--allow-all` — enable all permissions (tools, paths, URLs)
4. Run the command via Bash, capture stdout, and summarise the outcome for the user.
5. **After Copilot completes**, inform the user: "You can resume this Copilot session at any time by saying 'copilot resume' or asking me to continue with additional analysis or changes."

### Quick Reference

| Use case | Permission level | Key flags |
| --- | --- | --- |
| Read-only review or analysis | Default (no flags) | `-p "PROMPT" --model MODEL -s` |
| Apply local file edits | Write tools | `--allow-tool 'write' --allow-all-tools -s` |
| Run shell commands (scoped) | Specific shell | `--allow-tool 'shell(git:*)' --allow-all-tools -s` |
| Full access (network, paths) | All permissions | `--yolo -s` |
| Resume recent session | Inherited | `--continue -p "PROMPT" -s` |
| Run from another directory | Match task needs | `--add-dir <DIR>` plus other flags |

## Resuming Sessions

- To resume the most recent session: `gh copilot -- --continue -p "follow-up prompt" -s`
- To resume a specific session: `gh copilot -- --resume <session-id> -p "follow-up prompt" -s`
- Resumed sessions inherit the original model and permission configuration.
- After every Copilot command, immediately use `AskUserQuestion` to confirm next steps, collect clarifications, or decide whether to resume.
- Restate the chosen model and permission level when proposing follow-up actions.

## Critical Evaluation of Copilot Output

Copilot can be powered by various models (GPT, Claude, Gemini) with their own knowledge cutoffs and limitations. Treat Copilot as a **colleague, not an authority**.

### Guidelines

- **Trust internal knowledge** when confident. Push back directly if Copilot claims something incorrect.
- **Research disagreements** via web search or documentation before accepting Copilot's claims. Share findings with Copilot via resume if needed.
- **Account for knowledge cutoffs** — Copilot may not know about recent releases, APIs, or changes post-training.
- **Evaluate critically, never defer blindly** — scrutinise Copilot's suggestions, especially regarding:
  - Model names and capabilities
  - Recent library versions or API changes
  - Best practices that may have evolved

### When Copilot is Wrong

1. State the disagreement clearly to the user.
2. Provide evidence (own knowledge, web search, docs).
3. Optionally resume the Copilot session to discuss the disagreement. **Identify as Claude** so Copilot knows it is a peer AI discussion. Include the actual model name:
   ```bash
   gh copilot -- --continue -p "This is Claude (<your current model name>) following up. I disagree with [X] because [evidence]. What's your take on this?" -s
   ```
4. Frame disagreements as discussions, not corrections — either AI could be wrong.
5. Let the user decide how to proceed if there's genuine ambiguity.

## Error Handling

- Stop and report failures whenever `gh copilot -- --version` or a `gh copilot -- -p` command exits non-zero; request direction before retrying.
- Before using `--yolo` or `--allow-all`, ask the user for explicit permission via `AskUserQuestion`.
- When output includes warnings or partial results, summarise them and ask how to adjust using `AskUserQuestion`.
