# humanity-signal — human-readable schema summary

Full rationale in [`../SPEC.md`](../SPEC.md). Validator in [`humanity-signal.schema.json`](./humanity-signal.schema.json).

## Event

| Field | Type | Required | Notes |
|---|---|:---:|---|
| `version` | `1` | ✓ | Hard-coded constant. |
| `id` | string | ✓ | Stable, unique within a feed. |
| `emittedAt` | ISO 8601 | ✓ | When the event fired. |
| `project` | string | ✓ | Project namespace — `sidera`, `cruzar`, etc. |
| `sessionId` | string | ✓ | Opaque per-session id. No PII. |
| `kind` | string | ✓ | Open-ended. Known kinds below. Unknown kinds MUST be stored, not dropped. |
| `payload` | object | ✓ | Shape depends on kind. |
| `consent` | `ConsentScope` |  | Applied consent. Empty `appliesTo` = store-but-don't-apply. |

## Known kinds

- `shepherd-vocabulary` — a whistle / phrase used in context
- `drift-event` — detected a drift moment
- `soul-lock` — user said "lock it" / "that's the soul"
- `anti-pattern` — user rejected something explicitly
- `magic-moment` — user reacted positively to a specific output
- `decision-point` — user chose A over B with optional rationale
- `register-shift` — detected tone / language change
- `cross-project-echo` — same pattern reappears in a different project
- `think-aloud` — user narrated reasoning while working
- `breed-preference` — user preferred one model over another for a task
- `pack-composition` — user assembled specific models for a job
- `whistle-miss` — model ignored a recall / didn't understand
- `unknown` — forward-compatibility catch

New kinds may be introduced by any implementing tool. Consumers that don't recognize a kind MUST still store the event intact.

## Batch

```ts
interface HumanitySignalBatch {
  version: 1;
  batchId: string;
  flushedAt: string;                 // ISO
  project: string;
  sessionId: string;
  events: HumanitySignalEvent[];
}
```

Flushed at session end or when a local buffer fills.

## Consent

```ts
interface ConsentScope {
  appliesTo: SoulDocTargetField[];
  expiresAt?: string;
}

type SoulDocTargetField =
  | "voice" | "vocabulary" | "projects" | "commitments"
  | "magicMoments" | "antiPatterns" | "packPreferences";
```

`appliesTo` is the set of soul-doc fields this event is allowed to update during synthesis. Empty array = store-only. Missing `consent` means project-level consent applies.
