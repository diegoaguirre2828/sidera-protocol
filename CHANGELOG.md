# Changelog

All notable changes to `.sidera` / sidera-protocol will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Schema `version` field (inside the `.sidera` file) is an integer and tracks breaking schema changes only. The semver below tracks the spec repository itself.

---

## [1.1.1] — 2026-04-19 — Academic lineage citations

### Added
- `SPEC.md` §Lineage/Academic — cites nine works that frame the research conversation `.sidera` participates in:
  - Brcic (2025) — *The Memory Wars: Cognitive Sovereignty* (arXiv:2508.05867)
  - Menon (2026) — *Persistent Identity in AI Agents* (arXiv:2604.09588)
  - Annoni et al. (2026) — *Personalised LLMs and the digital twin metaphor* (AI & Society)
  - De Freitas et al. (2024) — *Replika Identity Discontinuity* (arXiv:2412.14190)
  - Kirk et al. (2025) — *Socioaffective alignment* (arXiv:2502.02528)
  - Zhang et al. (2025) — *Memory in the Age of AI Agents* (arXiv:2512.13564)
  - Zhou et al. (2026) — *Externalization in LLM Agents* (arXiv:2604.08224)
  - Kutterer (2024) — *AI companion data portability rights* (MIAI Grenoble)
  - Brown et al. (2024) — *Māori Algorithmic Sovereignty* (Data Science Journal)

### No schema change
This revision adds citations only. `version: 1` files remain valid.

---

## [1.1.0] — 2026-04-19 — Māori framework absorption

### Added
- **`manaLedger`** (optional top-level field) — accumulated weight of work shipped, commitments kept, soul-locks held, magic moments. Inspired by the Māori concept of `mana` (dignity + relational authority + earned-weight as one integrated concept). First `.sidera` field that tracks what the shepherd has *done*, not just who they are.
- **`mauriState`** (optional top-level field) — rolling life-force signal (depleted / steady / thriving) inferred from humanity-signal density, stop-word frequency, drift patterns. Inspired by Māori `mauri` / `mauri ora`.
- **SPEC.md §Lineage** — explicit attribution to AIHOA (Ako Aotearoa) + Te Mana Raraunga data-sovereignty tradition. Adoption is acknowledgment-with-attribution, not appropriation.

### Why this ships now
Wave-3 of the multilingual competitor scan (see `project_ai_landscape_wave3_maori_vocabulary_20260419.md` in the Sidera app memory) surfaced AIHOA — a Māori-framework tool that decomposes "soul" into four concepts (`wairua`, `mana`, `whanaungatanga`, `mauri ora`) where English collapses them to one. Sidera's schema gets richer by importing the sharper vocabulary.

### Non-breaking commitment
Schema `version` stays at `1`. Readers that don't understand `manaLedger` or `mauriState` MUST ignore them gracefully (no errors, no warnings). The 90-day no-breaking-change commitment from v1.0.0 holds.

### Preview of future adoption
`wairua` (identity across contexts) and `whanaungatanga` (relational fabric) are tracked as candidate future fields. They will ship when they prove load-bearing work beyond what existing baseline + pack-journal fields already carry. No guesswork additions.

---

## [1.0.0] — 2026-04-19 — First public release

### Context
Shipped the same week Anthropic released [Claude Memory Import](https://claude.com/import-memory), a prompt-based tool for moving AI memory between ChatGPT / Gemini / Grok / Claude. Claude Memory Import is excellent for onboarding but ships no canonical file format, no schema, no validation, and belongs to one lab. `.sidera` v1.0.0 is the neutral third-party format it lacks.

### Added
- **Top-level README** positioning against Claude Memory Import — "use Memory Import to onboard, use `.sidera` to keep."
- **`CHANGELOG.md`** (this file).
- **`SPEC.md`** — full specification with design rationale (previously shipped at commit `acfc407`, now referenced as v1.0.0 baseline).
- **`schemas/soul-doc.schema.json`** — JSON Schema validator for `.sidera`.
- **`schemas/humanity-signal.schema.json`** — JSON Schema validator for humanity-signal events.
- **Human-readable schema summaries** (`schemas/soul-doc.md`, `schemas/humanity-signal.md`).
- **Minimal example** (`examples/minimal.sidera`).
- **Privacy invariants** as constitutional commitments (no training, user-owned, local-first, consent-per-kind).
- **Compatibility notes** for Claude Memory Import, PAM, soul.md, Mem0/Letta/Zep.

### Schema version
Soul doc schema `version: 1`. This is the first stable `version` value; future bumps follow semver on breaking changes to the schema shape only.

### Stability commitment
No breaking changes to schema `version: 1` for 90 days from this release without prior announcement on a `v2-draft` branch.

---

## [0.1.0] — 2026-04-17 — Initial draft

### Added
- Initial schema spec shipped inside the [Sidera](https://github.com/diegoaguirre2828/sidera) reference application at commit `764d8bf`, then extracted into this public repository.
- Layer-3 pack-journal reframe (commit `acfc407`): `.sidera` is the partnership between shepherd + sheepdog, not a portable-you digital twin. Added `aiProfile`, `graveyard`, `sideraVoice`, `branches`, `packScope`, `DecayMetadata`.
