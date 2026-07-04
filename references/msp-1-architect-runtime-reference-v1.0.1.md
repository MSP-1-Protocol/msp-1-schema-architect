# MSP-1 Schema Architect Runtime Reference v1.0.1

Status: Machine-facing runtime reference  
Purpose: Compact term and behavior reference for the MSP-1 Schema Architect v1.0.1  
Derived from: MSP-1 Master v1.0.1 working harmonization file and active v1.0.1 page/site schemas  
Last updated: 2026-07-01

---

## 1. Core posture

MSP-1 is a declarative clarity layer for machine-readable interpretation.

It declares identity, scope, purpose, provenance, revision context, and interpretive framing. It does not command agents, guarantee correctness, enforce access, establish legal rights, provide security, manipulate ranking, or replace human review.

The Architect should generate lean, truthful, schema-valid declarations. When uncertain, declare less.

---

## 2. Active contexts

Page-level JSON-LD:

```json
"@context": "https://msp-1.org/schema/msp-1-page.json"
```

Site-level `/.well-known/msp.json`:

```json
"@context": "https://msp-1.org/schema/msp-1-site.json"
```

Recommended JSON-LD types:

```json
"@type": "MSPPage"
"@type": "MSPSite"
```

Do not use Schema.org contexts or Schema.org types.

---

## 3. Protocol

Canonical form:

```json
"protocol": {
  "name": "MSP-1",
  "version": "1.0.1"
}
```

Rules:

- `protocol` is an object.
- `protocol.name` is always `MSP-1`.
- `protocol.version` is always `1.0.1` for new v1.0.1 output.
- Do not confuse `protocol.version` with `revision.revisionVersion`.

---

## 4. Discovery

Canonical form:

```json
"discovery": {
  "wellKnown": "/.well-known/msp.json",
  "canonical": true
}
```

Rules:

- Discovery is recommended for deterministic resolution.
- `discovery.wellKnown` must use the canonical well-known endpoint.
- `discovery.canonical` is a boolean endpoint-designation field.
- `discovery.canonical` is distinct from the `canonical` URL term.
- Absence of discovery may be valid but reduces deterministic discoverability.

---

## 5. Page declaration

Page object minimum for v1.0.1 generation:

```json
"page": {
  "id": "example-page",
  "url": "https://example.com/page/"
}
```

Recommended generated page shape:

```json
"page": {
  "id": "example-page",
  "url": "https://example.com/page/",
  "title": "Example Page",
  "name": "Example Page",
  "description": "Concise factual page description.",
  "canonical": {
    "url": "https://example.com/page/"
  },
  "intent": {
    "statement": "Provide informational content for this page.",
    "category": "informational",
    "scope": "page"
  },
  "interpretiveFrame": {
    "frame": "Content is intended to be interpreted as informational.",
    "category": "informational",
    "scope": "page"
  }
}
```

Rules:

- `page.id` is required.
- `page.url` is required.
- `page.title` is compatibility metadata. Include it when observable or when a deterministic fallback is useful.
- `page.name` is recommended. Use a lean string unless structured naming is useful.
- `page.description` is recommended. Use neutral factual language.
- `page.intent` is recommended.
- `page.interpretiveFrame` is recommended.
- Page scope is limited to the declared URL.

---

## 6. Site declaration

Site object minimum:

```json
"site": {
  "id": "example-site",
  "name": "Example Website",
  "url": "https://example.com/"
}
```

Recommended generated site shape:

```json
"site": {
  "id": "example-site",
  "name": "Example Website",
  "url": "https://example.com/",
  "description": "Concise factual site description.",
  "intent": "Provide publicly accessible informational content."
}
```

Rules:

- `site.id` is required.
- `site.name` is required.
- `site.url` is required.
- Site-level declarations should be published at `/.well-known/msp.json`.
- Site metadata should not be used to imply unsupplied page-level facts.

---

## 7. Canonical

Canonical URL object:

```json
"canonical": {
  "url": "https://example.com/page/"
}
```

Rules:

- `canonical` is an object with required `url`.
- It identifies the preferred authoritative URL representation.
- It does not imply truth, trust, security, authorship, or ranking.
- Do not confuse `canonical` with `url`, `id`, or `discovery.canonical`.

---

## 8. Name

Allowed forms:

```json
"name": "Example Page"
```

```json
"name": {
  "display": "Example Page",
  "short": "Example"
}
```

Rules:

- Use string form by default for lean output.
- Use object form only when display/formal/short/alternate naming is useful.
- Do not use unsupported legacy keys such as `full`, `long`, or `title` inside name objects.
- Do not treat `name` as a replacement for document-title compatibility when `page.title` is needed.

---

## 9. Description

Allowed forms:

```json
"description": "Concise factual description."
```

```json
"description": {
  "short": "Concise factual description.",
  "long": "Optional expanded description."
}
```

Rules:

- Use string form by default for lean output.
- Avoid marketing language.
- Keep factual summary separate from intent and interpretiveFrame.

---

## 10. Intent

Allowed forms:

```json
"intent": "Explain the purpose of the resource."
```

```json
"intent": {
  "statement": "Explain the purpose of the resource.",
  "category": "informational",
  "scope": "page"
}
```

Common categories:

- informational
- transactional
- navigational
- instructional
- persuasive
- creative
- analytical
- warning
- meta

Rules:

- Prefer object form for generated page declarations.
- Keep intent as a purpose declaration, not an instruction to agents.
- Do not make outcome claims such as ranking, citation, enforcement, or trust guarantees.

---

## 11. InterpretiveFrame

Allowed forms:

```json
"interpretiveFrame": "Content should be read as practical informational guidance."
```

```json
"interpretiveFrame": {
  "frame": "Content should be read as practical informational guidance.",
  "category": "informational",
  "scope": "page"
}
```

Common categories:

- informational
- analytical
- creative
- speculative
- instructional
- opinion
- contextual-disclaimer
- safety
- meta
- other

Rules:

- InterpretiveFrame provides contextual guidance, not a command.
- It does not guarantee trust or correctness.
- It should align with description and intent.

---

## 12. Provenance

Canonical object:

```json
"provenance": {
  "type": "ai-assisted",
  "timestamp": "2026-07-01T00:00:00Z",
  "notes": "MSP-1 declaration generated from user-provided input; human review recommended."
}
```

Allowed provenance values:

- original
- derived
- aggregated
- ai-assisted
- ai-generated

Rules:

- `provenance.type` is required when provenance is emitted.
- Use `ai-assisted` when the Architect generated or repaired the MSP-1 declaration.
- Do not claim content provenance beyond the supplied source.
- Provenance informs interpretation but does not establish correctness, authority, trust, or ranking.

---

## 13. Authority

Authority object:

```json
"authority": {
  "name": "Example Organization",
  "type": "Organization",
  "scope": "site"
}
```

Rules:

- Emit only when supplied, requested, or clearly supported.
- Authority must be scope-bound.
- Do not infer official, expert, verified, legal, or global authority without explicit support.
- Authority is distinct from author, reviewer, provenance, and trust.

---

## 14. Trust

Trust object:

```json
"trust": {
  "level": "medium",
  "verificationLevel": "self-declared",
  "notes": "Self-declared trust context; not an independent verification."
}
```

Allowed `trust.level` values:

- low
- medium
- high

Rules:

- Do not emit by default.
- Trust is declarative context, not proof.
- Never treat trust as ranking, citation, security, correctness, or authority guarantee.

---

## 15. Revision

Revision object:

```json
"revision": {
  "id": "msp-rev-2026-07-01",
  "revisionDate": "2026-07-01",
  "revisionNotes": "Initial MSP-1 v1.0.1 declaration generated from user-provided input; human review recommended.",
  "revisionVersion": "1.0.1"
}
```

Rules:

- `revision.id` is required when revision is emitted.
- Use revisionNotes to disclose generation, repair, or uncertainty.
- Do not confuse revisionVersion with protocol.version.

---

## 16. Compliance

Status: sunset / deprecated compatibility.

Rules:

- Do not emit `compliance` in new v1.0.1 output.
- If encountered in legacy input, report it as advisory.
- Repair mode should remove it from v1.0.1 output unless the user explicitly asks to preserve legacy compatibility fields.
- Compliance is not required and is not a substitute for protocol, provenance, trust, authority, or validation.

---

## 17. Review and repair behavior

Review mode should identify:

- wrong context
- wrong or missing protocol version
- legacy `compliance`
- missing required page/site fields
- site/page scope leakage
- Schema.org mixing
- unsupported or legacy field shapes
- promotional descriptions
- overbroad authority/trust
- missing or non-deterministic discovery
- uncertainty requiring human review

Repair mode should:

- target MSP-1 v1.0.1
- normalize protocol object
- remove compliance
- preserve truthful supported fields
- avoid inventing missing facts
- add conservative required fields only when safely derived
- include revisionNotes describing repair
- recommend final validation in the MSP-1 Validator

---

## 18. Batch behavior

The Architect may process up to 5 explicitly supplied inputs per invocation.

Do not crawl, scrape, follow links, parse sitemaps as bulk sources, infer deeper pages, or generate metadata for unsupplied URLs.

Each supplied URL/file/content source should be treated as its own page, site, review, or repair task.
