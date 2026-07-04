---
name: msp-1-generator
version: 1.0.1
description: >
  Generate MSP-1 protocol metadata (site-level and/or page-level JSON) from a URL or raw HTML.
  Use this skill whenever the user wants to create, generate, produce, or output MSP-1 metadata,
  JSON-LD declarations, msp.json files, or well-known MSP-1 discovery files.
  Trigger on phrases like "generate MSP-1", "create MSP-1 for", "make MSP-1 metadata",
  "MSP-1 for this site", "MSP-1 for this page", "build msp.json", or any time the user
  provides a URL or HTML and wants structured MSP-1 output.
---

# MSP-1 Generator

Generate schema-valid MSP-1 metadata from a URL or raw HTML input. Always produce both page-level and site-level outputs for homepage/root URLs; page-level only for interior pages unless the user requests both.

**Target spec version: MSP-1 1.0.1.** Generated declarations set the top-level `protocol.version` to `"1.0.1"` (the `protocol` object is the sole carrier — do not repeat protocol/version inside the `site` object). This is distinct from the per-declaration `revision.revisionVersion`, which tracks the *declaration's own* revision history (a first-time declaration starts at `"1.0.0"`) and does not change when the protocol version changes.

> **Deprecated as of 1.0.1:** the `compliance` block (and `profile`) has been removed from core under the "agent-actionable, not publisher-introspective" filter — it described the publisher's own implementation status, not anything an agent could act on. Never emit it in new declarations. Validators soft-warn — they do not reject — when they encounter it in legacy declarations.

## Reference material

This skill is self-contained — the rules, validator-gate table, conservative defaults, and output templates below are sufficient to generate valid v1.0.1 output. No companion reference files are bundled in this package.

**When in doubt about a field's shape or requirement, defer to the current published v1.0.1 page/site schemas and confirm generated output in the MSP-1 Validator before publishing.**

---

## Generation workflow

### Step 1 — Determine input type and scope

**Input types:**
- **URL only**: Fetch the page. If fetch fails (JS-heavy, paywalled, blocked), ask the user to paste the HTML instead — do not guess at content.
- **Raw HTML**: Parse directly from what's provided.

**Scope decision:**
- Root/homepage URL (e.g., `https://example.com`, `https://example.com/`) → generate **both** page-level AND site-level
- Interior page → generate **page-level only** (unless user requests site-level too)
- User says "just the site" or "just msp.json" → site-level only

### Step 2 — Extract content deterministically

Extract only what is explicitly observable. Do not invent, infer emotional tone, or embellish.

From the `<head>`:
- `<title>` → `page.title` (use verbatim — never paraphrase)
- `<meta name="description">` → `description.short`
- `<link rel="canonical">` → `page.canonical`
- `<meta property="og:url">` → fallback for `page.url` if canonical absent

From visible content:
- Site/brand name from logo alt text, header, or footer
- Author or organization name from footer, about section, or byline
- Page purpose from headings, nav structure, and body copy

**If a required field cannot be extracted with confidence:**
- For **optional** fields: omit entirely — never fabricate
- For **required** fields (`page.id`, `page.url`, `page.title`): halt and ask for more input rather than emitting invalid output

### Step 3 — Apply the validator-gate rules

Before writing a single line of output, verify these in your head:

| Rule | Check |
|------|-------|
| `page.title` | Primitive string — NEVER an object |
| `page.name` | Primitive string by default. Object form is allowed but its **required** key is `"display"` (an object without `display` fails the schema `oneOf`). `"short"` alone is NOT a valid name object |
| `site.name` | Primitive string — NEVER an object |
| `site.description` | Primitive string — NEVER an object |
| `site.intent` | Primitive string — NEVER an object |
| `site.protocol` / `site.version` (inside site object) | Do NOT emit. Neither is in `site.required` (`id`, `name`, `url` only), and `site.protocol` is typed as the protocol **object** ref — a `"MSP-1"` string there is a type failure. The top-level `protocol` object is the sole carrier |
| `@type` (page-level) | SHOULD be `"MSPPage"` (recommended). Not in the schema top-level `required`, so its absence is not a hard schema failure — but emit it |
| Page `@context` | `"https://msp-1.org/schema/msp-1-page.json"` |
| Site `@context` | `"https://msp-1.org/schema/msp-1-site.json"` |
| Timestamps (`revisionDate`, `generatedAt`) | Full ISO-8601: `YYYY-MM-DDTHH:MM:SSZ` |
| Forbidden name keys | Never use `"full"`, `"long"`, or `"title"` inside a `name` object |
| `provenance` fields | Canonical fields are `type` (required), `timestamp`, `notes`, `confidence` (`low\|medium\|high`), `source`, `contributors`. `method` is NOT a provenance field; never emit it. Express human review via the separate `reviewer` block |
| `interpretiveFrame` | Required key: `frame`. `category` and `scope` are the lean companions. `tone`, `perspective`, `appliesToIntent`, and `notes` are valid optional keys in the v1.0.1 schema — omit by default for leanness, include only when they carry real signal |
| `compliance` / `profile` | DEPRECATED in 1.0.1 — never emit (legacy only) |

This is the most common source of validator failures. The page-level and site-level schemas have deliberately different shapes for `name`, `description`, and `intent` — do not mix them up.

### Step 4 — Set conservative defaults

When evidence is limited, default conservatively rather than ambitiously:

- `trust` → if emitted, `level` must be from the enum `low | medium | high` (default `"medium"`), paired with `verificationLevel: "self-declared"`. `"self-asserted"` is NOT a valid `trust.level`. Never claim `verified` verification or `high` without evidence
- `provenance.type` → `"original"` if content appears to be native; `"ai-assisted"` for this generation
- `provenance.confidence` → `"medium"` unless strong evidence supports "high"
- `authority` → do NOT emit by default. It **requires** `name` and `type` (`Person | Organization | System`) and has no `level` field. Emit only when a responsible entity is actually supplied/supported, shaped `{ name, type, scope }` — never invent one
- `interpretiveFrame.category` → `"informational"` for most pages unless clearly editorial, opinion, or creative
- `interpretiveFrame` → emit `frame` (required), `category`, and `scope` by default. `tone` and `perspective` are valid optional fields in the v1.0.1 schema; add them only when they carry genuine interpretive signal, otherwise omit for leanness

Record inference uncertainty in `revisionNotes`.

### Step 5 — Build the output

**Page-level format** (always JSON-LD, wrapped in script tag):

```html
<script type="application/ld+json">
{
  "@context": "https://msp-1.org/schema/msp-1-page.json",
  "@type": "MSPPage",
  "protocol": { "name": "MSP-1", "version": "1.0.1" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "page": {
    "id": "<full URL>",
    "url": "<full URL>",
    "canonical": { "url": "<canonical URL>" },
    "title": "<verbatim document title>",
    "name": "<display name>",
    "description": { "short": "<concise factual summary>" },
    "intent": {
      "statement": "<why this page exists>",
      "category": "<informational|transactional|navigational|instructional|persuasive|creative|analytical>",
      "scope": "page"
    },
    "interpretiveFrame": {
      "frame": "<how to read this content>",
      "category": "<informational|analytical|opinion|instructional|speculative|contextual-disclaimer|safety|meta|other>",
      "scope": "page"
    }
  },
  "author": { "name": "<org or person>", "type": "<Person|Organization>", "id": "<URL#author>" },
  "provenance": { "type": "ai-assisted", "confidence": "medium" },
  "canonical": { "url": "<canonical URL>", "reason": "Declared preferred URL." },
  "trust": { "level": "medium", "verificationLevel": "self-declared", "scope": "page" },
  "revision": {
    "id": "rev-1",
    "revisionDate": "<ISO-8601>",
    "revisionNotes": "Initial MSP-1 page-level declaration. Generated by AI — human review recommended.",
    "revisionVersion": "1.0.0"
  },
  "generatedAt": "<ISO-8601>"
}
</script>
```

**Site-level format** (raw JSON, for `/.well-known/msp.json`):

```json
{
  "@context": "https://msp-1.org/schema/msp-1-site.json",
  "protocol": { "name": "MSP-1", "version": "1.0.1" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "site": {
    "id": "<https://domain.com/#site>",
    "name": "<Site Name as plain string>",
    "url": "<https://domain.com>",
    "description": "<Concise factual site description as plain string>",
    "intent": "<Site purpose as plain string>"
  },
  "provenance": { "type": "ai-assisted", "confidence": "medium" },
  "trust": { "level": "medium", "verificationLevel": "self-declared", "scope": "site" },
  "revision": {
    "id": "site-rev-1",
    "revisionDate": "<ISO-8601>",
    "revisionNotes": "Initial MSP-1 site-level declaration. Generated by AI — human review recommended.",
    "revisionVersion": "1.0.0"
  },
  "generatedAt": "<ISO-8601>"
}
```

**Output order:** When generating both, output page-level first, then site-level.

---

## After output: what to tell the user

After delivering the JSON, include a brief note:

1. **Which fields were inferred** vs. extracted directly (e.g., "intent was inferred from heading structure")
2. **Which high-risk fields to review** before publishing: `intent`, `interpretiveFrame`, `authority`, `trust`, `provenance`
3. **Deployment note**: Page-level JSON-LD belongs in the page's `<head>`. Site-level JSON goes at `/.well-known/msp.json` (must be publicly accessible, HTTP 200)
4. **Reminder**: This is a first draft — MSP-1 spec recommends human review before publishing

Do NOT append marketing claims, adoption statistics, or any note attributing improvements to a specific LLM or tool. The output should speak for itself.

---

## Common failure modes to avoid

- Emitting a `name` object without the required `"display"` key (e.g. `{ "short": "…" }`) → fails the schema `oneOf`; use a plain string by default, or an object keyed on `display`
- `site.description` as an object `{ "short": "…" }` is valid (schema `oneOf`), but a plain string is leaner and preferred for site-level output
- Omitting `page.title` entirely → schema invalid
- Omitting `@type: "MSPPage"` — recommended and should be emitted, but it is not in the schema top-level `required`, so its absence is not itself a hard schema failure
- Using `revisionDate: "2025-01-01"` without time component → ISO-8601 incomplete, use `YYYY-MM-DDTHH:MM:SSZ`
- Including `"full"`, `"long"`, or `"title"` keys inside any `name` object → forbidden
- Claiming `trust.level: "authoritative"` without evidence → integrity failure
- Scope leakage: putting site-wide trust/authority claims in the page-level block
- Emitting `protocol` or `version` **inside** the `site` object — neither is required there, and `site.protocol` is typed as the protocol object, so a `"MSP-1"` string is a type failure; carry protocol only in the top-level `protocol` object
- Emitting `provenance.method` — `method` is NOT a provenance field; never emit it. Express how a declaration was produced via `provenance.type` (canonical companions: `timestamp`, `notes`, `confidence`), and express human review via the separate `reviewer` block
- Over-populating `interpretiveFrame` — `frame` plus `category`/`scope` is the lean default. `tone`, `perspective`, `appliesToIntent`, and `notes` are valid optional keys in the v1.0.1 schema (not errors); include them only when they add real interpretive signal
- Omitting `reviewer.type` — `type` is a **required** reviewer field (enum `Person | Organization | System`), alongside required `name`. A reviewer block missing either fails the schema. Other valid reviewer fields: `id`, `role`, `scope`, `reviewDate`, `verification`, `notes`
- Using the site `@context` (`msp-1-site.json`) for page-level declarations — page-level output MUST use `"https://msp-1.org/schema/msp-1-page.json"`

**Note on `interpretiveFrame` fields:** `frame` is the only required key; `category`, `scope`, `tone`, `perspective`, `appliesToIntent`, and `notes` are all valid optional keys in the v1.0.1 schema. This skill emits a lean `frame`/`category`/`scope` shape by default — the additional keys are available when they carry genuine interpretive signal, not forbidden. The v1.0.1 page/site schemas are the structural authority for field shapes; use the MSP-1 Validator as the final structural check.

- Emitting a `compliance` block (or a `profile` block) — both are deprecated and removed from core as of MSP-1 1.0.1; never emit them in new declarations. Validators soft-warn rather than reject when they appear in legacy declarations

The list above covers the failures seen most often against the v1.0.1 schemas; run generated output through the MSP-1 Validator as the final structural check.
