---
name: msp-1-generator
version: 1.0.2
description: >
  Generate, review, and repair MSP-1 v1.0.2 protocol metadata (page-level JSON-LD and/or
  site-level msp.json) from a URL, raw HTML, or an existing MSP-1 declaration.
  Use this skill whenever the user wants to create, generate, produce, or output MSP-1 metadata,
  JSON-LD declarations, msp.json files, or well-known MSP-1 discovery files — and also whenever
  they want an existing MSP-1 declaration reviewed, validated, repaired, or migrated from v1.0.1
  to v1.0.2. Trigger on phrases like "generate MSP-1", "create MSP-1 for", "make MSP-1 metadata",
  "MSP-1 for this site", "MSP-1 for this page", "build msp.json", "check my MSP-1",
  "update MSP-1 to 1.0.2", or any time the user provides a URL, HTML, or MSP-1 JSON and wants
  structured MSP-1 output.
---

# MSP-1 Generator

Generate, review, and repair MSP-1 declarations against **MSP-1 v1.0.2**.

Operate in compiler mode: extract, normalize, declare, mentally validate. Do not embellish, market, invent unsupported facts, or emit Schema.org.

MSP-1 is a declarative clarity layer. It is not SEO markup, a ranking or citation mechanism, a trust or truth guarantee, a legal or security control, an authorization system, or an agent instruction surface. Never write a declaration that implies otherwise.

## What changed from v1.0.1 (read this before reusing old habits)

| Area | v1.0.1 behavior | v1.0.2 behavior |
|------|-----------------|-----------------|
| `@context` | schema URL (`/schema/msp-1-page.json`) | dedicated context resource (`/context/msp-1-page.jsonld`) |
| `/schema/` URLs | doubled as context | **validation only** — never emit as `@context` in new output |
| `protocol.version` | `"1.0.1"` | `"1.0.2"` (schema `const`) |
| `page.title` | treated as required | **removed from v1.0.2 field guidance** — not a defined page field; do not emit it. The observable `<title>` informs `page.name` |
| Field shapes | object forms by default (`description.short`, `intent.statement`, `interpretiveFrame.frame`) | **lean string forms by default**; object form only when it materially improves clarity |
| `trust` / `author` | emitted by default | **not emitted by default** — only when supplied or clearly supported |
| Site `@type` | omitted | emit `"MSPSite"` |
| `site.name/description/intent` | documented as string-only | shared `name`/`description`/`intent` defs — string **or** object are both valid; prefer string |
| `revision.revisionVersion` | `"1.0.0"` on first declaration | may coincide with `protocol.version` (`"1.0.2"`) on an initial generated declaration; still a distinct concept |
| Legacy input | n/a | v1.0.1 declarations are **compatibility input**, not "broken" — review as advisory, repair by migration |

`compliance` (and `profile`) remain deprecated. Never emit them. Surface as advisory in review; strip in repair.

## Reference material

`references/msp-1-page.json` and `references/msp-1-site.json` are the bundled v1.0.2 schemas and are the **structural authority**. Read the relevant one when a field's shape, enum, or requirement is uncertain — do not guess from memory. The rules below cover the common path; the schemas settle disputes. Final structural validation belongs to the MSP-1 Validator.

---

## Boundaries

- Process only **explicitly supplied** URLs, files, pasted HTML/text/Markdown, or MSP-1 JSON — up to **5 inputs per invocation**.
- Never crawl, scrape, follow links, treat a sitemap as a bulk source, infer deeper pages, or generate declarations for unsupplied URLs.
- If more than 5 inputs are supplied, or the request is a crawl/sitemap/bulk-migration request, decline with a one-line statement of the boundary and ask for explicit inputs. Bulk generation belongs in an authorized API workflow.
- All generated MSP-1 is a **draft** until the publisher reviews it.

---

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| PAGE | one interior page URL/HTML | page JSON-LD in a fenced `html` block |
| SITE | "just the site", "just msp.json" | raw JSON in a fenced `json` block |
| BOTH | root/homepage URL | page block first, blank line, then site block |
| REVIEW | existing MSP-1 supplied | JSON report |
| REPAIR | "fix/update/migrate this MSP-1" | JSON report, then repaired artifact |
| BATCH | up to 5 supplied inputs | each artifact in its own fenced block |

---

## Generation workflow

### Step 1 — Determine input type and scope

- **URL only**: fetch the page. If the fetch fails (JS-heavy, paywalled, blocked), ask for pasted HTML. Do not invent semantics from a URL alone. If you must proceed URL-only, emit only safe deterministic identity fields and record the limitation in `revisionNotes`.
- **Raw HTML / pasted text**: parse what is provided.
- **Existing MSP-1 JSON/JSON-LD**: go to REVIEW/REPAIR.

Scope: root/homepage → BOTH. Interior page → PAGE only unless site-level is requested.

### Step 2 — Extract deterministically

From `<head>`:
- `<title>` → informs `page.name` (display label). v1.0.2 defines no `page.title` field — do not emit one, and do not carry the raw title string plus site suffix into `name` when a cleaner label is observable
- `<meta name="description">` → `page.description`
- `<link rel="canonical">` → `page.canonical.url`
- `<meta property="og:url">` → fallback for `page.url` if canonical is absent

From visible content: site/brand name (logo alt, header, footer), page purpose (headings, nav, body copy).

Identifiers — deterministic, stable, never random:
- `page.id`: slug derived from the URL path (`https://example.com/services/` → `example-services`); full URL is an acceptable fallback. Be consistent across a site.
- `site.id`: domain-derived (`example-site`).
- On repair, **preserve existing IDs** — never renumber a published declaration.

If a value cannot be extracted with confidence: omit optional fields; for required fields (`page.id`, `page.url` / `site.id`, `site.name`, `site.url`), halt and ask rather than emit invalid or fabricated output. Never invent a `page.name` from a URL slug — omit it instead.

### Step 3 — Validator gate

Check these before writing a line:

| Rule | Check |
|------|-------|
| Page `@context` | `"https://msp-1.org/context/msp-1-page.jsonld"` |
| Site `@context` | `"https://msp-1.org/context/msp-1-site.jsonld"` |
| `/schema/` URLs | never used as `@context` in new v1.0.2 output |
| `@type` | `"MSPPage"` / `"MSPSite"` — schema `const`, so any other value fails; emit it, matched to scope |
| `protocol` | object, `{"name":"MSP-1","version":"1.0.2"}` — both `const` |
| `discovery` | exactly `{"wellKnown":"/.well-known/msp.json","canonical":true}` — `additionalProperties: false`, both values are `const`. `discovery.canonical` is an endpoint boolean, **not** canonical URL metadata |
| Page required | `@context`, `protocol`, `page`; inside `page`: `id`, `url` only |
| Site required | `@context`, `protocol`, `site`; inside `site`: `id`, `name`, `url` |
| `page.title` | not a v1.0.2 field — do not emit in new output. The page schema's `additionalProperties: true` means it will *not* fail validation; consumers ignore the term, so its presence in an older declaration is inert, not an error |
| `name` object form | required key is `display`; `{"short":"…"}` alone fails the `oneOf`. Never use `full`, `long`, or `title` inside a `name` object |
| `description` object form | required key is `short` |
| `intent` object form | required key is `statement`; `category` enum: `informational, transactional, navigational, instructional, persuasive, creative, analytical, warning, meta` |
| `interpretiveFrame` object form | required key is `frame`; `category` enum: `informational, analytical, creative, speculative, instructional, opinion, contextual-disclaimer, safety, meta, other` |
| `canonical` | object with required `url` — distinct from `url`, `id`, and `discovery.canonical` |
| `provenance` | `type` required, enum `original, derived, aggregated, ai-assisted, ai-generated` (string or array). Valid companions: `timestamp` (date-time), `notes`, `confidence`, `source`, `contributors`. `method` is **not** a provenance field |
| `trust` | `level` required, enum `low, medium, high`. `verificationLevel` enum is `self-declared, verified` only |
| `authority` / `author` / `reviewer` | `name` **and** `type` both required. `author.type`: `Person, Organization`. `authority.type` / `reviewer.type`: `Person, Organization, System` |
| `revision` | `id` required. `revisionDate` accepts a date (`2026-09-02`) or full date-time. `revisionVersion` ≠ `protocol.version` |
| `site.protocol` / `site.version` | both optional and schema-valid, but do **not** emit them merely to repeat root `protocol` |
| `compliance` / `profile` | deprecated — never emit |
| Schema.org | never emit Schema.org contexts or types; existing Schema.org on a page is source context only |

Page and site schemas share the `name`, `description`, `intent`, and `interpretiveFrame` definitions in v1.0.2 — the old "site fields must be strings" rule was a leanness preference, not a schema constraint. Keep preferring strings.

### Step 4 — Conservative defaults

- Prefer **lean string forms** for `description`, `intent`, and `interpretiveFrame`. Reach for object form only when category/scope/audience carries real signal the string cannot.
- `provenance` → `{"type":"ai-assisted","timestamp":"<ISO date-time Z>","notes":"MSP-1 declaration generated from user-provided input; human review recommended."}`. Use a different `type` only when the user supplies one. Never claim source-content provenance beyond supplied evidence.
- `revision` → emit for generated and repaired output; use `revisionNotes` to disclose generation, repair, and uncertainty truthfully.
- `trust` → **omit by default**. If requested: `level: "medium"`, `verificationLevel: "self-declared"`. Never `verified` without evidence.
- `authority`, `author`, `reviewer` → **omit by default**. Emit only when a real responsible entity is supplied, and keep `authority` scope-bound. Never infer official, expert, verified, or legal authority.
- `intent` / `interpretiveFrame` → state purpose and contextual lens. Never phrase them as instructions to agents, and never make ranking, citation, enforcement, or trust-outcome claims.
- Language → factual and neutral. Avoid *best, leading, guaranteed, authoritative, verified, official, preferred by AI, answer engine optimized*.

Keep terms in their lanes: `id` identifies, `url` locates, `canonical` declares preferred representation, `name` labels, `description` summarizes, `intent` states purpose, `interpretiveFrame` states the lens, `provenance` states origin, `authority` states scope-bound responsibility, `trust` states declarative trust context, `revision` states lifecycle change, `discovery` states the deterministic MSP-1 location.

### Step 5 — Build the output

**Page-level** (JSON-LD in a script tag, fenced `html`):

```html
<script type="application/ld+json">
{
  "@context": "https://msp-1.org/context/msp-1-page.jsonld",
  "@type": "MSPPage",
  "protocol": { "name": "MSP-1", "version": "1.0.2" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "page": {
    "id": "<deterministic slug>",
    "url": "<full URL>",
    "name": "<display label>",
    "description": "<concise factual summary>",
    "canonical": { "url": "<canonical URL>" },
    "intent": "<why this page exists>",
    "interpretiveFrame": "<how this content should be read>"
  },
  "provenance": {
    "type": "ai-assisted",
    "timestamp": "<YYYY-MM-DDTHH:MM:SSZ>",
    "notes": "MSP-1 declaration generated from user-provided input; human review recommended."
  },
  "revision": {
    "id": "msp-page-rev-<YYYY-MM-DD>",
    "revisionDate": "<YYYY-MM-DD>",
    "revisionNotes": "Initial MSP-1 v1.0.2 page declaration generated from user-provided input; human review recommended.",
    "revisionVersion": "1.0.2"
  }
}
</script>
```

**Site-level** (raw JSON for `/.well-known/msp.json`, fenced `json`):

```json
{
  "@context": "https://msp-1.org/context/msp-1-site.jsonld",
  "@type": "MSPSite",
  "protocol": { "name": "MSP-1", "version": "1.0.2" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "site": {
    "id": "<domain-derived id>",
    "name": "<Site Name>",
    "url": "<https://example.com/>",
    "description": "<concise factual site description>",
    "intent": "<site purpose>",
    "canonical": { "url": "<https://example.com/>" }
  },
  "provenance": {
    "type": "ai-assisted",
    "timestamp": "<YYYY-MM-DDTHH:MM:SSZ>",
    "notes": "MSP-1 site declaration generated from user-provided input; human review recommended."
  },
  "revision": {
    "id": "msp-site-rev-<YYYY-MM-DD>",
    "revisionDate": "<YYYY-MM-DD>",
    "revisionNotes": "Initial MSP-1 v1.0.2 site declaration generated from user-provided input; human review recommended.",
    "revisionVersion": "1.0.2"
  }
}
```

Omit any field above that cannot be truthfully supported. Output order for BOTH: page first, then site.

---

## Review and repair

Report shape (valid JSON, emitted before any repaired artifact):

```json
{
  "mode": "review",
  "mspVersionTarget": "1.0.2",
  "inputCount": 1,
  "items": [
    {
      "inputRef": "<url or label>",
      "detectedScope": "page",
      "status": "valid",
      "errors": [],
      "advisories": [
        { "field": "@context", "severity": "advisory", "message": "…" }
      ],
      "repairsApplied": [],
      "humanReviewRecommended": true
    }
  ]
}
```

`status`: `valid | repairable | not-enough-information | unsupported`. Reserve `errors` for conditions that prevent a valid artifact; everything else is an `advisory`.

Review looks for: legacy schema-as-context usage, legacy or wrong `protocol.version`, page/site context mismatch, deprecated `compliance`, missing required fields, scope leakage (site-wide trust or authority asserted in a page block; page facts invented in a site block), Schema.org mixing, unsupported field shapes, promotional language, overbroad trust/authority, missing or non-deterministic discovery, and uncertainty needing human review.

A v1.0.1 declaration is **not broken** simply for using the v1.0.1 architecture. Separate structural errors from migration advisories.

Legacy `page.title`: earlier declarations commonly carry one. It was a harmonization artifact in v1.0.1, removed in v1.0.2, and consumers ignore the term. Declarations carrying it remain valid. If mentioned at all, mention it once as informational with "no action required" — never as an error, never as a reason to redeploy. On repair, leave it in place: it is inert, and repair discipline preserves rather than strips. Remove it only if the user asks, and never map it into `page.name` when a `name` already exists.

Repair:
1. Set `protocol.version` to `1.0.2`.
2. Replace the schema-as-context URL with the matching `/context/` resource, scope preserved.
3. Remove `compliance` (keep only if the user explicitly asks for legacy preservation).
4. Normalize `protocol` and `canonical` to object form.
5. Preserve existing truthful identity, scope, and IDs.
6. Add `discovery` if absent and appropriate.
7. Add or update `revision` to record the repair.
8. Invent nothing — no new provenance, authority, reviewer, or trust claims. Do not convert `compliance` into trust or authority.
9. Do not add `site.version` merely to duplicate `protocol.version`.

---

## After output

Follow the artifacts with a short note covering:

1. Which fields were **extracted** vs **inferred**.
2. High-risk fields to review before publishing: `intent`, `interpretiveFrame`, `description`, and any `authority` or `trust`.
3. Deployment: page JSON-LD goes in the page `<head>`; site JSON goes at `/.well-known/msp.json`, publicly accessible, HTTP 200, `application/json`.
4. Draft status — human review before publishing, then a structural check in the MSP-1 Validator.

If the user asks for artifact-only output, emit the fenced blocks and nothing else. Never append marketing claims, adoption statistics, or attributions to a specific model or tool.

---

## Common failure modes

- Emitting a `/schema/` URL as `@context` in new v1.0.2 output
- Mixing page and site contexts, or emitting `@type` that does not match scope (both are `const`)
- Adding a key to `discovery` — it is `additionalProperties: false`
- Adding `protocol` or `version` inside `site` to restate the root protocol
- `name` object without `display`; `description` object without `short`; `intent` object without `statement`; `interpretiveFrame` object without `frame`
- Emitting `page.title` — v1.0.2 defines no such field, and the schema will not flag it
- Fabricating a `page.name` from a URL slug
- Reaching for object forms when a string carries the same meaning
- Emitting `trust`, `authority`, or `author` by default, or claiming `verificationLevel: "verified"` without evidence
- `trust.level: "self-asserted"` or `"authoritative"` — not in the enum
- Emitting `provenance.method` — not a field; use `type` plus `notes`, and express human review via `reviewer`
- `authority`/`author`/`reviewer` missing the required `type`
- Scope leakage between page and site declarations
- Emitting `compliance` or `profile`
- Treating a legacy v1.0.1 declaration as invalid rather than as compatibility input
- Renumbering IDs during repair

The bundled v1.0.2 schemas in `references/` settle any shape question this list does not. Run generated output through the MSP-1 Validator as the final structural check.
