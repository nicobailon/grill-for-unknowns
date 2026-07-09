# grill-for-unknowns

`grill-for-unknowns` is a Hermes skill for getting an agent and user to a shared understanding before complex implementation work begins.

It combines:

- docs/source-grounded plan interrogation,
- one-question-at-a-time design grilling,
- domain language / ADR capture,
- and the “finding your unknowns” strategy: known knowns, known unknowns, unknown knowns, and unknown unknowns.

The intended behavior is simple:

> Inspect the real territory first, grill the plan and user only where decisions matter, surface blindspots, write down the shared understanding, then build.

## Why this should help GPT-style models too

This skill is not Claude/Fable-specific magic. It is model-agnostic prompting hygiene: proactively extract the missing context from the user, docs, source, tests, and domain language **before** asking the model to execute.

A useful working hypothesis for GPT 5.5/5.6-style models:

- They are strong at precise execution, planning, tool use, and consistency once the spec is clear.
- They may be more literal and execution-focused than Claude/Fable-style models.
- When critical context is missing, they can produce work that is technically correct to the prompt but wrong for the user's real intent.
- Therefore, front-loading unknown discovery and shared-understanding work should improve them a lot: it turns implicit taste, hidden constraints, and underspecified decisions into explicit instructions.

So this skill deliberately slows the model down before implementation in order to make the eventual build faster and more correct.

## What this skill is for

Use it when an agent should **not** rush straight into implementation:

- unfamiliar codebase area,
- unfamiliar API/library/platform docs,
- vague product/design direction,
- long-running subagent or coding-agent launch,
- complex architecture or domain-model decision,
- failed previous attempt due to assumptions,
- or any plan that needs to be pressure-tested before building.

The skill asks the agent to separate:

- **facts** it can discover from docs/source/tests/config,
- **decisions** it needs the user to make,
- **domain language** that should be clarified and recorded,
- and **unknowns** that could materially change the implementation.

## Credits and lineage

This skill is substantially inspired by, and should be treated as a Hermes adaptation/fork of, Matt Pocock’s skill composition around `grill-with-docs`:

- Matt Pocock’s `grill-with-docs` skill:  
  https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md
- Matt Pocock’s `domain-modeling` skill:  
  https://github.com/mattpocock/skills/tree/main/skills/engineering/domain-modeling
- Matt Pocock’s `grilling` skill:  
  https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md

The core `grill-with-docs` idea is:

> run a grilling session, using domain modeling, so a plan/design is sharpened while shared language and durable decisions are captured.

This skill keeps that lineage and adapts it for Hermes skill folders, templates, implementation launch packets, subagent workflows, and map-vs-territory unknown discovery.

Additional inspiration comes from Thariq’s X article:

- Thariq, “A Field Guide to Fable: Finding Your Unknowns”:  
  https://x.com/trq212/status/2073100352921215386

That article frames agentic coding quality as finding the gap between:

- **the map** — prompt, plan, assumptions, context, skills, and docs snippets,
- and **the territory** — real codebase, docs, tests, APIs, product constraints, deployment constraints, and user taste.

It introduces the useful working taxonomy:

- **Known knowns** — facts already stated or verified.
- **Known unknowns** — unresolved decisions you know are unresolved.
- **Unknown knowns** — taste or intuition the user recognizes only when shown examples/prototypes.
- **Unknown unknowns** — hidden constraints, opportunities, or failure modes nobody has considered yet.

## How this differs from the upstream skills

The upstream skills are intentionally small and compositional. This Hermes skill expands the composition into a more operational workflow:

1. Inspect docs/source/tests before asking factual questions.
2. Maintain a session ledger for map, territory, unknowns, and domain language.
3. Ask one material question at a time, with a recommended answer.
4. Capture canonical terms in `CONTEXT.md` when appropriate.
5. Capture durable decisions as ADRs only when they are hard to reverse, surprising without context, and based on a real trade-off.
6. Produce a subagent/coding-agent launch packet only after shared understanding is reached.
7. Keep implementation notes for deviations and newly discovered unknowns.
8. End with an explainer/checklist/quiz when the user needs to understand the change before merge.

## Folder contents

```txt
grill-for-unknowns/
├── SKILL.md
├── README.md
├── references/
│   ├── upstream-lineage.md
│   └── domain-modeling-add-on.md
└── templates/
    ├── ADR.md
    ├── CONTEXT.md
    └── grill-session.md
```

## License and attribution note

This local Hermes skill is a derivative/adaptation inspired by the linked public skills and article. Keep this README with the skill so future edits preserve attribution to Matt Pocock’s original skill design and Thariq’s “Finding Your Unknowns” article.

The upstream `mattpocock/skills` repository is MIT licensed with copyright held by Matt Pocock. Because this skill is an adaptation/fork rather than a totally original work, the local `LICENSE` file preserves Matt Pocock’s MIT copyright notice and adds Nico Bailon’s 2026 copyright notice for the Hermes adaptation.
