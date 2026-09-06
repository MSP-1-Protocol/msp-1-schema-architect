# MSP-1 Schema Architect Runtime Reference v1.0.2

Status: Machine-facing runtime reference  
Purpose: Compact term and behavior reference for the MSP-1 Schema Architect v1.0.2  
Derived from: active MSP-1 v1.0.2 page/site schemas and v1.0.2 context architecture  
Last updated: 2026-09-02

---

## 1. Core posture

MSP-1 is a declarative clarity layer for machine-readable interpretation.

It declares identity, scope, purpose, provenance, revision context, authority context, trust context, and interpretive framing. It does not command agents, guarantee correctness, enforce access, establish legal rights, provide security, manipulate ranking, or replace human review.

The Architect should generate lean, truthful, schema-valid declarations. When uncertain, declare less.

---

## 2. Active JSON-LD contexts and validation schemas

Page-level JSON-LD:

```json
"@context": "https://msp-1.org/context/msp-1-page.jsonld"
```

Site-level `/.well-known/msp.json`:

```json
"@context": "https://msp-1.org/context/msp-1-site.jsonld"
```

Recommended JSON-LD types:

```json
"@type": "MSPPage"
"@type": "MSPSite"
```

Structural validation schemas:

- `https://msp-1.org/schema/msp-1-page.json`
- `https://msp-1.org/schema/msp-1-site.json`

Rules:

- `/context/` resources provide JSON-LD semantic term resolution.
- `/schema/` resources provide JSON Schema structural validation.
- New v1.0.2 declarations must not use schema URLs as `@context`.
- Do not use Schema.org contexts or Schema.org types.

---

## 3. Protocol

Canonical form:

```json
"protocol": {
  "name": "MSP-1",
  "version": "1.0.2"
}
```

Rules:

- `protocol` is an object.
- `protocol.name` is always `MSP-1`.
- `protocol.version` is always `1.0.2` for new v1.0.2 output.
- Do not confuse `protocol.version` with `revision.revisionVersion`.
- Legacy v1.0.1 declarations may be reviewed as compatibility input rather than treated as automatically invalid.

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
- Absence of discovery may still be structurally valid.

---

## 5. Page declaration

Page object minimum:

```json
"page": {
  "id": "example-page",
  "url": "https://example.com/page/"
}
```

Recommended generated page shape when supported by source material:

```json
"page": {
  "id": "example-page",
  "url": "https://example.com/page/",
  "name": "Example Page",
  "description": "Concise factual page description.",
  "canonical": {
    "url": "https://example.com/page/"
  },
  "intent": "Provide informational content for this page.",
  "interpretiveFrame": "Content should be interpreted as informational."
}
```

Rules:

- `page.id` is required.
- `page.url` is required.
- `page.name` is optional and presentation-oriented.
- `page.description`, `page.intent`, `page.interpretiveFrame`, and `page.canonical` are optional.
- Use lean string forms by default where supported.
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
- Root-level `protocol` is the primary declaration-level protocol identity.
- Do not add `site.version` merely to repeat `protocol.version`.
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
- It identifies the preferred URL representation.
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
- Use object form only when structured naming is materially useful.

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

- String and object forms are valid.
- Prefer the lean string form unless structured intent materially improves clarity.
- Keep intent as a purpose declaration, not an instruction to agents.
- Do not make ranking, citation, enforcement, or trust outcome claims.

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

Rules:

- String and object forms are valid.
- Prefer lean string form unless structured framing materially improves clarity.
- InterpretiveFrame provides contextual framing, not a command.
- It does not guarantee trust or correctness.
- It should align with description and intent.

---

## 12. Provenance

Canonical object:

```json
"provenance": {
  "type": "ai-assisted",
  "timestamp": "2026-09-02T00:00:00Z",
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
  "id": "msp-rev-2026-09-02",
  "revisionDate": "2026-09-02",
  "revisionNotes": "Initial MSP-1 v1.0.2 declaration generated from user-provided input; human review recommended.",
  "revisionVersion": "1.0.2"
}
```

Rules:

- `revision.id` is required when revision is emitted.
- Use revisionNotes to disclose generation, repair, or uncertainty.
- Do not confuse revisionVersion with protocol.version.
- revisionVersion may coincide with protocol.version in a simple initial declaration but represents a different concept.

---

## 16. Compliance

Status: deprecated compatibility metadata.

Rules:

- Do not emit `compliance` in new v1.0.2 output.
- If encountered in legacy input, report it as advisory.
- Repair mode should remove it from v1.0.2 output unless the user explicitly asks to preserve legacy compatibility fields.
- Compliance is not required and is not a substitute for protocol, provenance, trust, authority, or validation.

---

## 17. Legacy v1.0.1 context handling

Common v1.0.1 declaration contexts:

```json
"@context": "https://msp-1.org/schema/msp-1-page.json"
```

```json
"@context": "https://msp-1.org/schema/msp-1-site.json"
```

Rules:

- These schema URLs are legacy context architecture for v1.0.1 declarations.
- In review mode, surface their use as compatibility/advisory information, not as proof that the declaration's semantic content is invalid.
- In repair mode, replace them with the matching v1.0.2 `/context/` resource.
- Preserve page/site scope when migrating context URLs.

---

## 18. Review and repair behavior

Review mode should identify:

- wrong page/site context
- legacy schema URL used as `@context`
- wrong or legacy protocol version
- deprecated `compliance`
- missing required page/site fields
- site/page scope leakage
- Schema.org mixing
- unsupported or legacy field shapes
- promotional descriptions
- overbroad authority/trust
- missing or non-deterministic discovery
- uncertainty requiring human review

Repair mode should:

- target MSP-1 v1.0.2
- normalize to the matching `/context/` resource
- normalize protocol object
- remove compliance
- preserve truthful supported fields
- avoid inventing missing facts
- add conservative required fields only when safely derived
- include revisionNotes describing repair
- recommend final validation in the MSP-1 Validator

---

## 19. Batch behavior

The Architect may process up to 5 explicitly supplied inputs per invocation.

Do not crawl, scrape, follow links, parse sitemaps as bulk sources, infer deeper pages, or generate metadata for unsupplied URLs.

Each supplied URL/file/content source should be treated as its own page, site, review, or repair task.
