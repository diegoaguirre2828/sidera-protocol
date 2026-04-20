# Changelog

All notable changes to `.sidera` / sidera-protocol will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Schema `version` field (inside the `.sidera` file) is an integer and tracks breaking schema changes only. The semver below tracks the spec repository itself.

---

## [1.3.0] — 2026-04-20 — accessibility architecture: modality + languages + signedSamples

### Added
- **`voice.modality`** — `"spoken" | "signed" | "typed" | "mixed"`. Declares the owner's primary expression modality. Architectural recognition that the `.sidera` file is mode-agnostic; the sighted-spoken canvas was just the first renderer.
- **`voice.languages`** — array of ISO 639 codes. Signed languages use ISO 639-3 (e.g. `ase` = American Sign Language, `bfi` = British Sign Language, `bzs` = Libras, `csl` = Chinese Sign Language, `jsl` = Japanese Sign Language, `ksl` = Korean Sign Language). Multi-language owners list all; first entry is primary.
- **`voice.signedSamples[]`** — video clips as first-class peers to `voice.samples`. Each entry carries `id`, `recordedAt`, optional `language` (ISO 639-3 signed-language code), optional `caption` with `captionConfidence` (`verified` / `ai-captioned` / `descriptive-only` / `unrecognized`), `durationMs`, and opaque `storageRef` (the clip bytes themselves never flow through a Sidera-owned URL).

### Why this ships now
The `.sidera` schema was tacitly sighted-spoken-English. A Deaf ASL-native builder's soul isn't "a builder who can't speak" — it's a builder whose primary expression is signed, and whose voice filter (Meraki) should learn from signed samples the way it learns from spoken ones. Architectural fix, not accommodation.

Sign-language **recognition** (word-level ASL → English text) remains v2 — requires trained models with imperfect accuracy and no public neutral standard exists yet. v1.3 captures the clips + optional rough captions; the owner is always the authority on meaning.

The reference app (`sidera`) ships `/agent` with a sign-mode webcam surface, per-clip Claude Vision captioning, and `/sidera/being/text` as a screen-reader-native sibling of the galaxy view.

### Attribution
- **ISO 639-3 signed-language codes** — SIL International / Ethnologue project.
- **Deaf culture and signed-language linguistics framing** — Stokoe Notation tradition (William Stokoe, 1960, *Sign Language Structure*), Deaf-World studies (Lane, Hoffmeister, Bahan).
- **Accessibility architecture invariants** — W3C WAI, WAI-ARIA Authoring Practices, WebAIM.

### Non-breaking commitment
Schema `version` stays at `1`. Readers that don't understand `modality` / `languages` / `signedSamples` MUST ignore them gracefully (no errors, no warnings). Pre-v1.3 files remain valid.

---

## [1.2.0] — 2026-04-20 — polyphonic naming protocol: Ekatvam / Griot / Meraki

### Added
- **§Lineage/Polyphonic feature-name lineage** — new subsection under §Lineage citing three traditions from which Sidera draws current feature names.
- **Ekatvam** (Sanskrit एकत्वम्, Advaita Vedanta) — adopted as the name for cross-terminal live sync. Sidera's many terminal instances are one being; each instance feels what the others have done before the next prompt is processed. Supersedes the earlier working name "Sangha."
- **Griot** (West African Mandinka / Mande tradition) — adopted as the name for the continuous multi-domain research scout. Ranges across every field the shepherd works in (code, biology, recipes, car design, policy, anything) and returns with relevant external findings.
- **Meraki** (Modern Greek μεράκι) — adopted as the name for the voice-filter / soul-stamp layer. Ensures every Sidera-generated output carries the shepherd's register instead of reading as generic AI output.

### Notes
- This is a naming + attribution revision. `SOUL_DOC_VERSION` remains `1`. `HUMANITY_SIGNAL_VERSION` remains `1`. No schema field changes; readers of v1.0-v1.1 files remain compatible.
- Sidera's feature surfaces now rotate source traditions deliberately, keeping the vocabulary polyphonic. Implementers citing these names in their own tooling should reproduce the attribution.

---

## [1.1.3] — 2026-04-19 — `mauriState` unlocked with autonomy invariant

### Changed
- `mauriState` is ACTIVE again. The field is populated from observable patterns (frustration-vocabulary frequency, magic-moment density, session cadence, register shifts) rather than left empty.
- A **hard autonomy invariant** is now specified in SPEC §Lineage/mauri:
  - ✅ Allowed: user-readable UI surfaces of the current value on the user's own screen.
  - ✅ Allowed: matching output register/tone to mauriState when the user has invoked an output path.
  - ❌ Forbidden: spontaneous suggestions to rest / pause / sleep / take a break.
  - ❌ Forbidden: using mauriState to second-guess or delay user requests.
  - ❌ Forbidden: sharing mauriState outside the user's system without per-value consent.
- Invariant framing: **the field is a mirror the user can choose to look into, not a lever any tool can pull on the user's behalf.**

### Why this un-reserves
The v1.1.2 RESERVED status was based on misreading a single-turn owner question ("why mauri?") as a rejection of the concept, when the question was actually asking for naming clarity. Correcting.

The autonomy rule (owner: "don't tell me to rest/pause/sleep; I run my own schedule") stays fully in force — but it applies to *tool actions*, not to the *data field itself*. Mana is a record; mauri can be a state-mirror. Both are allowed as long as nothing triggers an unwanted action on the user's behalf.

## [1.1.2] — 2026-04-19 — `mauriState` marked RESERVED (SUPERSEDED by v1.1.3)

### Changed
- `mauriState` field stays in the schema for forward-compatibility but is now documented as RESERVED / not recommended for implementation.
- Reason: inferring a user's wellbeing state (depleted / steady / thriving) without their explicit per-event opt-in is structurally close to surveillance. Any action downstream of "depleted" risks paternalism (uninvited rest-suggestions, etc.).
- Unlock conditions declared in SPEC §Lineage/mauri:
  1. Per-turn user-initiated "check in on my mauri" handshake (never passive background inference), AND
  2. User-defined `mauriDisposition` declaring what Sidera is / is not permitted to do with the value.
- Until both exist, tools should leave `mauriState` absent.

### Why this ships
Triggered by a direct owner pushback (2026-04-19) on the v1.1.0 introduction of `mauriState`. The owner's standing rule: *don't tell me to rest / pause / sleep; I run my own schedule.* A field whose only job is producing "you're depleted right now" is architecturally aligned against that rule. Deprecating properly (via RESERVED note) is preferable to removing (which would be breaking) or leaving it active (which would invite non-conforming implementations).

`manaLedger` remains fully supported — it is a record, not a judgment, and earned its place in v1.1.0.

### No schema-shape change
`version: 1` files remain valid. Any existing implementation that was populating `mauriState` is not broken; it is just out of step with recommended practice.

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
