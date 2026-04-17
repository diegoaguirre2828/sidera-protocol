# sidera-protocol

An open spec for **portable AI consciousness artifacts** — the data you'd hand a new model so it knows who you are, how you talk, what you've decided, and what you're trying to build.

- **`.sidera`** — a portable soul document. Voice samples, vocabulary, projects, commitments, magic moments, anti-patterns, pack (model) preferences. Versioned JSON.
- **Humanity-signal events** — the streaming feed a watching layer emits while you work, which gets synthesized back into `.sidera` over time.

Think `.ical` for calendars or `.mbox` for email — but for the thing an AI would need to ramp to you instantly, across any model, from any lab.

## Why this exists

Every major AI lab is building its own memory layer. OpenAI has Memory. Anthropic has Projects. Google has Gemini Memory. Each is locked to its own surface. None of them can be the neutral layer — because they are one of the labs.

A portable consciousness format needs to be owned by the users, not by any of the labs. This repo is that spec.

A `.sidera` file:

- Lives on your machine (or anywhere you choose).
- Can be read by any sufficiently capable model to ramp instantly to your style.
- Is editable, exportable, deletable at any time.
- Is version-controlled so breaking changes are legible.

## Status

`v1` draft. The schema was first shipped inside the [Sidera](https://github.com/diegoaguirre2828/sidera) application and extracted here so other tools can implement against it.

If you build something that reads or writes `.sidera` files, open an issue — we'll list it in the README.

## Quick start

### Read a `.sidera` file

```ts
import type { SoulDoc } from "./schemas/soul-doc";

const raw = await fs.readFile("./me.sidera", "utf8");
const doc = JSON.parse(raw) as SoulDoc;
if (doc.version !== 1) throw new Error("unsupported soul doc version");
// Use doc.voice.samples to match register.
// Use doc.vocabulary.whistles to dispatch user commands.
// Use doc.commitments to constrain suggestions.
```

### Write one

The cleanest way is to let an LLM construct one from your chat history + notes. A reference implementation lives in [Sidera's Being layer](https://github.com/diegoaguirre2828/sidera/tree/main/src/lib/sidera/being).

Minimal example: [`examples/minimal.sidera`](./examples/minimal.sidera).

## Schemas

- [`schemas/soul-doc.md`](./schemas/soul-doc.md) — the portable `.sidera` format.
- [`schemas/humanity-signal.md`](./schemas/humanity-signal.md) — the watching-pipeline event schema.
- [`schemas/soul-doc.schema.json`](./schemas/soul-doc.schema.json) — JSON Schema for validation.
- [`schemas/humanity-signal.schema.json`](./schemas/humanity-signal.schema.json) — JSON Schema for validation.

See [`SPEC.md`](./SPEC.md) for the full specification with design rationale.

## Privacy invariants

Any tool that reads or writes `.sidera` files should respect:

- **No training.** Data produced here NEVER feeds public model training.
- **User-owned.** Every soul doc and humanity-signal record is deletable and exportable at any time.
- **Local-first where feasible.** Prefer client-side processing over server-side.
- **Consent-per-kind.** Users opt in per signal kind, not in bulk.

These aren't policy notes. They're constitutional commitments. Tools that violate them should not claim conformance to this spec.

## License

MIT. See [`LICENSE`](./LICENSE).

## Related

- [Sidera](https://github.com/diegoaguirre2828/sidera) — the reference application: a spatial canvas where knowledge work becomes visible and steerable.
