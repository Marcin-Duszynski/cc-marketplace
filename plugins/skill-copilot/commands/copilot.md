---
description: Run a prompt via GitHub Copilot CLI
argument-hint: [prompt]
allowed-tools: Bash, AskUserQuestion
---

Invoke the `skill-copilot:copilot` skill to handle this request.

The user wants to run a task via GitHub Copilot CLI with this prompt: $ARGUMENTS

Follow the skill's workflow:
1. Ask which model to use via `AskUserQuestion` (the skill defines the default)
2. Determine the appropriate permission level for the task
3. Run `gh copilot -- -p "PROMPT" --model MODEL -s` with the assembled flags
4. Summarise the output
5. Offer to resume the session
