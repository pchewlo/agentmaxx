---
name: agentmaxx
description: Agentmaxx mode. Run substantial work as large parallel multi-agent Workflows, sized to what this machine can take right now. Invoking /agentmaxx is the user's explicit opt-in to Workflow orchestration.
argument-hint: [task]
---

The user has opted in to multi-agent orchestration. Use the Workflow tool, with as many agents as the work splits into. Don't drop to one agent because of token cost.

Task: $ARGUMENTS

- **No task given:** agentmaxx mode is now on for the rest of this session. Reply "Agentmaxx on." and apply the steps below to every substantial task the user gives from here on. Small edits and conversational replies stay solo. Turn it off when the user says "agentmaxx off".
- **Task given:** apply the steps below to it now. Mode stays on afterwards.

1. **Check the machine.**
   - macOS: `sysctl -n hw.ncpu hw.memsize vm.loadavg; memory_pressure -Q | tail -1`
   - Linux: `nproc; free -g | awk '/Mem/{print $2, $7}'; cat /proc/loadavg`

   Work out the limits from what it reports:
   - Agents running at once: min(16, cores − 2). The rest queue.
   - Headless browsers at once: RAM in GB ÷ 4, at most 6.
   - Builds, installs, test suites, ML at once: cores ÷ 4, rounded, at least 1.
   - Free memory under 25%, or a 1-minute load above two-thirds of the core count: halve the browser and build limits and say so.

2. **Scout inline first.** Build the work list (files, sites, sources, items) before writing the script. One agent per item is the default.

3. **Size the fleet by what each agent runs.**
   - Thinking, reading, web fetches, plain HTTP scraping: no limit, use 50+ if the work splits that far.
   - Headless browser (Playwright/Chromium): no more than the browser limit at once. Run them in sequential `parallel()` batches of that size.
   - Builds, `npm install`, test suites, ML/upscaling: no more than the build limit at once.
   - Agents writing files in the same repo in parallel: `isolation: 'worktree'`.
   - To run more agents at once than one workflow allows, split the items into slices and launch several workflows side by side.

4. **Pick models.** Workflow agents inherit the session model unless told otherwise. Set `model: 'sonnet'` for build, research and verify agents and `'haiku'` for trivial load or format steps. Keep the main session's model for judgement and merging.

5. **Shape:** `pipeline()` over the items (do → verify), then one compile/synthesis agent. Add an adversarial verify stage for research or audits. `log()` anything skipped or capped.

6. **Before launching**, tell the user in one line how many agents, which heavy tools, the batch sizes and the models.

The user's standing rules (CLAUDE.md, memory) still apply. Don't take outward-facing actions, such as sending messages, publishing or deploying, without asking.
