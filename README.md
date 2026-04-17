# Decision Simulator For Claude Code

`decision-simulator` is the GitHub repository for the `decision-simulator` Claude Code plugin.

The plugin acts as a decision-quality layer for Claude Code. It improves judgment in two places:

- when a user explicitly asks a decision question
- when Claude itself reaches a real crossroads during planning, implementation, debugging, or architecture work

The repository includes the Claude Code marketplace metadata needed for GitHub installation, but the project itself is the plugin.

It is intentionally composable:

- no mandatory hooks
- no workflow takeover
- no assumption that it is always active
- no conflict by design with planning-oriented plugins such as Superpowers

## Repository Contents

```text
decision-simulator/
  .claude-plugin/
    marketplace.json
  plugins/
    decision-simulator/
      .claude-plugin/
        plugin.json
      skills/
        decision-simulator/
          SKILL.md
        evaluate-tradeoffs/
          SKILL.md
        detect-context-gaps/
          SKILL.md
        compare-implementation-paths/
          SKILL.md
      agents/
        decision-reviewer.md
      README.md
  README.md
  .gitignore
  LICENSE
```

## Purpose

This plugin exists to help Claude make better decisions, not just longer ones.

It is designed to improve moments like:

- Should I use X or Y?
- Should I refactor this or leave it alone?
- Should this logic live in the frontend or backend?
- Is this architecture too heavy for the problem?
- What are the real tradeoffs here?
- What am I missing before I commit to this path?
- Should Claude continue with the current implementation path or switch approaches?
- Is the abstraction it is about to introduce actually worth it?
- Is the next step reversible, or am I about to create cleanup debt?

The quality bar is practical judgment:

- real tradeoffs instead of generic bullets
- second-order effects and hidden costs
- explicit uncertainty and missing context
- grounded recommendations rather than fake balance

## How It Improves Claude Code

This plugin is useful when Claude is no longer just retrieving facts or writing code mechanically. It is most useful when Claude has to choose.

In practice, it improves Claude Code by making it more likely to:

- stop and compare plausible paths before committing to a costly implementation
- notice when a "clean" abstraction is actually premature and will create more cleanup than leverage
- distinguish between a quick patch, a durable fix, and a rewrite that is not justified yet
- identify where complexity lands after the change, not just whether the code looks nicer immediately
- call out rollback difficulty, migration cost, monitoring burden, and team coordination overhead before those become surprises
- make a recommendation with a stated confidence level instead of hiding behind vague balance

That changes the character of Claude's output. Instead of:

- listing interchangeable pros and cons
- drifting into architecture theater
- following momentum into the first plausible solution
- saying "it depends" without explaining what flips the answer

it becomes better at making and defending a specific call.

## Where It Helps In Agentic Work

The plugin is designed for agentic workflows, not just one-off Q&A.

### During Planning

It helps Claude pressure-test the shape of the plan before implementation starts.

- Should this be a small local change or a cross-cutting refactor?
- Is a new service, shared module, or abstraction actually warranted?
- Which unknowns are important enough to answer before work starts?

### During Implementation

It helps Claude catch bad mid-stream decisions that often look reasonable in the moment.

- extracting shared code too early
- moving logic across boundaries without enough payoff
- choosing the fastest path even when rollback is painful
- introducing dependencies or infrastructure that are expensive to unwind

### During Debugging

It helps Claude decide whether to patch symptoms or fix the real source of the problem.

- Is this a local bug or a boundary problem?
- Is the proper fix worth the scope increase right now?
- What is the lowest-risk fix that will not create recurring debt?

### During Review Or Self-Correction

It helps Claude challenge its own preferred path.

- What is the strongest argument against the current approach?
- Which assumption is carrying too much of the plan?
- What hidden cost shows up after merge, rollout, or maintenance?

## Why This Is Useful In Real Projects

A lot of agentic coding failures are not syntax failures. They are judgment failures.

The model can write correct code for the wrong approach. It can choose a technically valid implementation that is:

- harder to debug
- harder to roll back
- harder for the team to own
- overbuilt for the actual problem
- cheap today but expensive over the next month

Decision Simulator is aimed at that failure mode. It makes Claude more likely to pick the boring-right solution over the clever-wrong one.

## Installation

### Local testing with `--plugin-dir`

From the repository root:

```bash
claude --plugin-dir ./plugins/decision-simulator
```

After Claude Code starts, reload plugins if needed:

```text
/reload-plugins
```

Then try:

```text
/decision-simulator:decision-simulator Should I split this service now or wait?
```

### Install through Claude Code from a local checkout

From a parent directory that contains this repository:

```text
/plugin marketplace add ./decision-simulator
/plugin install decision-simulator@decision-simulator
```

### Install through Claude Code from GitHub

After publishing the repository to GitHub:

```text
/plugin marketplace add <github-owner>/decision-simulator
/plugin install decision-simulator@decision-simulator
```

## Local Validation

Validate the marketplace manifest:

```bash
claude plugin validate ./.claude-plugin/marketplace.json
```

Validate the plugin:

```bash
claude plugin validate ./plugins/decision-simulator
```

## Distribution Notes

- The repository includes `.claude-plugin/marketplace.json` so Claude Code can install the plugin from GitHub or a local path.
- The plugin follows current Claude Code conventions using `.claude-plugin/plugin.json`.
- Skills live in `skills/<skill-name>/SKILL.md`.
- The plugin can be loaded directly with `claude --plugin-dir`.

## Publishing

1. Create a new GitHub repository named `decision-simulator`.
2. Commit and push the repository.
3. Add a tag or release when you want a stable public version.
4. Share installation instructions using the GitHub `owner/repo` format so users can install the plugin through Claude Code.

## Limitations

- The plugin improves reasoning quality but cannot create missing business context.
- It does not validate assumptions empirically; some decisions still require experiments or production data.
- It does not manage downstream execution after the decision is made.
