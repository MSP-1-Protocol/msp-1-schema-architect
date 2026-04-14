---
name: msp-1-generator
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

## Reference files

Load these as needed — don't load all upfront:
- `references/msp-1-core-spec_machine.md` — required/optional field rules per entity
- `references/msp-1-schema_machine.md` — field types, enums, and validator constraints
- `references/msp-1-namespace_machine.md` — canonical namespace IDs
- `references/MSP-1_Technical_Spec.md` — concise validator-gate rules (load first)
- `references/msp-1-inline-example.json` — page-level example output (validator-confirmed)
- `references/msp-example.json` — site-level example output (validator-confirmed)
- `references/msp-1-implementation-best-practices.md` — guidance on inference and uncertainty
- `references/msp-1-common-implementation-mistakes.md` — what to avoid
- `references/msp-1-quick-start-checklist.md` — checklist for complete output

**Always load `references/MSP-1_Technical_Spec.md` before generating.** It contains the critical validator-gate rules that prevent the most common schema failures.

**When in doubt about field shape or requirement, the live validator is the ground truth** — not the schema reference files, which may document fields the validator does not yet accept.

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
| `page.name` | Object with `"short"` key — NEVER a plain string |
| `site.name` | Primitive string — NEVER an object |
| `site.description` | Primitive string — NEVER an object |
| `site.intent` | Primitive string — NEVER an object |
| `site.protocol` (inside site object) | String `"MSP-1"` — REQUIRED and NEVER an object |
| `site.version` (inside site object) | String e.g. `"1.0.0"` — NEVER an object |
| `@type` (page-level only) | Must be `"MSPPage"` — always present, never omitted |
| Page `@context` | `"https://msp-1.org/schema/msp-1-page.json"` |
| Site `@context` | `"https://msp-1.org/schema/msp-1-site.json"` |
| Timestamps (`revisionDate`, `generatedAt`) | Full ISO-8601: `YYYY-MM-DDTHH:MM:SSZ` |
| Forbidden name keys | Never use `"full"`, `"long"`, or `"title"` inside a `name` object |
| `provenance.method` | Always include — use `"ai-generated"` or `"human-authored"` |
| `interpretiveFrame` | Only use: `frame`, `category`, `scope` — no other sub-fields |
| `site.verification` + `site.lastUpdated` | Both belong **inside the `site{}` block**, not at the document root — the validator checks the unwrapped site instance |

This is the most common source of validator failures. The page-level and site-level schemas have deliberately different shapes for `name`, `description`, and `intent` — do not mix them up.

### Step 4 — Set conservative defaults

When evidence is limited, default conservatively rather than ambitiously:

- `trust.level` → `"self-asserted"` (never "verified" or "authoritative" without evidence)
- `provenance.type` → `"original"` if content appears to be native; `"ai-assisted"` for this generation
- `provenance.confidence` → `"medium"` unless strong evidence supports "high"
- `provenance.method` → `"ai-generated"` when generating from a URL or HTML; `"human-authored"` only when the declaration was written by a person
- `authority.level` → `"self-asserted"`
- `interpretiveFrame.category` → `"informational"` for most pages unless clearly editorial, opinion, or creative
- `interpretiveFrame` sub-fields → use only `frame`, `category`, and `scope`; omit all others

Record inference uncertainty in `revisionNotes`.

### Step 5 — Build the output

**Page-level format** (always JSON-LD, wrapped in script tag):

```html
<script type="application/ld+json">
{
  "@context": "https://msp-1.org/schema/msp-1-page.json",
  "@type": "MSPPage",
  "protocol": { "name": "MSP-1", "version": "1.0.0" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "page": {
    "id": "<full URL>",
    "url": "<full URL>",
    "canonical": "<canonical URL>",
    "title": "<verbatim document title>",
    "name": { "short": "<short display name>" },
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
  "provenance": { "type": "ai-assisted", "confidence": "medium", "method": "ai-generated" },
  "canonical": { "url": "<canonical URL>", "reason": "Declared preferred URL." },
  "trust": { "level": "self-asserted", "scope": "page" },
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
  "protocol": { "name": "MSP-1", "version": "1.0.0" },
  "discovery": {
    "wellKnown": "/.well-known/msp.json",
    "canonical": true
  },
  "site": {
    "id": "<https://domain.com/#site>",
    "name": "<Site Name as plain string>",
    "url": "<https://domain.com>",
    "description": "<Concise factual site description as plain string>",
    "intent": "<Site purpose as plain string>",
    "protocol": "MSP-1",
    "version": "1.0.0",
    "verification": {
      "core": true,
      "verified": false,
      "authoritative": false
    },
    "lastUpdated": "<YYYY-MM-DD>"
  },
  "authority": { "subjectId": "<site id>", "scope": "site", "level": "self-asserted" },
  "provenance": { "type": "ai-assisted", "confidence": "medium", "method": "ai-generated" },
  "compliance": { "core": true },
  "trust": { "level": "self-asserted", "scope": "site" },
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

- Using `page.name: "string"` instead of `page.name: { "short": "string" }` → validator failure
- Using `site.description: { "short": "..." }` instead of `site.description: "..."` → validator failure
- Omitting `page.title` entirely → schema invalid
- Omitting `@type: "MSPPage"` from page-level output → schema invalid
- Using `revisionDate: "2025-01-01"` without time component → ISO-8601 incomplete, use `YYYY-MM-DDTHH:MM:SSZ`
- Including `"full"`, `"long"`, or `"title"` keys inside any `name` object → forbidden
- Claiming `trust.level: "authoritative"` without evidence → integrity failure
- Scope leakage: putting site-wide trust/authority claims in the page-level block
- Omitting `site.protocol` from inside the `site` object — it is **required** there even though a top-level `protocol` block also exists; the live validator will reject metadata missing this field
- Omitting `provenance.method` — always include it; use `"ai-generated"` for AI-produced declarations and `"human-authored"` for human-written ones
- Adding `"tone"` to the `interpretiveFrame` object — `tone` appears in the schema reference files but is rejected by the live validator; use only `frame`, `category`, and `scope`
- Adding `"type"` to the `reviewer` object — `type` is not a declared reviewer field and will cause a validation error; valid reviewer fields are: `name`, `id`, `role`, `scope`, `reviewDate`, `notes`
- Using the site `@context` (`msp-1-site.json`) for page-level declarations — page-level output MUST use `"https://msp-1.org/schema/msp-1-page.json"`
- Placing `verification` and `lastUpdated` at the top level of the site-level document — the validator's `policyChecks` function runs against the **unwrapped `site` instance** (the contents of the `site{}` block), not the root document. Both fields MUST live inside `site{}`: `"site": { ..., "verification": { "core": true, "verified": false, "authoritative": false }, "lastUpdated": "YYYY-MM-DD" }`. Placing them at the document root will not clear the advisory.

**Note on schema reference files vs. live validator:** Some fields documented in `msp-1-schema_machine.md` (e.g., `interpretiveFrame.tone`, `interpretiveFrame.perspective`) are not accepted by the live validator. When a conflict exists between a reference file and live validator results, the validator wins. Treat the reference files as informational and the validator as authoritative.

See `references/msp-1-common-implementation-mistakes.md` for the full list.
