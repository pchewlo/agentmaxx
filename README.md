# agentmaxx

A Claude Code skill that makes Claude split big jobs across many parallel agents, with caps on browsers and builds worked out from your machine's cores, memory and current load.

## Install

```sh
mkdir -p ~/.claude/skills/agentmaxx
curl -o ~/.claude/skills/agentmaxx/SKILL.md \
  https://raw.githubusercontent.com/pchewlo/agentmaxx/main/skills/agentmaxx/SKILL.md
```

It needs a Claude Code version with the Workflow tool, which is the multi-agent runner that typing "ultracode" turns on. It works on macOS and Linux.

## Use

- `/agentmaxx` turns the mode on for the rest of the session. Every substantial task after that runs as a multi-agent workflow. Small edits and quick replies stay with one agent.
- `/agentmaxx <task>` runs that task with many agents straight away and leaves the mode on.
- "agentmaxx off" turns it off.

Claude Code's default workflow size guideline is under 10 agents. Raise "Dynamic workflow size" in `/config` if you want it gone.

## Licence

MIT
