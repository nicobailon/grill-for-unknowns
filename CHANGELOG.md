# Changelog

## 0.1.2

Anti-exhaustion release — addresses the failure mode where "grilling" primes an agent to over-question to the point of user exhaustion. The exhaustion controls now live in the skill instead of relying on user discipline.

- Defined grilling as few, evidence-priced questions and explicitly licensed *not* asking about non-material topics.
- Made the stop condition explicit: the grill ends when the unknowns ledger is empty, with remaining-count announcements as it shrinks.
- Added a default budget of ~5 blocking questions per session; exceeding it requires user opt-in.
- Added a fatigue valve: short or impatient answers switch the agent to defaults mode — remaining unknowns become one batched assumptions list for veto.
- Added a batched endgame: residual low-risk unknowns are presented as a single assumptions list instead of serial questions.

## 0.1.1

Token-efficiency release — three duplication/no-op sweeps of `SKILL.md`, prompted by [@mattpocock](https://github.com/mattpocock)'s feedback that the skill was long, costly in tokens, and hard to review. Thanks Matt.

- Trimmed `SKILL.md` by ~40% — from ~4.7k to ~2.8k tokens (341 → 230 lines) — with all strategy elements from the upstream skills and Thariq's article preserved.
- Removed skill-authoring guidance that had leaked into runtime instructions (preserved as maintainer notes in `references/upstream-lineage.md`).
- Moved implementation-notes and launch-packet boilerplate into `templates/`.
- Deduplicated `CONTEXT.md`/ADR formats (canonical in `templates/`, referenced from the domain-modeling add-on) and the launch packet (canonical in `templates/launch-packet.md`).
- Added Claude Code and Codex install instructions; de-Hermes'd adaptation wording.
- Added a five-point upstream comparison to the READMEs.

## 0.1.0

Initial public release.

- Added `grill-for-unknowns` skill.
- Added docs/source-grounded grilling workflow.
- Added known/unknown taxonomy.
- Added domain-modeling add-on.
- Added templates for grill sessions, `CONTEXT.md`, and ADRs.
- Added README attribution to Matt Pocock and Thariq.
