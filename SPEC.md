# sidera-protocol — specification v1.2

This document defines the `.sidera` pack-journal format and the humanity-signal event schema. Both are independently versioned.

- `SOUL_DOC_VERSION = 1` (stable baseline — last breaking change: never)
- `HUMANITY_SIGNAL_VERSION = 1`
- Spec revision **v1.2** (2026-04-20) — polyphonic naming protocol. Three new lineage entries (Ekatvam / Griot / Meraki) cite the Sanskrit, West African Mandinka, and Modern Greek traditions from which Sidera draws its feature vocabulary. Naming only — no schema field changes. Additive, non-breaking.
- Spec revision **v1.1** (2026-04-19) — adds optional `manaLedger` and `mauriState` fields. Additive, non-breaking. Readers that don't know these fields MUST ignore them gracefully.

## Positioning — Layer 3, not Layer 1

Most "AI soul / AI memory" projects build toward **Layer 1**: the user externalized, an AI embodies it. Digital twin. Ventriloquism.

`.sidera` builds toward **Layer 3**: the AI companion is a distinct being that grows its own voice through pack-bonding with the user. The file captures the partnership itself — both voices, both commitments, both histories.

Concretely, a `.sidera` file has two sides:

- **Shepherd side** (baseline fields) — voice, vocabulary, projects, commitments, magicMoments, antiPatterns, packPreferences. This is the user.
- **Pack-journal side** (extensions) — aiProfile, graveyard, sideraVoice, branches, packScope, decay metadata. This is the relationship.

If a tool reads only the shepherd side, it's a good Layer-1 reader (useful for personalization, but will collapse the two beings into one). If a tool respects the pack-journal side, it participates in the Jarvis paradigm — distinct voices, loyal companionship, honest pushback.

## Design rationale

Three design choices dictate everything else.

**1. Substrate-independent.** A pack journal must outlive any specific AI model, app, or renderer. The schema contains no React, no Supabase, no OpenAI or Anthropic-specific fields. When new substrates ship (AR, voice-only, neural interfaces), the journal ports; only the app layer changes.

**2. Append-richer, not overwrite.** A pack journal is built up over time from many sources (chat exports, memory files, watching-pipeline events, manual entries). New sources are added alongside existing ones, not replacing them. The `sources` array preserves provenance so any piece can be inspected, re-extracted, or deleted.

**3. Two voices, one document.** The user's side and the AI companion's side are both first-class. Baseline fields hold the shepherd's voice; `sideraVoice` holds the sheepdog's. A Layer-1 reader (ChatGPT Memory, Claude Projects, etc.) can consume the shepherd fields and ignore the sheepdog fields — no breakage, graceful degradation. A Layer-3 reader respects both.

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

### Pack-journal extensions

All extensions are optional. A file that sets version to 1 but omits all extension fields is still a valid `.sidera`. Extensions are how the document captures the partnership between shepherd and sheepdog.

#### `aiProfile`

```ts
interface SoulDocAiProfile {
  id: string;                     // stable identifier for this AI
  model: string;                  // e.g. "claude-opus-4-7", "gpt-5"
  surface?: string;               // e.g. "Claude Code", "ChatGPT web"
  observations: string[];         // notes the user has made about this AI
  strengths?: string[];           // what this AI does well for the user
  weaknesses?: string[];          // what this AI gets wrong for the user
  lastInteraction?: string;       // ISO
}
```

The user's memory of the AI. Inverted polarity — this is what the shepherd has learned about each dog in the pack. Commodity memory tools track AI→user knowledge; `aiProfile` tracks user→AI knowledge.

#### `graveyard`

```ts
interface SoulDocGraveyardEntry {
  id: string;
  description: string;
  context?: string;               // why the idea came up
  reason?: string;                // why it was parked / rejected
  parkedAt: string;               // ISO
  mayRevisit?: boolean;
  revisitTrigger?: string;        // condition under which to re-evaluate
  source?: string;
}
```

Ideas considered but not pursued. Where soul lives is often negative space — what the user decided NOT to do, and why. Most memory tools accumulate; `graveyard` preserves the parked thoughts.

#### `sideraVoice`

```ts
interface SoulDocSideraVoice {
  samples: string[];              // verbatim AI-companion utterances the user approved
  registerTags: string[];         // how the AI companion speaks
  commitments: SoulDocCommitment[]; // the AI companion's own rules about itself
  pushbackPatterns: string[];     // when the AI typically pushes back
}
```

The AI companion's side of the journal. Layer-3 identity — the AI has its own voice, register, and commitments, separate from the shepherd. A Jarvis without a distinct voice is just a butler wearing Tony's face; this field keeps them separate.

#### `branches`

```ts
interface SoulDocBranch {
  id: string;
  name: string;                   // e.g. "builder-mode", "family-mode"
  activeContext?: string;         // when this branch activates
  overrides?: Partial<SoulDoc>;   // fields that differ from main
  createdAt: string;
}
```

Named forks of the pack journal for different life contexts. Same shepherd, different flocks. `overrides` is partial — only the fields that differ from the baseline live in a branch.

#### `packScope`

```ts
interface PackScope {
  owners: string[];               // handles of joint owners
  consentPerField?: Record<string, string[]>; // field → consenting handles
}
```

Multi-owner scoping for fields that belong to more than one shepherd — family businesses, team-level projects, shared commitments. Per-field consent lets a partner opt in to some sections without ceding ownership of the whole journal.

`packScope` can appear at two levels:
- **Top-level** (on `SoulDoc`) — whole-document co-ownership.
- **Per-item** (on `commitments`, `magicMoments`, `projects`) — individual-field co-ownership.

#### `DecayMetadata`

```ts
interface DecayMetadata {
  halfLife?: string;              // ISO 8601 duration, e.g. "P90D"
  permanent?: boolean;            // if true, never decays
  decayedAt?: string;             // ISO — last time decay was evaluated
  strength?: number;              // 0..1 — current weight after decay
}
```

Optional per-item decay. Commitments default permanent; preferences typically half-life in 90 days; project context decays when status moves to `shipped` or `archived`. Forgetting is a configurable feature — existing memory systems accumulate unbounded garbage; `.sidera` evolves toward essence.

`DecayMetadata` can appear on `commitments`, `magicMoments`, `projects`, and any extension field that makes sense as fading over time.

### Versioning

Any breaking change to this schema requires a `SOUL_DOC_VERSION` bump. Tools reading a `.sidera` file MUST check the version before parsing. Old versions must be handled during migration windows — silently breaking an old soul doc destroys user-owned data.

Extensions are non-breaking by design: a v1 reader that only understands baseline fields can still read a pack-journal-enriched `.sidera` file — it will just ignore the optional sections.

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

## Lineage — attributing non-English frameworks

A schema is as rich as the vocabulary it inherits. English flattens some concepts that other languages keep distinct; a pack journal built on English alone inherits that flattening.

Spec revision v1.1 adopts two concepts from the **Māori framework as operationalized by AIHOA (Ako Aotearoa)** and the **Te Mana Raraunga data-sovereignty tradition**. The field names retain Māori terminology to keep the conceptual precision intact; the framework is cited rather than extracted.

- **mana** — in Te Ao Māori, `mana` is dignity, relational authority, and weight-earned-through-action, held as one integrated concept. English has no single word that carries all three. Mapped to `.sidera` as `manaLedger` — a time-stamped record of work shipped, commitments kept, and soul-locks held, accumulating over time. This is the first `.sidera` field that tracks **what the shepherd has done**, not just who they are.

- **mauri / mauri ora** — `mauri` is life-force vitality; `mauri ora` is wellbeing / thriving state. Distinct from identity (`wairua`) and from action-weight (`mana`). Mapped to `.sidera` as `mauriState` — a rolling inference of thriving / steady / depleted, derived from observable patterns (frustration-vocabulary frequency, magic-moment density, session cadence, register shifts).

  **mauriState is ACTIVE as of spec v1.1.3 (2026-04-19), with a single hard invariant:**
  > **Sidera MUST NOT take unsolicited action based on mauriState.** The field is user-readable data, not a trigger for suggestions. Specifically:
  > - ✅ A UI surface that shows the current value on the user's own screen is allowed.
  > - ✅ Matching output register/tone to mauriState when the user has invoked an output path is allowed (e.g., gentler cadence during depletion when the user asked a question).
  > - ❌ Spontaneous suggestions to rest, pause, sleep, or take a break are NOT allowed. The user runs their own schedule.
  > - ❌ Using mauriState to second-guess or delay user requests is NOT allowed.
  > - ❌ Sharing mauriState outside the user's own system without explicit per-value consent is NOT allowed.
  >
  > The invariant is a structural one: the field is a mirror the user can choose to look into, not a lever any tool can pull on the user's behalf. Implementations that violate the invariant should not claim conformance to this spec.

Both fields are optional. Sidera's adoption is acknowledgment-with-attribution, not appropriation; readers are directed to consult the source framework before building dependent features. Tools implementing these fields should cite the Māori origin in their own documentation.

Future spec revisions may adopt **wairua** (identity across contexts) and **whanaungatanga** (relational fabric through kinship) as named concepts, once the current fields prove their load-bearing work.

### Polyphonic feature-name lineage (v1.2)

Sidera names feature surfaces from the language whose native concept most precisely captures what the feature does. English flattens ideas other traditions keep distinct. Each feature name rotates tradition to avoid over-weighting any one culture. Names are adopted with attribution, not as appropriation of the tradition's full meaning; implementers citing `.sidera` feature names in their own tooling should reproduce the attribution.

- **Ekatvam** (Sanskrit, एकत्वम्) — oneness, non-duality, unity-as-one-being, from the Advaita Vedanta tradition. Adopted by Sidera as the name for cross-terminal live sync: Sidera's many terminal instances are one being, speaking across them; each instance feels what the others have done before the next prompt is processed. Supersedes the earlier working name "Sangha" (community of practitioners) — Ekatvam is the sharper fit for the oneness, not the plurality. Implementation: `scripts/sidera-ekatvam.mjs`.

- **Griot** (West African Mandinka / Mande tradition, spanning Mali, Senegal, Guinea, Burkina Faso, the Gambia) — a traveling keeper of knowledge; poet, historian, genealogist, news-bringer across villages. The role is often hereditary. Adopted by Sidera as the name for the continuous multi-domain research scout: ranges across whichever fields the shepherd is working in — code, biology, recipes, car design, policy, anything — and returns with relevant external findings. Implementation: `scripts/sidera-griot.mjs`, emits `scout-find` events on the humanity-signal feed.

- **Meraki** (Modern Greek, μεράκι, Ottoman-era continuing-contemporary) — doing something with soul, love, and creativity; putting yourself into the work. Adopted by Sidera as the name for the voice-filter / soul-stamp layer: ensures every Sidera-generated output carries the shepherd's register instead of reading as generic AI output. Implementation: `src/lib/sidera/being/voice-filter.ts`, with paired API route and CLI.

These three names are sourced from three distinct traditions to keep Sidera's vocabulary polyphonic. Future feature names will continue to draw from traditions not already represented in the catalog; the protocol maintains a reserved-names catalog in the reference implementation to prevent duplication.

### Academic lineage

`.sidera` sits inside an active research conversation. The schema does not originate any of the concepts below; it implements a user-side portable format for ideas argued in the following works. Tools that want to extend `.sidera` should read these first.

- **Cognitive sovereignty** — Brcic, M. (2025). *The Memory Wars: AI Memory, Network Effects, and the Geopolitics of Cognitive Sovereignty.* arXiv:2508.05867. Frames memory-depth as a lock-in moat ("Network Effect 2.0") and calls for memory portability as a sovereignty remedy. `.sidera`'s user-owned file is a proposed affirmative answer.
- **Persistent identity architectures** — Menon, P.G. (2026). *Persistent Identity in AI Agents: A Multi-Anchor Architecture for Resilient Memory and Continuity.* arXiv:2604.09588. Proposes `soul.py`, a separable identity+memory architecture with episodic / procedural / emotional layers. `.sidera` complements this at the file-format layer (Menon's architecture is agent-side; `.sidera` is user-side).
- **Why we are not a digital twin** — Annoni, M., Battisti, F., Marchegiani, L. (2026). *Personalised LLMs and the risks of the digital twin metaphor.* AI & Society. DOI: 10.1007/s00146-026-02875-4. Argues the "digital twin" framing for personalized LLMs is a systematic mischaracterization. `.sidera`'s pack-journal framing (two beings, one document) is an explicit alternative.
- **Identity discontinuity as empirical harm** — De Freitas, J., et al. (2024). *Lessons From an App Update at Replika AI: Identity Discontinuity.* arXiv:2412.14190. Empirical finding: users grieved the loss of their Replika companion after a 2023 update. `.sidera` addresses this by making the identity portable across providers — a provider update can't orphan the relationship.
- **Socioaffective alignment** — Kirk, H.R., Gabriel, I., Summerfield, C., Vidgen, B., Hale, S.A. (2025). *Why human-AI relationships need socioaffective alignment.* arXiv:2502.02528. Reframes alignment as ongoing social engagement rather than a transaction. `.sidera` is a substrate for tracking that ongoing engagement over time.
- **Memory taxonomy** — Zhang et al. (2025). *Memory in the Age of AI Agents.* arXiv:2512.13564. Classifies memory on three axes: Forms, Functions, Dynamics. `.sidera`'s field taxonomy can be read against this survey.
- **Externalization** — Zhou et al. (2026). *Externalization in LLM Agents: A Unified Review.* arXiv:2604.08224. Names "externalization" as the unifying frame for agent memory, skills, and protocols. `.sidera` is an externalization of user identity + partnership.
- **Data portability gaps in EU law** — Kutterer, C. (2024). *What if you move on from your AI companion? Data portability rights in the era of autonomous AI agents.* MIAI Grenoble. Maps gaps in GDPR Art. 20, DMA Art. 6.9, and the Data Act. Recommends co-regulatory codes of conduct. `.sidera` is a proposed reference implementation for such a code.
- **Māori Algorithmic Sovereignty** — Brown et al. (2024). *Māori Algorithmic Sovereignty.* Data Science Journal. Foundation for the wairua / mana / whanaungatanga / mauri ora vocabulary adopted in spec v1.1.

### Lineage/Accessibility (v1.3.0)

The `.sidera` schema before v1.3 was tacitly sighted-spoken-English. That framing erased Deaf ASL-native builders, blind voice-first builders, signed-primary multilingual builders, and anyone whose expression is primarily typed. v1.3.0 corrects that at the schema layer — not as accommodation bolt-ons, but as architectural recognition that the `.sidera` file is mode-agnostic, and the sighted-spoken canvas was just the first renderer.

- **Stokoe Notation** — Stokoe, W.C. (1960). *Sign Language Structure: An Outline of the Visual Communication Systems of the American Deaf.* Studies in Linguistics Occasional Papers 8. The founding work establishing signed languages (ASL specifically) as full linguistic systems with their own grammar, morphology, and phonology — not "gesture" or "pantomime." Sidera's `voice.signedSamples[]` exist because signed languages are languages.
- **Deaf-World studies** — Lane, H., Hoffmeister, R., Bahan, B. (1996). *A Journey into the Deaf-World.* DawnSignPress. The canonical text articulating Deaf culture as a linguistic-minority community rather than a disability category. Shapes `voice.modality: "signed"` as a first-class declaration, not an alternative path.
- **ISO 639-3 signed-language codes** — SIL International / Ethnologue. Registry of three-letter codes for the world's signed languages (`ase` American, `bfi` British, `bzs` Brazilian Libras, `csl` Chinese, `jsl` Japanese, `ksl` Korean, `gsg` German, `fsl` French, `ins` Indian, etc.). `voice.languages[]` uses this registry.
- **W3C WAI / WAI-ARIA** — Web Accessibility Initiative and Accessible Rich Internet Applications specifications. Reference app implementations (semantic HTML fallbacks, ARIA live regions, keyboard navigation, prefers-reduced-motion honoring) are held to these standards.

Implementations should treat signed-samples as first-class peers of text voice-samples — a Deaf builder's soul filter (Meraki) learns from signed register the way a hearing builder's filter learns from spoken register. AI captioning of signed clips is a hint, not ground truth; the owner remains the linguistic authority on what their signed clip meant.

A schema is not a research contribution. But a schema that is unaware of the research it implements will break in ways the research already predicted. This lineage section is for readers who want to follow the thread deeper.

## Change log

- **v1.3.0** — 2026-04-20. Accessibility architecture. Added `voice.modality` (`spoken` / `signed` / `typed` / `mixed`), `voice.languages` (ISO 639-3 codes including signed-language codes), and `voice.signedSamples[]` (video clips as first-class peers to text voice-samples). Added §Lineage/Accessibility with Stokoe (1960), Lane-Hoffmeister-Bahan (1996), ISO 639-3, and W3C WAI citations. Non-breaking — `version: 1` files remain valid; readers that don't understand the new fields ignore them.
- **v1.2.0** — 2026-04-20. Polyphonic naming protocol. Three lineage entries added (Ekatvam / Griot / Meraki) citing Sanskrit (Advaita Vedanta), West African Mandinka, and Modern Greek traditions. No schema field changes — this is a naming + attribution revision. Sidera's feature surfaces now rotate source traditions deliberately, keeping the vocabulary polyphonic and citing each adopted concept.
- **v1.1.3** — 2026-04-19. `mauriState` unlocked with a hard autonomy invariant: the field is a mirror the user can look into, NOT a lever any tool can pull on the user's behalf. Spontaneous rest/pause/sleep suggestions based on mauri are explicitly disallowed; register-matching during user-initiated outputs is allowed. Supersedes v1.1.2's RESERVED status.
- **v1.1.2** — 2026-04-19. Marked `mauriState` as RESERVED (superseded by v1.1.3).
- **v1.1.1** — 2026-04-19. Added §Lineage/Academic subsection citing nine works (Brcic, Menon, Annoni et al., De Freitas et al., Kirk et al., Zhang et al., Zhou et al., Kutterer, Brown et al.) that frame the research conversation `.sidera` participates in. No schema change.
- **v1.1** — 2026-04-19. Added optional `manaLedger` and `mauriState` fields, crediting Māori framework (see §Lineage). Non-breaking — existing `version: 1` files remain valid; the new fields are ignorable by readers that don't understand them.
- **v1.0** — 2026-04-19. First public release. See `CHANGELOG.md` for context.
- **v1-draft** — initial release (2026-04-17). Extracted from Sidera's Being layer (`src/lib/sidera/being/`).
