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

## Why I made it

I run a daily job feed that scrapes academic job listings from university websites. In October 2026 I asked Claude Code to add about 100 European universities as sources, and it worked through them with a single agent open. The job split cleanly into one scraper per university, so I asked why it wasn't running 50 agents at once.

There were three reasons. Claude Code only starts a multi-agent workflow when you opt in within the prompt, and a preference saved in memory doesn't count. Once you opt in, the default size guideline is under 10 agents. And a workflow runs at most min(16, CPUs − 2) agents at the same time, because the agents run on your machine. The model is in the cloud, but each agent is a local process that runs its bash commands, scrapers and builds locally.

The agents themselves are cheap. On my Mac (15 cores, 24 GB) eleven Claude processes were using about 1.75 GB between them, roughly 150 MB each. What slows the machine down is the tools they run. Each headless Chromium takes 300 MB to 1 GB, and builds, `npm install` and test suites each take a full core. So capping the number of agents is the wrong limit. Thinking, reading and plain HTTP fetches can go wide, and browsers and builds need their own lower caps.

The skill checks cores, memory and load before each run, sets the browser and build caps from them (6 and 4 on my Mac), halves them if the machine is already busy, and says in one line how many agents it will start before it launches. It also works as a mode switch, so I type `/agentmaxx` once at the start of a session instead of opting in on every prompt.

The model step came after the first big run. Workflow agents inherit the session model, so 110 agents all ran on Opus, hit my 5-hour usage limit, and 92 of them failed. Workers now run on Sonnet, trivial steps on Haiku, and Opus stays in the main session.

The caps are rules of thumb from one Mac, not measurements. In practice your Claude usage limit, or rate limiting from the sites you scrape, usually stops you before your machine does.

## Licence

MIT
