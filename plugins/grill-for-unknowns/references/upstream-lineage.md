# Upstream Lineage: Grill with Docs + Finding Unknowns

This skill adapts three upstream Matt Pocock skills plus Thariq's "Finding Your Unknowns" article into a Hermes workflow.

## Source skills

- `grill-with-docs`: https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md
  - Minimal composition skill: run a `/grilling` session while using `/domain-modeling`.
  - Important implication: the real behavior comes from combining relentless interview + domain-model maintenance.
- `grilling`: https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md
  - Interview relentlessly about every aspect of a plan/design until shared understanding is reached.
  - Walk the design tree branch by branch.
  - Ask one question at a time.
  - Provide a recommended answer for each question.
  - Look up facts in the codebase; decisions belong to the user.
  - Do not enact the plan until shared understanding is confirmed.
- `domain-modeling`: https://github.com/mattpocock/skills/tree/main/skills/engineering/domain-modeling
  - Build/sharpen domain terminology as design proceeds.
  - Challenge fuzzy or conflicting language immediately.
  - Cross-reference claims against code.
  - Update `CONTEXT.md` inline when domain terms crystallize.
  - Offer ADRs only for decisions that are hard to reverse, surprising without context, and the result of a real trade-off.

## Article strategy being added

Thariq's article frames agentic coding quality as discovering the gap between:

- **Map**: prompt, plan, assumptions, skills, docs snippets, current agent mental model.
- **Territory**: real codebase, APIs, product/domain constraints, deployment environment, tests, user taste, reviewer expectations.

That gap is classified as:

- Known knowns
- Known unknowns
- Unknown knowns
- Unknown unknowns

The article's concrete tactics are: blindspot passes, brainstorming/prototypes, one-question-at-a-time interviews, references/source code as specs, implementation plans, implementation notes, explainers, and quizzes.

## Hermes adaptation

`grill-for-unknowns` should therefore behave as a composition skill:

1. Run the grilling interview loop from `grilling`.
2. Keep a domain model / shared language ledger from `domain-modeling`.
3. Ground factual claims in docs/source/tests before asking the user.
4. Use the known/unknown taxonomy to find hidden assumptions before implementation.
5. Persist crystallized terminology to `CONTEXT.md` and major trade-off decisions to ADRs when appropriate.
6. Do not build until the user confirms shared understanding, unless the user explicitly asks to proceed with labeled assumptions.
