# MSP-1 Schema Library — Machine-First Consolidated Reference

Protocol: MSP-1  
Schema hub: https://msp-1.org/schema/  
Index line: MSP-1.0.x • Last updated 2026-01-04  
JSON Schema draft: 2020-12 (via "$schema" in term files)

GOAL
- Provide a compact, deterministic reference for MSP-1 schema structures.
- Optimize for: generation, validation, and conservative inference.
- Do not invent fields; use only published schemas.
- Prefer declared resolution over inferred behavior.

================================================================================
A) JSON-LD CONTEXT MAP (TERM MAPPINGS)

Source: https://msp-1.org/schema/msp-1-site.json

Namespace prefixes:
- msp: https://msp-1.org/ns/
- xsd: http://www.w3.org/2001/XMLSchema#

Core mappings:
- protocol -> msp:protocol
- version -> msp:version
- site -> msp:site
- page -> msp:page
- id -> @id
- type -> @type
- title -> msp:title
- name -> msp:name
- description -> msp:description
- url -> { @id: msp:url, @type: @id }
- intent -> msp:intent
- authority -> msp:authority
- provenance -> msp:provenance
- interpretiveFrame -> msp:interpretiveFrame
- canonical -> { @id: msp:canonical, @type: @id }

Discovery mappings:
- discovery -> msp:discovery
- wellKnown -> { @id: msp:wellKnown, @type: @id }

Structural mappings:
- parent -> { @id: msp:parent, @type: @id }
- section -> { @id: msp:section, @type: @id }

Lifecycle & trust mappings:
- revision -> msp:revision
- revisionVersion -> msp:revisionVersion
- revisionDate -> { @id: msp:revisionDate, @type: xsd:dateTime }
- revisionNotes -> msp:revisionNotes
- compliance -> msp:compliance
- trust -> msp:trust
- author -> msp:author
- reviewer -> msp:reviewer
- role -> msp:role

NOTE
- This section defines JSON-LD term resolution only.
- Term presence here does NOT imply requirement.
- Required vs optional status is defined by each term’s JSON Schema.

================================================================================
B) TERM-LEVEL JSON SCHEMAS (VALIDATION STRUCTURES)

Format:
- $id
- required[]
- key properties (types/enums/formats)
- additionalProperties

------------------------------
discovery
$id: https://msp-1.org/schema/discovery/discovery.json
required: []
properties:
- wellKnown: string(uri-reference)
- canonical: boolean
additionalProperties: false

Rules:
- The canonical MSP-1 discovery endpoint is `/.well-known/msp.json`.
- If present, discovery.wellKnown MUST resolve to the canonical endpoint.
- Absence of discovery does NOT invalidate MSP-1 metadata.

------------------------------
title
$id: https://msp-1.org/schema/title/title.json
required: [value]
properties:
- value: string
- language: string
- source: enum(document, og, twitter, fallback)
additionalProperties: false

Rules:
- Represents the document title of a page.
- Required for page-level entities.
- Must reflect observable document metadata or a deterministic fallback.
- Must not be paraphrased or promotional.

------------------------------
author
$id: https://msp-1.org/schema/author/author.json
required: [name, type]
properties:
- name: string
- type: enum(Person, Organization)
- id: string(uri)
- role: string
- email: string(email)
- verification: object
  - level: enum(self-declared, verified, authoritative)
  - verifiedBy: string(uri)
additionalProperties: false

------------------------------
authority
$id: https://msp-1.org/schema/authority/authority.json
required: [name, type]
properties:
- name: string
- type: enum(Person, Organization, System)
- id: string(uri)
- scope: string
- jurisdiction: string
- role: string
- verification: object
  - level: enum(self-declared, verified, authoritative)
  - verifiedBy: string(uri)
additionalProperties: false

------------------------------
canonical
$id: https://msp-1.org/schema/canonical/canonical.json
required: [url]
properties:
- url: string(uri)
- reason: string
- lastReviewed: string(date)
- authority: string(uri)
- verification: object
  - level: enum(self-declared, verified, authoritative)
  - verifiedBy: string(uri)
additionalProperties: false

Rules:
- Canonical identifies the preferred URL for de-duplication.
- Canonical does not imply trust or authority.

------------------------------
description
$id: https://msp-1.org/schema/description/description.json
required: [short]
properties:
- short: string
- long: string
- alt: array[string]
- interpretiveFrame: string
- language: string
- lastUpdated: string(date)
additionalProperties: false

------------------------------
intent
$id: https://msp-1.org/schema/intent/intent.json
required: [statement]
properties:
- statement: string
- category: enum(informational, transactional, navigational, instructional, persuasive, creative, analytical, warning, meta)
- audience: string
- scope: string
- motivation: string
- interpretiveFrame: string
- priority: enum(low, medium, high, critical)
- lastUpdated: string(date)
additionalProperties: false

------------------------------
interpretiveFrame
$id: https://msp-1.org/schema/interpretiveFrame/interpretiveFrame.json
required: [frame]
properties:
- frame: string
- category: enum(informational, analytical, creative, speculative, instructional, opinion, contextual-disclaimer, safety, meta, other)
- tone: string
- perspective: string
- scope: string
- appliesToIntent: boolean
- notes: string
- lastUpdated: string(date)
additionalProperties: false

================================================================================
C) INDEX-LISTED SCHEMAS (AUTHORITATIVE VIA URL)

These schemas are indexed but not expanded here. Fetch directly when compiling
local or offline references.

- compliance: https://msp-1.org/schema/compliance/compliance.json
- page: https://msp-1.org/schema/page/page.json
- protocol: https://msp-1.org/schema/protocol/protocol.json
- provenance: https://msp-1.org/schema/provenance/provenance.json
- revisionDate: https://msp-1.org/schema/revisionDate/revisionDate.json
- site: https://msp-1.org/schema/site/site.json
- trust: https://msp-1.org/schema/trust/trust.json
- type: https://msp-1.org/schema/type/type.json
- url: https://msp-1.org/schema/url/url.json
- version: https://msp-1.org/schema/version/version.json

NOTE
- Page schema requires `id`, `url`, and `title`.
- Omission of required fields invalidates page-level metadata.

# END OF MACHINE-FIRST SCHEMA REFERENCE
