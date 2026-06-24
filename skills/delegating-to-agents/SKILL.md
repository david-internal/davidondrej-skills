---
name: delegating-to-agents
description: MUST be read ANY time another AI agent is involved — sending a prompt to or polling a Pi/Codex/Claude Code/Hermes/OpenCode agent, driving an agent running in another cmux pane/surface/terminal/tmux window, delegating or relaying ANYTHING to an agent in the same cmux workspace, spawning a sub-agent, or orchestrating agent-to-agent work. Read it BEFORE the first message you send to another agent. Covers the critical rule that prompts sent to a TUI agent must be ONE single line (newlines = Enter = fragmented/cut-off prompts), short polling cadence, which agent to pick, and per-agent characteristics. If you are about to type `cmux send`/`tmux send-keys` to any agent, this skill applies.
---

# Delegating to Agents

## Which agent to pick

- **For most tasks → use the general-purpose local agent** configured in the workspace.
- **Default for coding → Codex CLI.** It is often strong on complex, long-running software engineering tasks. Delegate hard or multi-step engineering work here when appropriate.
- **Frontend / design → use an agent/model combination that is strong at UI, styling, and design work.**
- **A good setup for complex work:** use one agent as the orchestrator and delegate execution to a coding agent running in another pane/surface. This is not the only setup that works, but it is a solid default for heavy, long-running tasks.

## Polling cadence — keep sleeps SHORT

General principle: when waiting on another agent, use short `sleep` intervals so you check often. Do not `sleep 30` by default. Start with `sleep 3-5`; if the agent is not done, just `sleep 5` again and re-check. Scale up only for genuinely heavy tasks.

After every check, send the user a one-line progress update: what the other agent is doing and whether it is on track. Keep it extremely concise.

Claude Code cmux note: after Claude finishes, it may prefill a predicted next user message; that draft is Claude, not the user speaking.

## Sending prompts — NEVER put newlines in the message body

When sending a prompt to a TUI agent via `cmux send`, `tmux send-keys`, or similar, **the message text must be a single line**. In these TUIs a newline = Enter, so a multi-line string submits after the first line and the rest arrive as separate mid-turn messages — cutting off / fragmenting your prompt.

Fixes, pick one:
- Send the whole prompt as **one line**. Use `. ` or `; ` instead of line breaks, then one explicit `send-key enter`.
- For long or multi-step instructions, **write them to a temporary file** and tell the agent to read it: `cmux send --surface surface:N "read /tmp/task.md and follow it"` then `cmux send-key --surface surface:N enter`.

**cmux command names:** to target another agent surface use `cmux send --surface surface:N` and `cmux send-key --surface surface:N enter`. There is no `send-surface` or `send-key-surface` command — the `--surface` flag goes on the regular `send` / `send-key`.

**Always wrap the prompt in double quotes; do not escape the outer quotes.** A recurring bug is emitting `cmux send --surface surface:N \"...\"`. In bash, `\"` outside any quoting is broken and can yield `unexpected EOF while looking for matching '`. Rules:
- Outer quoting is a plain double quote: `cmux send --surface surface:N "your prompt here"`. Do not prefix the quotes with backslashes.
- Inside the prompt, avoid apostrophes/single-quotes and literal double quotes when possible. Rephrase instead.
- Do not switch to single-quote outer wrapping as a workaround unless you have checked the shell quoting carefully. If a previous send failed, the cause may have been escaped outer quotes.

## Remote or privileged environments

When work involves remote, privileged, or production-like environments, avoid relaying fragile per-command instructions through multiple layers. Use only authorized access paths, follow the project security policy, and prefer giving an appropriately scoped agent the context it needs in the environment where the work is performed. Do not include credentials, hostnames, IP addresses, or access instructions in prompts or public files.

## Agent characteristics

- **Fast local agents** usually launch and respond quickly. No need for long waits; `sleep 3-5` is often enough.
- **Coding agents** are useful for implementation, refactoring, test writing, CI debugging, and longer software engineering tasks.
- **Design-capable agents** can be better for UI, styling, layout, and product polish tasks.

## Common agent reference

Background only — do not over-index on this. Many agents use a portable **SKILL.md**-style convention. Project-local skill folders should generally take precedence over personal/global skill folders.

- **Pi** — lightweight, model-agnostic agent with a small core toolset that can be extended via skills, prompt templates, packages, or extensions. Often useful as an orchestrator or general-purpose assistant.
- **Hermes** — persistent autonomous agent pattern with cross-session memory, reusable skills, scheduling, and subagent delegation. Can orchestrate other agents as workers.
- **Claude Code** — Claude-centric coding agent with project conventions such as `CLAUDE.md`, rules, agents/subagents, skills, and plugins. Skills can be injected into the main conversation rather than run as separate processes.
- **Codex CLI** — coding-focused CLI agent with support for noninteractive execution, sandboxing, MCP-style integrations, and repository guidance files such as `AGENTS.md`.

## Driving interactive CLIs

- Codex, Pi, and OpenCode-style TUIs generally need a PTY when driven by tooling.
- Claude Code can often be used in noninteractive/print-style modes depending on the workflow.
- Orchestrator agents can drive coding CLIs as workers rather than acting only as one more coding CLI.
