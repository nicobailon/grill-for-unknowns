# grill-for-unknowns

**An agent skill for finding unknowns, grilling plans, and reaching shared understanding before implementation.**

`grill-for-unknowns` combines docs/source-grounded plan interrogation with the “finding your unknowns” strategy: known knowns, known unknowns, unknown knowns, and unknown unknowns.

The intended behavior is simple:

> Inspect the real territory first, grill the plan and user only where decisions matter, surface blindspots, write down the shared understanding, then build.

## What it does

Use this skill when an agent should **not** rush straight into implementation:

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
- and **unknowns** that could materially change implementation.

## Install

The skill is a standard `SKILL.md` folder, so installation is the same everywhere: copy `plugins/grill-for-unknowns` into your agent's skills directory. Clone the repo once:

```bash
git clone https://github.com/nicobailon/grill-for-unknowns.git /tmp/grill-for-unknowns
```

### Claude Code

```bash
mkdir -p ~/.claude/skills
cp -R /tmp/grill-for-unknowns/plugins/grill-for-unknowns ~/.claude/skills/grill-for-unknowns
```

Claude Code discovers it automatically; invoke it as `/grill-for-unknowns` or let it trigger from the description. For a project-scoped install, copy into `.claude/skills/` inside the repo instead.

### Codex

```bash
mkdir -p ~/.agents/skills
cp -R /tmp/grill-for-unknowns/plugins/grill-for-unknowns ~/.agents/skills/grill-for-unknowns
```

Codex picks up skills from `~/.agents/skills` (personal) or `.agents/skills` inside a repo (project-scoped); invoke with `$grill-for-unknowns` or let implicit matching select it from the description. See the [Codex skills docs](https://learn.chatgpt.com/docs/build-skills).

### Hermes Agent

```bash
mkdir -p ~/.hermes/skills/software-development
cp -R /tmp/grill-for-unknowns/plugins/grill-for-unknowns ~/.hermes/skills/software-development/grill-for-unknowns
```

Then start a fresh Hermes session or reload skills, and load it explicitly when needed:

```txt
/skill grill-for-unknowns
```

### Other agents

Any agent that supports `SKILL.md` skill folders can use the same copy-the-folder approach.

## Example prompts

```txt
Use grill-for-unknowns before implementing this. Inspect the relevant docs/source first, then interview me one material question at a time until we have shared understanding.
```

```txt
Grill this plan against the codebase and official docs. Do not build yet. Surface unknown unknowns, challenge domain terms, and produce a launch packet.
```

```txt
I know what I want when I see it. Use grill-for-unknowns to prototype options and extract my unknown knowns before implementation.
```

```txt
Before spawning subagents, use grill-for-unknowns to turn this fuzzy request into a clear launch packet with assumptions, open questions, verification steps, and rollback risks.
```

## Package contents

```txt
grill-for-unknowns/
├── README.md
├── CHANGELOG.md
├── LICENSE
├── NOTICE.md
├── package.json
└── plugins/
    └── grill-for-unknowns/
        ├── SKILL.md
        ├── README.md
        ├── LICENSE
        ├── references/
        │   ├── upstream-lineage.md
        │   └── domain-modeling-add-on.md
        └── templates/
            ├── ADR.md
            ├── CONTEXT.md
            ├── grill-session.md
            ├── implementation-notes.md
            └── launch-packet.md
```

## Credits and lineage

This skill is substantially inspired by, and should be treated as an adaptation/fork of, Matt Pocock’s skill composition around `grill-with-docs`:

- Matt Pocock’s `grill-with-docs` skill:  
  https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md
- Matt Pocock’s `domain-modeling` skill:  
  https://github.com/mattpocock/skills/tree/main/skills/engineering/domain-modeling
- Matt Pocock’s `grilling` skill:  
  https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md

Additional inspiration comes from Thariq’s X article:

- Thariq, “A Field Guide to Fable: Finding Your Unknowns”:  
  https://x.com/trq212/status/2073100352921215386

## How this differs from the upstream skills

The upstream skills are deliberately minimal and compositional: `grilling` is a ~10-line relentless-interview loop, and `grill-with-docs` composes it with `domain-modeling`. `grill-for-unknowns` keeps that core loop and expands it in five ways:

1. **It covers gaps an interview can't see.** The upstream loop only handles known unknowns — questions someone already knows to ask. The four-quadrant taxonomy adds explicit tactics for unknown knowns (the user's unverbalized taste, extracted through cheap prototypes and contrasting references instead of questions the user can't answer) and unknown unknowns (a blindspot pass over docs/source/tests before the interview starts).
2. **It defines what a good question is.** "Relentless" is a personality, not a quality bar. Here every question must be material, grounded in evidence, and answerable, using a template that forces a citation, a why-it-matters, and a recommended default. Low-risk unknowns become labeled assumptions instead of questions at all.
3. **It is self-contained.** `grill-with-docs` silently depends on two other skills being installed; an agent that loads only the headline file gets almost nothing. This skill inlines the grilling loop and the domain-modeling rules, so it works dropped into any agent — Hermes, Claude Code, or Codex.
4. **It extends past the planning boundary.** Upstream ends at "don't build until shared understanding is confirmed." This skill covers what happens after alignment: a launch packet for subagents and long-running coding agents, a deviation policy (continue-and-log vs. stop-and-ask), implementation notes for newly discovered unknowns, and a post-implementation explainer/quiz.
5. **It produces durable artifacts.** Session ledgers, launch packets, implementation notes, `CONTEXT.md`, and ADRs give the shared understanding a reusable written shape instead of leaving it in the chat transcript.

## License

MIT. See [`LICENSE`](LICENSE).

The upstream `mattpocock/skills` repository is MIT licensed with copyright held by Matt Pocock. Because this skill is an adaptation/fork rather than a totally original work, the license preserves Matt Pocock’s MIT copyright notice and adds Nico Bailon’s copyright notice for this adaptation.
