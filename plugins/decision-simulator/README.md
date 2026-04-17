# Decision Simulator

`decision-simulator` is a Claude Code plugin that improves technical judgment without trying to run the whole workflow.

It is built for the moments where generic AI answers become expensive:

- choosing between two plausible approaches
- deciding whether a refactor is worth it
- deciding whether architecture is overkill
- splitting responsibility across frontend and backend
- judging whether a library or dependency is worth adopting
- identifying what information is still missing before a call can be trusted
- deciding whether Claude should keep going down its current path or switch approaches mid-task

## Problem Statement

Claude is often directionally helpful but weak at decision quality. It tends to produce symmetrical pros and cons, bland "it depends" language, or shallow summaries that sound balanced without helping you choose.

This plugin is designed to raise the signal level. It helps Claude:

- identify the real decision, not just the surface wording
- compare realistic options rather than strawmen
- surface second-order effects and hidden operational costs
- separate facts from assumptions and uncertainty
- make a recommendation when one path clearly wins
- notice when its own next step is based on a weak assumption, expensive abstraction, or low-reversibility choice

This applies both to explicit user questions and to Claude's own internal crossroads while it is reasoning through work.

## How It Changes Claude's Behavior

This plugin is meant to change behavior, not just output formatting.

With it active, Claude should be more likely to:

- explicitly define the decision before solving the wrong problem
- compare the current path against realistic alternatives, including leaving the code alone
- name which factors are actually decisive instead of dumping every possible consideration
- surface second-order effects like migration burden, rule drift, debugging complexity, or ownership confusion
- catch low-reversibility moves before committing to them
- make a recommendation with confidence, instead of presenting fake balance

That matters because many bad agentic outcomes come from reasonable-looking decisions made too quickly:

- introducing an abstraction because duplication feels uncomfortable
- splitting code across boundaries without enough payoff
- selecting a library because it looks "proper" rather than because it solves the real constraint
- expanding scope from a targeted fix into a refactor that the codebase did not actually need

Decision Simulator is designed to interrupt that kind of weak momentum.

## How It Helps In Claude Code Workflows

### Planning

It sharpens plan quality before implementation begins.

- identifies whether the work is local or architectural
- finds the missing constraints that could invalidate the plan
- compares a minimal path against a heavier "proper" path

### Implementation

It helps Claude choose better execution paths while it is already in the work.

- decide whether to patch locally or move logic into a shared layer
- decide whether to keep the current approach or switch paths
- decide whether a new abstraction is buying enough to justify its cost

### Debugging

It improves fix quality by separating symptom patches from durable fixes.

- compare root-cause fixes versus containment patches
- weigh time-to-recovery against long-term maintenance cost
- call out when a "quick fix" is likely to create recurring incidents

### Review And Self-Correction

It helps Claude challenge its own preferred answer.

- strongest argument against the current path
- hidden operational cost after rollout
- assumptions that need to be verified before confidence should be high

## Why This Is Useful Alongside Other Plugins

This plugin is a judgment layer, not a workflow layer.

That is why it works well with tools like Superpowers. A planning plugin can structure the work; Decision Simulator improves the quality of the decisions made inside that structure.

In practice, that means:

- better brainstorming because weak options get pressure-tested earlier
- better plans because the decisive tradeoffs are explicit
- better implementation because Claude is less likely to overbuild
- better reviews because the strongest counterarguments are surfaced before merge

## What Makes This Different

Most AI decision support stays generic. This plugin is opinionated about quality, not process.

- It does not force a workflow.
- It does not install hooks.
- It does not assume it owns planning mode.
- It does not try to replace plugins like Superpowers.
- It does push Claude to make sharper, better-defended calls.

The result should feel like stronger judgment layered into the current workflow, not a new workflow competing with it.

## Included Skills

### `decision-simulator`

Flagship skill for high-signal decision analysis. It defines the decision, goal, options, constraints, tradeoffs, hidden costs, second-order effects, missing context, recommendation, and confidence. It should help both when the user asks for guidance and when Claude needs to pressure-test its own next move.

### `evaluate-tradeoffs`

Compares options across the dimensions that actually matter in context and calls out when one option dominates.

### `detect-context-gaps`

Finds the missing information and hidden assumptions that make a recommendation unreliable.

### `compare-implementation-paths`

Compares practical technical paths to the same goal and explains when each is better.

## Included Agent

### `decision-reviewer`

A lightweight read-oriented subagent for deeper decision analysis when the initial answer needs a stronger recommendation or when Claude reaches a meaningful technical crossroads that depends on repository context.

## Example Prompts

- "Should I keep this caching logic in the frontend or move it to the API?"
- "Should we adopt TanStack Query here or is that overkill?"
- "Is splitting this service out worth it yet, or should we leave it in the monolith?"
- "Compare an incremental refactor versus a rewrite for this auth flow."
- "What am I missing before I decide to use Kafka for this?"
- "Can you pressure-test this architecture choice and tell me the strongest case against it?"
- "Before you do that refactor, check whether the abstraction is actually worth introducing."
- "You seem to be deciding between patching this locally or moving the logic into a shared layer. Compare those paths first."

## Example Output

Short example:

> **Decision:** Move validation from the frontend into the backend API, while keeping lightweight client-side checks for instant feedback.
>
> **Key tradeoff:** Frontend-only validation is faster to ship, but it duplicates rules, weakens security, and makes debugging inconsistent state harder.
>
> **Hidden cost:** If validation stays split informally, rule drift will become a recurring support problem rather than a one-time engineering cost.
>
> **Recommendation:** Centralize authoritative validation in the backend. Keep client checks only for UX. This is slightly slower now but materially cheaper to maintain.
>
> **Confidence:** Medium-high. The main uncertainty is whether any current integrations depend on undocumented frontend-only behavior.

## Compatibility Philosophy

This plugin is intentionally narrow:

- improve decisions
- stay composable
- avoid workflow ownership

That means:

- no mandatory hooks
- no auto-enforced process
- no assumption that the plugin is always active
- no special dependency on any other plugin

When used alongside Superpowers or other planning-oriented plugins, Decision Simulator should make brainstorming, planning, review, and implementation choices sharper without competing for control. It should improve judgment inside the current workflow, including moments where Claude itself is deciding what to do next.

## Limitations

- It cannot replace missing business context, production metrics, or team constraints.
- It will only be as good as the factual inputs available in the conversation or repository.
- It improves decision analysis, not empirical validation. Some questions still require measurement, experiments, or rollout data.
- It does not manage execution after the decision is made.

## Local Testing

From the marketplace repository root:

```bash
claude --plugin-dir ./plugins/decision-simulator
```

Inside Claude Code:

```text
/reload-plugins
/decision-simulator:decision-simulator Should I refactor this service now or leave it alone?
```

## Marketplace Installation

Local marketplace:

```text
/plugin marketplace add ./decision-simulator
/plugin install decision-simulator@decision-simulator
```

GitHub install:

```text
/plugin marketplace add <github-owner>/decision-simulator
/plugin install decision-simulator@decision-simulator
```
