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
