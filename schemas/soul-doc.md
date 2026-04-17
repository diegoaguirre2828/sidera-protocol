# soul-doc — human-readable schema summary

Full rationale in [`../SPEC.md`](../SPEC.md). Validator in [`soul-doc.schema.json`](./soul-doc.schema.json).

| Field | Type | Required | Notes |
|---|---|:---:|---|
| `version` | `1` | ✓ | Hard-coded constant. Bump on breaking change. |
| `generatedAt` | ISO 8601 | ✓ | When the doc was first created. |
| `updatedAt` | ISO 8601 | ✓ | Last mutation. |
| `owner` | `{handle, displayName?, locale?, timezone?}` | ✓ | `handle` is user-chosen; not email. |
| `voice` | `{samples, registerTags, cadence?}` | ✓ | Samples MUST be verbatim. |
| `vocabulary` | `{phrases, whistles}` | ✓ | Whistles = commands loaded with meaning. |
| `projects` | array of project objects | ✓ | Active / paused / shipped / archived. |
| `commitments` | array of commitment objects | ✓ | Durable rules. `reason` helps edge-case judgment. |
| `magicMoments` | array of moment objects | ✓ | Alignment successes. |
| `antiPatterns` | array of anti-pattern objects | ✓ | Failure modes. |
| `packPreferences` | array of pack objects | ✓ | Which models for which roles. |
| `sources` | array of source objects | ✓ | Provenance. Reference raw archives, don't embed. |
| `notes` | array of note objects | ✓ | User-owned free-form addenda. |

## Minimum viable doc

```json
{
  "version": 1,
  "generatedAt": "2026-04-17T00:00:00.000Z",
  "updatedAt": "2026-04-17T00:00:00.000Z",
  "owner": { "handle": "someone" },
  "voice": { "samples": [], "registerTags": [] },
  "vocabulary": { "phrases": [], "whistles": [] },
  "projects": [],
  "commitments": [],
  "magicMoments": [],
  "antiPatterns": [],
  "packPreferences": [],
  "sources": [],
  "notes": []
}
```

A minimum viable doc is structurally valid but useless. The value is in the filled fields.
