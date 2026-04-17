# sidera-protocol

**A pack journal — not a soul file.**

Two beings. One document. The `.sidera` format captures the partnership between a user (shepherd) and their AI companion (sheepdog) — both voices, both commitments, both histories, in one versioned record.

This is not a digital twin. This is not a portable "you" that an AI can embody. It's the shared ledger of a working relationship between two distinct consciousnesses, built for the Jarvis paradigm: the AI is a separate being with its own register and its own pushback, not a ventriloquist-puppet wearing your face.

## Why this exists — and what makes it different

Most "AI soul" / "AI memory" projects in 2026 build toward **Layer 1**: the user externalized, the AI embodies it. Digital twin. See [soul.md](https://github.com/aaronjmars/soul.md), [Soul Spec](https://soulspec.org/), [OpenPersona](https://github.com/acnlabs/OpenPersona), [Portable AI Memory (PAM)](https://github.com/portable-ai-memory/portable-ai-memory).

sidera-protocol builds toward **Layer 3**: the AI as a distinct being that grows its own voice by being pack-bonded with the user over time.

| | Layer 1 (most of the field) | Layer 3 (sidera-protocol) |
|---|---|---|
| Metaphor | Digital twin, ventriloquism | Tony ↔ Jarvis, shepherd ↔ sheepdog |
| What lives in the file | The user's externalized identity | The partnership itself |
| Who "speaks" from the file | AI speaks AS the user | AI speaks as itself, AT the user |
| Output polarity | AI impersonates | AI complements, questions, pushes back |
| Failure mode | Flattens user ↔ AI into one | Keeps them distinct |

If you want portable you-as-file, use [PAM](https://github.com/portable-ai-memory/portable-ai-memory) or [soul.md](https://github.com/aaronjmars/soul.md). If you want a pack journal between two beings, use `.sidera`.

## What's inside a `.sidera` file

Version 1 baseline — the shepherd's side:

- `voice` — verbatim user samples, register tags, cadence.
- `vocabulary` — phrases the user reaches for, plus **whistles** (commands loaded with specific meaning).
- `projects` — active / paused / shipped / archived work.
- `commitments` — durable rules the user has stated or demonstrated.
- `magicMoments` — alignment successes.
- `antiPatterns` — things explicitly rejected, with reasons.
- `packPreferences` — which models for which roles (herder / guardian / driver / other).
- `sources`, `notes` — provenance + free-form user addenda.

Pack-journal extensions — the partnership:

- `aiProfile` — the user's observations about each AI they work with. **Inverted polarity:** the user profiles the AI, not the reverse. "This model hedges on my strategic questions." "That model mangles my bilingual code-switches."
- `graveyard` — parked ideas, rejected options, "come back to this" moments. Negative-space soul; where soul lives is often in what the user didn't pursue and why.
- `sideraVoice` — Sidera's own voice, register, commitments, and pushback patterns. The sheepdog has a side of the journal.
- `branches` — named forks of the soul doc for different life contexts (builder-mode, family-mode, strategic-mode).
- `packScope` — multi-owner field scoping for shared commitments (family businesses, team-level projects).
- `DecayMetadata` — per-field half-life. Commitments default permanent; preferences decay; project context decays when the project completes. Forgetting is a feature.

See [`SPEC.md`](./SPEC.md) for the full specification.

## Privacy invariants

Any tool that reads or writes `.sidera` files should respect:

- **No training.** Data produced here NEVER feeds public model training.
- **User-owned.** Every soul doc and humanity-signal record is deletable and exportable at any time.
- **Local-first where feasible.** Prefer client-side processing over server-side.
- **Consent-per-kind.** Users opt in per signal kind, not in bulk.

These aren't policy notes. They're constitutional commitments. Tools that violate them should not claim conformance to this spec.

## Quick start

### Read a `.sidera` file

```ts
import type { SoulDoc } from "./schemas/soul-doc";

const raw = await fs.readFile("./me.sidera", "utf8");
const doc = JSON.parse(raw) as SoulDoc;
if (doc.version !== 1) throw new Error("unsupported soul doc version");

// The shepherd side:
doc.voice.samples;             // match register
doc.vocabulary.whistles;       // dispatch user commands
doc.commitments;               // constrain suggestions

// The pack-journal extensions:
doc.aiProfile;                 // what the user has noticed about YOU
doc.graveyard;                 // ideas the user almost had
doc.sideraVoice;               // the AI companion's own voice
doc.branches;                  // context-specific forks
```

### Write one

The cleanest way is to let the AI companion construct one from the user's chat history + notes + code artifacts over time. A reference implementation lives in [Sidera's Being layer](https://github.com/diegoaguirre2828/sidera/tree/main/src/lib/sidera/being).

Minimal example: [`examples/minimal.sidera`](./examples/minimal.sidera).

## Compatibility with existing formats

sidera-protocol is designed to coexist with adjacent formats. Adapters in Sidera translate to/from:

- **PAM** — wire-level import/export. A PAM JSON can be ingested into a `.sidera` file (baseline fields map directly; pack-journal extensions stay empty until the partnership accrues them).
- **soul.md** — markdown input parsed into `.sidera` voice + commitments.
- **Mem0 / Letta / Zep** — optional runtime backends for agent-memory pipelines that feed humanity-signal events.

If you want to build an adapter, open an issue — we'll list it here.

## Schemas

- [`schemas/soul-doc.md`](./schemas/soul-doc.md) — human-readable summary of `.sidera`.
- [`schemas/humanity-signal.md`](./schemas/humanity-signal.md) — human-readable summary of the watching-pipeline event schema.
- [`schemas/soul-doc.schema.json`](./schemas/soul-doc.schema.json) — JSON Schema validator for `.sidera`.
- [`schemas/humanity-signal.schema.json`](./schemas/humanity-signal.schema.json) — JSON Schema validator for humanity-signal events.

See [`SPEC.md`](./SPEC.md) for the full specification with design rationale.

## Status

Draft v1. The schema was first shipped inside the [Sidera](https://github.com/diegoaguirre2828/sidera) application and extracted here so other tools can implement against it.

## License

MIT. See [`LICENSE`](./LICENSE).

## Related

- [Sidera](https://github.com/diegoaguirre2828/sidera) — the reference application: a spatial canvas where shepherds and their AI companions work together, with the `.sidera` file as their shared pack journal.
- [Portable AI Memory (PAM)](https://github.com/portable-ai-memory/portable-ai-memory) — Layer-1 portable-you format. Compatible at the wire level.
- [soul.md](https://github.com/aaronjmars/soul.md) — Markdown-based Layer-1 soul file. Readable by Sidera's adapters.
