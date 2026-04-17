# Decision Simulator

Decision Simulator is a decision-quality layer for Claude Code.

It improves the part of agentic coding that breaks most often: judgment.

Claude can usually produce code. The harder problem is choosing the right path before it writes the code, and correcting itself when the path starts looking wrong halfway through.

That is what this plugin is for.

It helps Claude make better calls about:

- whether a refactor is justified or just tempting
- whether logic should stay local or move into a shared abstraction
- whether a dependency is actually worth adopting
- whether a bug should be patched, reworked at the boundary, or fixed more deeply
- whether an architecture decision is buying leverage or just buying complexity
- what information is still missing before a decision should be trusted

The goal is not to take over the workflow. The goal is to improve the decisions made inside the workflow you already use.

## How It Works

Decision Simulator activates when Claude hits a real decision point.

Sometimes that comes from the user:

- "Should I use X or Y?"
- "Should I refactor this now or leave it alone?"
- "Should this live in the frontend or backend?"

Sometimes it comes from Claude's own internal reasoning:

- it is about to introduce a shared abstraction
- it is drifting from a local fix into a broader rewrite
- it has found multiple plausible implementation paths
- it is making a choice with low reversibility or hidden long-term cost

When that happens, the plugin pushes Claude to slow down and do better work.

Instead of jumping straight into the first plausible path, it is prompted to:

- define the actual decision
- restate the goal
- identify meaningful constraints
- compare realistic options, including leaving things alone
- surface second-order effects and hidden costs
- identify missing context and uncertainty
- make a grounded recommendation with confidence

That changes the shape of Claude's behavior in practice.

Without a decision-quality layer, Claude often does one of four things:

- follows momentum into the first workable approach
- overvalues clean abstractions too early
- gives symmetrical pros and cons when one option is obviously stronger
- says "it depends" without saying what it depends on

With Decision Simulator, Claude becomes more likely to pick the boring-right path instead of the clever-wrong one.

## Where You Feel The Difference

### During planning

Claude gets better at deciding the shape of the work before implementation starts.

- Should this be a targeted fix or a real refactor?
- Is a new service, module, or abstraction warranted yet?
- Which unknowns matter enough to answer before starting?

### During implementation

Claude gets better at correcting itself while already in the work.

- Should this logic stay local or move into a shared layer?
- Is the current path still the best option now that the codebase context is clearer?
- Is this abstraction paying for itself, or just creating cleanup debt?

### During debugging

Claude gets better at choosing between symptom patches and durable fixes.

- Is this a local bug or a boundary problem?
- Is the real fix worth the scope increase right now?
- What is the lowest-risk fix that does not create recurring debt?

### During review or self-correction

Claude gets better at challenging its own preferred answer.

- What is the strongest argument against the current path?
- Which assumption is doing too much work?
- What cost only shows up after merge, rollout, or maintenance?

## Why This Is Useful

A lot of failed agentic work is not failed because the model cannot write code.

It fails because the model chooses a technically valid path that is still the wrong call for the project.

That usually looks like:

- code that works but is harder to debug
- abstractions that are tidy but premature
- architecture that is impressive but unnecessary
- fixes that solve the immediate issue while creating future drag
- tradeoff analysis that sounds thoughtful but is not actually useful

Decision Simulator is built for exactly that failure mode.

It does not try to make Claude more verbose. It tries to make Claude more convincing, more skeptical of weak ideas, and more useful when a technical decision actually matters.

## Installation

Decision Simulator is not in the official Anthropic marketplace yet.

For now, install it from GitHub through Claude Code.

### Claude Code

In Claude Code, add the GitHub repo as a marketplace:

```text
/plugin marketplace add echoo19/decision-simulator
```

Then install the plugin:

```text
/plugin install decision-simulator@decision-simulator
```

If Claude Code is already running, reload plugins:

```text
/reload-plugins
```

### Local testing

From the repository root:

```bash
claude --plugin-dir ./plugins/decision-simulator
```

Then try it with:

```text
/decision-simulator:decision-simulator Should I split this service now or wait?
```

## What’s Inside

Decision Simulator includes one main skill, three supporting skills, and one lightweight agent.

### Main skill

- `decision-simulator`
  The flagship decision-analysis skill. It defines the decision, clarifies the goal, compares realistic options, identifies constraints, surfaces hidden costs, and makes a recommendation with confidence.

### Supporting skills

- `evaluate-tradeoffs`
  Compares options across the dimensions that actually matter in context.

- `detect-context-gaps`
  Finds the missing information and assumptions that could flip the answer.

- `compare-implementation-paths`
  Compares practical approaches to the same goal and explains when each path is better.

### Agent

- `decision-reviewer`
  A lightweight subagent for deeper technical decision analysis when Claude needs a stronger recommendation or wants to pressure-test its own next move.

## Example Prompts

- "Should I keep this validation in the frontend or move it to the backend?"
- "Should I refactor this now or leave it alone?"
- "Should I adopt TanStack Query here or is that overkill?"
- "Compare an incremental refactor versus a rewrite for this auth flow."
- "Before you keep going, check whether this abstraction is actually worth introducing."
- "You are deciding between patching this locally or moving it into a shared module. Compare those paths first."
- "What am I missing before I commit to this architecture?"

## A Simple Before / After

Without a decision-quality layer, Claude often says something like:

> Both approaches have tradeoffs. Backend validation is more secure, while frontend validation is faster and simpler. It depends on your needs.

With Decision Simulator, the answer is pushed toward something more like:

> Keep lightweight client checks for UX, but move authoritative validation to the backend. Frontend-only validation is faster this week, but it duplicates rules, weakens guarantees, and creates debugging drift once multiple clients exist. The real cost is not the validation logic itself; it is rule divergence over time.

That is the point of the plugin: less generic balance, more usable judgment.

## Compatibility

Decision Simulator is designed to be composable.

It does not install hooks, take over planning mode, or assume it is the only plugin active.

It is intended to work well:

- for normal Claude Code use
- for casual interactive coding sessions
- for more agentic workflows where Claude is making its own next-step decisions
- alongside plugins like Superpowers that structure the workflow but still benefit from better decision quality inside each step

## Validation

Validate the marketplace manifest:

```bash
claude plugin validate ./.claude-plugin/marketplace.json
```

Validate the plugin:

```bash
claude plugin validate ./plugins/decision-simulator
```

## Repository Structure

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
  PRIVACY.md
  .gitignore
  LICENSE
```

## Contributing

Contributions are welcome, but this plugin should stay narrow.

Decision Simulator is not meant to become a general workflow framework or a grab bag of prompts. Good contributions usually make the plugin better at technical judgment without making it noisier, broader, or more controlling.

Good contribution areas:

- sharper tradeoff analysis
- better examples and README improvements
- better detection of missing context and weak assumptions
- clearer interoperability with Claude Code workflows and other plugins
- fixes to skill triggering, wording, or recommendation quality

Less likely to be a fit:

- mandatory workflow control
- hooks that try to take over Claude Code globally
- broad expansions unrelated to technical decision quality
- remote services, telemetry, or data collection behavior

### Suggested process

1. Open an issue first if the change is non-trivial.
2. Describe the concrete failure mode you want to improve.
3. Keep the scope focused on better decisions, not more process.
4. Test the plugin locally before opening a PR.
5. Submit a pull request with a short explanation of what changed and why.

### Local contributor workflow

Clone the repository and run the plugin locally:

```bash
claude --plugin-dir ./plugins/decision-simulator
```

Validate before opening a PR:

```bash
claude plugin validate ./.claude-plugin/marketplace.json
claude plugin validate ./plugins/decision-simulator
```

Useful contributor checks:

- try at least one explicit user decision prompt
- try at least one mid-task "Claude is about to choose a path" prompt
- make sure the plugin still feels composable and does not take over the workflow
- make sure examples and docs match actual behavior

### PR guidance

Good pull requests usually include:

- the problem being fixed
- the exact behavior change
- one or two example prompts that show the improvement
- any documentation updates needed to keep the repo accurate

## Limitations

- It improves judgment, not factual access. Missing business context or production data can still change the answer.
- It does not replace experiments, measurements, or rollout evidence.
- It does not manage execution after the decision is made.

## If This Helps

Use it on a real decision point, not a toy prompt.

The best tests are places where Claude is tempted to overbuild:

- a refactor that might not be worth it
- a shared abstraction that might be premature
- a library choice with hidden long-term cost
- a patch-vs-rewrite-vs-boundary-change call

If it saves you from one expensive wrong turn, it has already paid for itself.
