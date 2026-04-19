# Changelog

All notable changes to `.sidera` / sidera-protocol will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Schema `version` field (inside the `.sidera` file) is an integer and tracks breaking schema changes only. The semver below tracks the spec repository itself.

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
