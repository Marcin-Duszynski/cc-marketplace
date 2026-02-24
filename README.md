# skill-copilot

Claude Code plugin to delegate prompts to [GitHub Copilot CLI](https://gh.io/copilot-cli) for code analysis, refactoring, code review, and automated editing.

## Features

- **Multi-model support** — GPT, Claude, and Gemini models via Copilot CLI
- **Conservative permissions by default** — read-only analysis with explicit escalation
- **Session resume** — continue previous Copilot sessions seamlessly
- **AI-to-AI debate** — Claude critically evaluates Copilot output and can discuss disagreements
- **Slash command** — `/copilot <prompt>` for quick invocation

## Prerequisites

- [GitHub CLI](https://cli.github.com/) (`gh`) installed
- GitHub Copilot CLI extension installed (`gh copilot`)
- Active GitHub Copilot subscription

## Installation

1. In Claude Code, run `/plugin`
2. Add marketplace: `Marcin-Duszynski/skill-copilot`
3. Install and enable `skill-copilot`

## Usage

**Via skill (auto-triggers):**
- "Ask copilot to review this code"
- "Use copilot for refactoring"
- "Send to copilot for analysis"

**Via command:**
- `/copilot review this file for security issues`
- `/copilot explain the architecture of this project`

## Supported Models

| Provider | Models |
|----------|--------|
| OpenAI | gpt-5.3-codex (default), gpt-5.2-codex, gpt-5.2, gpt-5.1-codex-max, gpt-5.1-codex, gpt-5.1, gpt-5.1-codex-mini, gpt-5-mini, gpt-4.1 |
| Anthropic | claude-opus-4.6, claude-opus-4.6-fast, claude-opus-4.5, claude-sonnet-4.6, claude-sonnet-4.5, claude-sonnet-4, claude-haiku-4.5 |
| Google | gemini-3-pro-preview |

## Licence

MIT
