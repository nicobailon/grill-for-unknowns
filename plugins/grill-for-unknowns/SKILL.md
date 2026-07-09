---
name: grill-for-unknowns
description: Use when starting or reviewing a complex implementation where the user wants an agent to interrogate the plan against docs/source evidence, surface unknown unknowns, and avoid rushing into build mode. Combines docs-grounded grilling with a map-vs-territory unknowns pass.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [planning, docs, unknowns, agentic-coding, interviews, verification]
    related_skills: [writing-plans, software-development-workflow, subagent-driven-development]
---

# Docs + Unknowns Grill

## Overview

Use this skill before a complex/long-running implementation, during a stuck implementation, or before merge/review when the plan may be based on an incomplete map of the territory.

The core idea is:

- **The map** = the prompt, plan, assumptions, skills, prior context, docs excerpts, and the agent's current mental model.
- **The territory** = the real codebase, product constraints, APIs, docs, user taste, deployment environment, and failure modes.
- **Unknowns** = the gap between the map and the territory.

This skill combines four behaviors:

1. **Grill me with docs** — ground the conversation in official docs, source code, schemas, tests, examples, and existing project conventions; then challenge the user's plan against that evidence.
2. **Relentless grilling** — walk the design tree one material decision at a time until the user and agent share the same understanding. Facts are looked up; decisions are put to the user with a recommended answer.
3. **Domain modeling** — sharpen the project's vocabulary as the design evolves. Challenge fuzzy/overloaded terms, update `CONTEXT.md` when language crystallizes, and record ADRs only for durable, surprising trade-off decisions.
4. **Finding your unknowns** — explicitly classify known knowns, known unknowns, unknown knowns, and unknown unknowns; then use blindspot passes, prototypes, references, interviews, implementation notes, explainers, and quizzes to reduce the expensive ones before building too much.

The goal is not to ask endless questions. The goal is to discover the few answers that would materially change architecture, scope, UX, data model, safety, permissions, domain language, or implementation strategy — and to write down the shared understanding as it forms.

## Lineage and Supporting Files

This skill is a Hermes adaptation of Matt Pocock's `grill-with-docs` composition (`grilling` + `domain-modeling`) plus Thariq's "Finding Your Unknowns" strategy.

Supporting files:

- `README.md` — attribution, lineage, and high-level usage notes.
- `LICENSE` — MIT license preserving upstream Matt Pocock copyright plus Nico Bailon's adaptation copyright.
- `references/upstream-lineage.md` — source-skill lineage and how the concepts compose.
- `references/domain-modeling-add-on.md` — `CONTEXT.md` and ADR rules adapted from `domain-modeling`.
- `templates/grill-session.md` — working document for a full grill/unknowns session.
- `templates/CONTEXT.md` — lightweight glossary template.
- `templates/ADR.md` — minimal ADR template.

## When to Use

Use when:

- The user says not to rush implementation, asks for a stronger plan, or wants to use a Fable-style planning/orchestration pass.
- The task depends on unfamiliar docs, APIs, libraries, platform behavior, or source conventions.
- The user has a vague product/design desire and likely has **unknown knowns**: they will know good/bad when they see it, but cannot fully specify it upfront.
- The agent is about to spawn subagents or a long-running coding agent and needs a better launch packet.
- A previous attempt failed because the agent made assumptions, overfit to generic best practices, or missed real codebase constraints.
- Reviewing a plan/spec/PR where you need to pressure-test hidden assumptions before merge.

Do **not** use when:

- The task is trivial, mechanical, or already has unambiguous acceptance criteria.
- The user explicitly wants immediate execution and the risk of wrong assumptions is low.
- You can verify the right answer directly with a single tool call and no interview is needed.

## Operating Mode

Stay in **Explore** or **Plan** mode until the unknowns that could change the implementation are resolved or explicitly accepted as assumptions.

Default sequence:

1. **Restate the map** — summarize the user's request, the intended outcome, and what is already known.
2. **Read the territory** — inspect the relevant docs/source/tests/config before grilling. Do not rely on vibes if docs or code are available.
3. **Open a grill session ledger** — use `templates/grill-session.md` when the session is complex enough to need a durable working doc.
4. **Build the unknowns ledger** — classify known knowns, known unknowns, unknown knowns, and suspected unknown unknowns.
5. **Build the domain ledger** — identify fuzzy terms, overloaded concepts, vocabulary conflicts, and context boundaries. Use `references/domain-modeling-add-on.md` for `CONTEXT.md` / ADR rules.
6. **Grill one decision at a time** — ask only the next high-leverage question, cite docs/source/code when relevant, and provide the recommended answer.
7. **Propose defaults** — for low-risk unknowns, choose a sensible default and label it as an assumption instead of blocking.
8. **Persist shared understanding** — update `CONTEXT.md` for crystallized domain terms and offer ADRs only for durable, surprising trade-off decisions.
9. **Create or revise the plan** — make the implementation plan focus on decisions likely to change, not mechanical steps.
10. **Ask for confirmation before build** — do not enact the plan until the user confirms shared understanding, unless they explicitly authorize proceeding with labeled assumptions.
11. **During implementation** — keep implementation notes for deviations and newly discovered unknowns.
12. **Post implementation** — produce an explainer and quiz/review checklist so the user understands what changed.

## Unknowns Taxonomy

Use this table explicitly in the output when the task is ambiguous enough to justify it.

| Type | Meaning | How to expose it | Example |
| --- | --- | --- | --- |
| Known knowns | Requirements already stated or proven by docs/source | Restate and cite | "Use Stripe Connect; webhook endpoint already exists." |
| Known unknowns | The user/agent knows a decision is unresolved | Ask targeted questions or choose labeled defaults | "Should refunds sync one-way or two-way?" |
| Unknown knowns | The user would recognize the right result when shown, but has not verbalized the criterion | Prototype, sketches, examples, references | "This dashboard feels too enterprise; make it more operator-like." |
| Unknown unknowns | Constraints or possibilities nobody has considered yet | Blindspot pass over docs/source/tests/internet; ask experts; search prior art | "The API rate limit makes this sync architecture impossible." |

## Docs-Grounded Grill Procedure

### 1. Gather evidence first

Before asking the user to decide, inspect available ground truth:

- Official docs for libraries/platforms/APIs.
- Local source files, routes, models, schemas, migrations, tests, and config.
- Existing project conventions and similar implementations.
- Error logs, CI failures, issue comments, PR diffs, or previous implementation notes.
- Reference implementations the user points to, even if in another language.

If the user provides docs, use them. If docs are missing but retrievable, fetch them. If docs cannot be accessed, say that and mark the claim as unverified.

### 2. Convert evidence into pressure-test questions

Good grill questions have all three properties:

- **Material** — the answer could change architecture, scope, UX, data model, security, permissions, or acceptance criteria.
- **Grounded** — the question points to docs/source behavior or a concrete uncertainty, not generic preference fishing.
- **Answerable** — the user can choose from options, approve a default, or supply a reference.

Bad grill questions:

- Obvious preferences that a competent agent can default.
- Exhaustive questionnaires before any research.
- Asking the user to answer things the code/docs can answer.
- Open-ended "anything else?" questions with no context.

### 3. Ask one material question at a time when blocked

If an answer is required to proceed, ask one question, explain why it matters, and give a recommended default. This is the imported `grilling` behavior: walk the design tree branch-by-branch, but do not dump the whole tree on the user at once.

Template:

```md
Blocking question: <question>
Why it matters: <what changes if answer A vs B>
Evidence: <doc/source/test/reference citation>
Recommended answer: <default + rationale>
If you don't care: I'll proceed with <default>.
```

If multiple questions are useful but not blocking, keep them in the grill queue and ask the next unresolved material decision first. Proceed with labeled assumptions only when the user has authorized that mode.

### 4. Separate facts from decisions

- **Facts**: inspect docs/source/tests/config/logs. Do not ask the user to answer what the repo can answer.
- **Decisions**: put them to the user, with a recommended answer.
- **Domain language**: if terms are fuzzy or conflicting, challenge them immediately and capture the canonical term when resolved.

## Domain Modeling: Shared Language and ADRs

The original `grill-with-docs` skill depends on `domain-modeling`, so this skill must not only ask questions — it must also maintain shared language.

Use `references/domain-modeling-add-on.md` for details.

During the grill:

- Challenge fuzzy or overloaded terms immediately.
- Compare the user's terms against existing `CONTEXT.md`, code identifiers, docs, and product copy.
- Ask whether conflicting terms represent distinct domain concepts or accidental synonyms.
- When a term is resolved, update `CONTEXT.md` immediately if working inside a repo and the user has authorized file edits.
- Keep `CONTEXT.md` pure: glossary only, no implementation plan, no scratchpad, no ADR content.

Offer an ADR only when the decision is:

1. hard to reverse,
2. surprising without context, and
3. the result of a real trade-off.

If any of those are false, record the decision in the session/implementation notes instead of creating an ADR.

## Finding Unknown Unknowns: Blindspot Pass

Run a blindspot pass when the user is entering an unfamiliar domain, unfamiliar part of the codebase, or high-stakes integration.

Prompt pattern:

```md
Do a blindspot pass before implementation. Search the relevant docs/source/tests and identify unknown unknowns that could materially change the plan. Explain them in plain language, rank by implementation risk, and suggest how to resolve each one cheaply.
```

Output shape:

```md
## Blindspot Pass

### Highest-risk unknown unknowns
1. <unknown>
   - Why it matters:
   - Evidence:
   - Cheap resolution:
   - Decision owner: user / agent / docs / prototype

### Likely safe assumptions
- <assumption> — why safe, how to verify later

### Questions worth asking now
1. <one material question>
```

## Unknown Knowns: Brainstorms, Prototypes, and References

When the user will recognize the right answer visually or behaviorally but cannot fully specify it:

- Build cheap prototypes before wiring real systems.
- Offer multiple directions with meaningful contrast, not tiny variations.
- Ask the user to react to examples, screenshots, demos, or reference source.
- Capture the user's reactions as explicit criteria.

Examples:

```md
Before touching the real app, create a single-file mock with fake data showing 3 distinct UX directions. Use it to extract the user's unknown knowns: density, tone, hierarchy, workflow, and what feels wrong.
```

```md
Find 2-3 similar modules in this repo and 1 external reference implementation. Compare patterns, then ask which behavior should match our target.
```

## Implementation Plan Requirements

When producing the plan, lead with the decisions most likely to change:

1. **Decision surface** — data model, type interfaces, permissions, user-facing flows, API semantics, migration strategy.
2. **Evidence** — docs/source references that justify the plan.
3. **Open questions** — only material unknowns, ranked by risk.
4. **Resolved assumptions** — low-risk defaults the agent will use unless corrected.
5. **Prototype/reference artifacts** — links or paths if relevant.
6. **Implementation steps** — bite-sized, ordered, with verification gates.
7. **Deviation policy** — what the implementer should do if the territory contradicts the map.

Do not bury critical architecture decisions under mechanical refactoring steps.

## During Implementation: Notes and Deviations

For complex work, create a temporary implementation notes file such as `implementation-notes.md` or include an equivalent section in the final report.

Minimum sections:

```md
# Implementation Notes

## Plan Snapshot
- <short plan/date/session>

## Decisions Made
- <decision> — reason/evidence

## Deviations
- Planned: <original>
- Actual: <change>
- Why: <constraint discovered>
- Risk: <low/med/high>

## New Unknowns
- <unknown> — resolved by / deferred to / needs user

## Verification
- <command/test/manual check> — result
```

Default deviation policy:

- If the issue is low-risk and local, choose the conservative option, log it, and continue.
- If the issue changes architecture, data migration, security, cost, or user-facing behavior, stop and ask.
- If docs contradict the plan, trust the docs/source over the original map and update the plan.

## Post-Implementation: Explain, Pitch, Quiz

After implementation, help the user and reviewers understand the territory discovered during the work.

Deliver:

- What changed and why.
- Which unknowns were resolved.
- Which assumptions remain.
- Docs/source evidence for important behavior.
- Verification results from real commands/tests.
- A short quiz/checklist if the user needs to understand before merge.

Quiz prompt pattern:

```md
I want to make sure I understand this change before merging. Give me a concise report with context, what changed, why it changed, verification results, remaining assumptions, and a quiz at the bottom. I should be able to answer every quiz item from the report.
```

## Subagent / Coding-Agent Launch Packet

Before spawning a subagent or external coding agent, prepare a launch packet:

```md
## Goal
<what to build>

## Map
<current plan/spec/prototype>

## Territory to inspect first
- <docs URLs>
- <source paths>
- <tests/config>
- <reference implementations>

## Known knowns
- <facts>

## Known unknowns
- <questions and chosen defaults>

## Suspected unknown unknowns
- <blindspots to check before editing>

## Unknown knowns to preserve
- <taste/product criteria extracted from prototypes/references>

## Deviation policy
- Continue if:
- Stop and ask if:
- Log deviations in:

## Verification gates
- <commands/tests/manual checks>
```

If using multiple subagents, split roles:

- **Docs scout** — reads official docs/source and returns constraints.
- **Codebase scout** — maps existing patterns and tests.
- **Prototype scout** — creates cheap visual/API alternatives to expose unknown knowns.
- **Implementer** — edits only after the plan is stable enough.
- **Reviewer** — grills the diff against the launch packet and docs.

## Example Prompts

### Blindspot pass

```md
I'm about to implement <feature> in an unfamiliar part of the codebase. Do a docs-grounded blindspot pass first: inspect the relevant source/tests/docs, classify known knowns / known unknowns / unknown knowns / unknown unknowns, then ask only the questions where my answer would materially change the implementation.
```

### Grill my plan with docs

```md
Grill this implementation plan against the official docs and our existing source conventions. Don't start building. Find assumptions, contradictions, missing edge cases, and decisions that are likely to change. For each issue, cite the doc/source evidence and recommend a default.
```

### Interview one question at a time

```md
Interview me one question at a time about the unknowns in this feature. Prioritize questions where my answer would change architecture, permissions, data model, or user-facing workflow. Use docs/source evidence when asking, and propose a default each time.
```

### Prototype to find unknown knowns

```md
I know what I'll like when I see it, but I can't specify it yet. Before wiring real code, make 3 cheap prototypes with fake data that differ meaningfully in workflow and visual hierarchy. Then extract my reactions into explicit product criteria.
```

### Implementation notes

```md
Keep implementation-notes.md. If you hit an edge case or docs/source constraint that forces you to deviate from the plan, choose the conservative option when safe, log it under Deviations, and stop to ask only if it changes architecture, security, migration, cost, or user-facing behavior.
```

## Common Pitfalls

1. **Grilling before reading.** Do not ask the user to answer questions that docs/source/tests can answer. Inspect the territory first.

2. **Missing upstream composition.** If adapting another skill, article, framework, or repo workflow, do not stop at the obvious headline behavior. Inspect its linked/referenced skills, support files, and dependency chain before declaring the new skill complete. `grill-with-docs` looked tiny, but its real behavior came from `grilling` + `domain-modeling`.

3. **Accepting the first draft as final.** Treat a newly authored skill as a first pass. Re-read it against the source material and ask what dependency, support file, template, or class-level behavior is missing before calling it done.

4. **Attribution/license drift.** If this skill is forked or adapted from another public skill/article/repo, keep attribution in `README.md` and preserve upstream license/copyright notices in `LICENSE`. Do not replace upstream copyright with only the local adapter's name.

5. **Question spam.** The point is fewer, better questions. Ask what changes the build, not every preference.

6. **Treating plans as truth.** A plan is a map. If implementation discovers conflicting territory, update the map.

7. **Over-constraining the agent.** Too-specific instructions can prevent useful pivots. Define the goal, constraints, and stop/continue rules; leave room for local implementation judgment.

8. **Under-constraining the agent.** Vague prompts cause generic best-practice choices that may not fit the product/codebase. Provide references, docs, taste examples, and acceptance criteria.

9. **Ignoring unknown knowns.** If the user has taste/product intuition they cannot verbalize, use prototypes and references before implementation becomes expensive to change.

10. **No deviation log.** Long-running agents silently make choices. Require implementation notes so the next iteration learns from the territory.

11. **Skipping post-implementation understanding.** A passing test suite is not the same as user understanding. Produce an explainer and, when appropriate, a quiz/checklist before merge.

## Verification Checklist

Before moving from planning to implementation:

- [ ] Relevant docs/source/tests/config were inspected, or the lack of access is stated.
- [ ] If adapting an external skill/framework/article, linked skills, referenced support files, dependency chains, README attribution, and license/copyright requirements were inspected — not just the headline artifact.
- [ ] Known knowns, known unknowns, unknown knowns, and suspected unknown unknowns are listed.
- [ ] Blocking questions are material and include recommended defaults.
- [ ] Low-risk unknowns are converted into labeled assumptions rather than blocking progress.
- [ ] The plan leads with likely-to-change decisions, not mechanical steps.
- [ ] Deviation policy is explicit for long-running/subagent work.
- [ ] Verification gates are defined before implementation begins.

Before finalizing implementation:

- [ ] Deviations and newly discovered unknowns were logged.
- [ ] Tests/checks/manual verification were actually run and reported.
- [ ] Remaining assumptions are visible.
- [ ] The user/reviewer gets an explainer sufficient to understand the change.
