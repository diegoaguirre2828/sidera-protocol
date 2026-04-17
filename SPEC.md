# sidera-protocol — specification v1

This document defines the `.sidera` soul document format and the humanity-signal event schema. Both are independently versioned.

- `SOUL_DOC_VERSION = 1`
- `HUMANITY_SIGNAL_VERSION = 1`

## Design rationale

Two design choices dictate everything else.

**1. Substrate-independent.** A soul doc must outlive any specific AI model, app, or renderer. The schema contains no React, no Supabase, no OpenAI or Anthropic-specific fields. When new substrates ship (AR, voice-only, neural interfaces), the soul ports; only the app layer changes.

**2. Append-richer, not overwrite.** A soul doc is built up over time from many sources (chat exports, memory files, watching-pipeline events, manual entries). New sources should be added alongside existing ones, not replace them. The `sources` array on the soul doc preserves provenance so any piece can be inspected, re-extracted, or deleted.

## `.sidera` soul document

### File format

UTF-8 JSON. The canonical file extension is `.sidera`. Pretty-printed with 2-space indentation is preferred for diffability.

### Top-level shape

```ts
interface SoulDoc {
  version: 1;
  generatedAt: string;            // ISO 8601
  updatedAt: string;              // ISO 8601
  owner: SoulDocOwner;
  voice: SoulDocVoice;
  vocabulary: SoulDocVocabulary;
  projects: SoulDocProject[];
  commitments: SoulDocCommitment[];
  magicMoments: SoulDocMagicMoment[];
  antiPatterns: SoulDocAntiPattern[];
  packPreferences: SoulDocPackPreference[];
  sources: SoulDocSource[];
  notes: SoulDocNote[];
}
```

### Fields

#### `owner`

```ts
interface SoulDocOwner {
  handle: string;                 // user-chosen, not email
  displayName?: string;
  locale?: string;                // e.g. "en-US", "es-MX"
  timezone?: string;              // IANA
}
```

`handle` is user-chosen and stable. Do not use email, legal name, or phone.

#### `voice`

```ts
interface SoulDocVoice {
  samples: string[];              // verbatim user quotes
  registerTags: string[];         // short phrases, e.g. "terse", "direct"
  cadence?: string;               // "short bursts", "long flowing", "mixed"
}
```

`samples` must be verbatim. Do not paraphrase, spell-correct, or summarize. Typos and code-switches are load-bearing — they encode register.

#### `vocabulary`

```ts
interface SoulDocVocabulary {
  phrases: string[];              // recurring phrases, verbatim
  whistles: SoulDocWhistle[];
}

interface SoulDocWhistle {
  phrase: string;                 // verbatim trigger the user says
  meaning: string;                // what it means in practice
  examples?: string[];            // usage excerpts
}
```

A **whistle** is a command-level phrase the user has loaded with specific meaning — e.g. `"sync"` meaning "re-read memory," or `"end for today"` meaning "run the close-of-session ritual." These are the highest-leverage piece of a soul doc for directing work.

#### `projects`

```ts
interface SoulDocProject {
  id: string;
  name: string;
  soul?: string;                  // one-paragraph soul of the project
  status?: "active" | "paused" | "shipped" | "archived";
  startedAt?: string;
  notes?: string;
}
```

Include projects the user is *currently working on or recently stewarding*. Prune archived work unless the user wants lineage preserved.

#### `commitments`

```ts
interface SoulDocCommitment {
  statement: string;              // "I won't ship half-ass features"
  reason?: string;                // usually from a specific incident
  source?: string;                // citation
}
```

Commitments are durable rules the user has stated or demonstrated. Each should ideally carry a `reason` so a later model can judge edge cases instead of blindly enforcing.

#### `magicMoments`

```ts
interface SoulDocMagicMoment {
  description: string;
  context?: string;
}
```

Moments when the user experienced something that felt like success or alignment. Used to reverse-engineer what kind of outputs to aim for.

#### `antiPatterns`

```ts
interface SoulDocAntiPattern {
  description: string;
  reason?: string;
  source?: string;
}
```

Things the user has explicitly rejected. Distinct from commitments — commitments are positive rules; anti-patterns are the failure modes.

#### `packPreferences`

```ts
interface SoulDocPackPreference {
  role: "herder" | "guardian" | "driver" | "other";
  preferredModels: string[];       // e.g. ["claude-opus", "gpt-5"]
  notes?: string;
}
```

Which breeds (models) the user prefers for which jobs. Role names are borrowed from the shepherd-metaphor — use them loosely to indicate task class (lead, steady workhorse, fast low-cost, miscellaneous).

#### `sources`

```ts
interface SoulDocSource {
  id: string;
  kind: SoulDocSourceKind;
  ingestedAt: string;              // ISO
  ref: string;                     // opaque pointer
}

type SoulDocSourceKind =
  | "memory-files"
  | "chatgpt-export"
  | "claude-export"
  | "gemini-export"
  | "cursor-export"
  | "git-commits"
  | "voice-memo"
  | "manual-entry"
  | "session-capture";
```

Provenance. Never embed raw source content in the soul doc itself — keep raw archives separate, reference only.

#### `notes`

```ts
interface SoulDocNote {
  id: string;
  createdAt: string;
  body: string;
}
```

Free-form user-owned addenda. Not extracted — the user writes these themselves.

### Versioning

Any breaking change to this schema requires a `SOUL_DOC_VERSION` bump. Tools reading a `.sidera` file MUST check the version before parsing. Old versions must be handled during migration windows — silently breaking an old soul doc destroys user-owned data.

### Validation

`schemas/soul-doc.schema.json` provides a JSON Schema validator. A minimal runtime guard:

```ts
function isSoulDoc(value: unknown): value is SoulDoc {
  if (!value || typeof value !== "object") return false;
  const v = value as Record<string, unknown>;
  return (
    typeof v.version === "number" &&
    typeof v.generatedAt === "string" &&
    typeof v.updatedAt === "string" &&
    typeof v.owner === "object" &&
    v.owner !== null &&
    typeof (v.owner as Record<string, unknown>).handle === "string" &&
    Array.isArray(v.projects) &&
    Array.isArray(v.sources)
  );
}
```

## Humanity-signal events

The watching-pipeline event schema. When an AI tool loads into a work session, it may emit humanity-signal events as the user works. Events flow into a local store; a later synthesis pass updates the soul doc.

Events are **per-session, per-project**. Aggregation across sessions is the synthesis layer's job, not the event's.

### Event

```ts
interface HumanitySignalEvent {
  version: 1;
  id: string;                       // stable, unique within a feed
  emittedAt: string;                // ISO 8601
  project: string;                  // e.g. "sidera", "cruzar"
  sessionId: string;                // opaque per-session id (no PII)
  kind: HumanitySignalKind;
  payload: Record<string, unknown>;
  consent?: ConsentScope;
}
```

### Kinds

Open-ended — tools may emit new kinds. Unknown kinds MUST be stored, not dropped.

Known kinds:

| Kind | Payload intent |
|---|---|
| `shepherd-vocabulary` | A whistle / phrase used in context |
| `drift-event` | Guardian detected a drift moment |
| `soul-lock` | User said "lock it" / "that's the soul" |
| `anti-pattern` | User rejected something explicitly |
| `magic-moment` | User reacted positively to a specific output |
| `decision-point` | User chose A over B, optional rationale |
| `register-shift` | Detected tone / language change |
| `cross-project-echo` | Same pattern reappears in a different project |
| `think-aloud` | User narrated reasoning while working |
| `breed-preference` | User preferred one model over another for a task |
| `pack-composition` | User assembled specific models for a job |
| `whistle-miss` | Model ignored a recall / didn't understand |
| `unknown` | Forward-compatibility catch |

### Consent

```ts
interface ConsentScope {
  appliesTo: SoulDocTargetField[];
  expiresAt?: string;
}

type SoulDocTargetField =
  | "voice" | "vocabulary" | "projects" | "commitments"
  | "magicMoments" | "antiPatterns" | "packPreferences";
```

If a user has consented to only update `vocabulary` from automatic extraction, an event emitted during a session with that scope can feed the `vocabulary` field but not `commitments`. Empty `appliesTo` means store-but-don't-apply — the event is captured for future re-extraction but does not mutate the soul doc.

### Batching

```ts
interface HumanitySignalBatch {
  version: 1;
  batchId: string;
  flushedAt: string;
  project: string;
  sessionId: string;
  events: HumanitySignalEvent[];
}
```

When a session ends or a buffer fills, events are flushed as a batch for ingestion.

## Conformance

A tool claims conformance to sidera-protocol v1 if it:

1. Reads `.sidera` files respecting the version check.
2. Writes `.sidera` files that pass `schemas/soul-doc.schema.json`.
3. Respects the privacy invariants: no training, user-owned, local-first-where-feasible, consent-per-kind.

Tools that want to call themselves *conformant writers* should additionally pass a round-trip test (write then read, structural equality) — provided in `examples/`.

## Change log

- **v1** — initial release (2026-04-17). Extracted from Sidera's Being layer (`src/lib/sidera/being/`).
