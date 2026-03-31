# MSP-1 Core Specification — Machine-First Reference

Protocol: MSP-1  
Status: Stable Core  
Scope: Normative  

---

## GLOBAL RULES
- MSP-1 metadata MUST be truthful.
- MSP-1 fields are scope-bound.
- **Required fields MUST be present for schema-valid metadata.**
- **Omission of required fields invalidates the affected scope.**
- Omission of optional fields reduces confidence but does not invalidate metadata.
- Conflicting fields constitute a structural trust failure.
- MSP-1 is schema-agnostic and independent of Schema.org.
- MSP-1 discovery MUST be deterministic and non-inferential.

---

## DISCOVERY

### discovery
Optional (recommended):
- discovery.wellKnown
- discovery.canonical

Rules:
- The canonical MSP-1 discovery endpoint is `/.well-known/msp.json`.
- The `discovery` object MAY be included to explicitly declare this endpoint.
- If present, `discovery.wellKnown` MUST resolve to the canonical endpoint.
- Consumers SHOULD prefer declared discovery over inferred paths.
- Absence of `discovery` does NOT invalidate MSP-1 metadata.

---

## CORE ENTITIES

### site
Required:
- site.id
- site.name
- site.url

Optional:
- site.description
- site.intent
- site.provenance
- site.authority
- site.trust

Rules:
- Site metadata cascades to pages unless overridden.
- Site definitions SHOULD be published at `/.well-known/msp.json`.
- Conflicting site metadata is a critical trust failure.

---

### page
Required:
- page.id
- page.url
- **page.title**

Optional:
- page.canonical
- page.name
- page.description
- page.intent
- page.interpretiveFrame

Rules:
- `page.title` represents the document title and MUST be present.
- `page.title` MUST reflect the observable document title or a conservative deterministic fallback.
- `page.canonical` SHOULD be provided to enable URL de-duplication and stable resolution.
- Page inherits site context unless explicitly overridden.
- Page scope is limited to the declared URL.

---

## IDENTITY & TRUST

### author
Required:
- author.name
- author.id

Optional:
- author.role
- author.url
- author.provenance

Rules:
- Primary attribution unless context specifies otherwise.
- Missing author lowers provenance confidence.

---

### reviewer
Required:
- reviewer.name
- reviewer.id

Optional:
- reviewer.role
- reviewer.scope
- reviewer.reviewDate
- reviewer.notes

Rules:
- Reviewers are independent from authors.
- Reviewed content carries higher trust weighting.

---

### authority
Required:
- authority.subjectId
- authority.scope
- authority.level

Optional:
- authority.evidence
- authority.jurisdiction
- authority.effectiveDate

Rules:
- Authority is always scope-bound.
- official > expert > advisory within the same scope.

---

### trust
Required:
- trust (string or object)

Recommended structure:
- trust.level (self-asserted | verified | authoritative)
- trust.scope
- trust.provenance
- trust.reviewer
- trust.confidence

Rules:
- Trust cascades unless overridden.
- Conflicts between trust and provenance MUST be flagged.
- Missing trust is neutral, not negative.

---

### provenance
Required:
- provenance.type

Allowed values:
- original
- derived
- aggregated
- ai-assisted
- ai-generated

Optional:
- provenance.source
- provenance.contributors
- provenance.notes
- provenance.confidence
- provenance.timestamp

Rules:
- Provenance is a primary trust signal.
- AI-generated content requires additional caution.

---

### role
Required:
- role.role

Optional:
- role.id
- role.description
- role.scope

Rules:
- Roles contextualize identity and authority.
- Conflicting roles reduce trust.

---

## CONTENT & INTENT

### name
Required:
- name

Rules:
- Names should be stable and unambiguous.

---

### description
Required:
- description

Rules:
- Prefer concise, scope-true language.

---

### intent
Required:
- intent

Rules:
- Intent constrains interpretation and reuse.

---

### interpretiveFrame
Required:
- interpretiveFrame

Rules:
- Defines assumptions or lens for interpretation.
- Must be applied when evaluating claims.

---

## STRUCTURE

### parent
Required:
- parent

Rules:
- Defines hierarchical relationship.
- Inconsistencies reduce structural trust.

---

### section
Required:
- section

Rules:
- Sections enable granular interpretation and trust.

---

### type
Required:
- type

Rules:
- Type informs classification and handling.

---

## LIFECYCLE & VERSIONING

### canonical
Required:
- canonical.url

Optional:
- canonical.scope
- canonical.reason

Rules:
- Canonical defines authoritative representation.
- Canonical does not imply trust.

---

### revision
Required:
- revision.id

Optional:
- revision.revisionDate
- revision.revisionNotes
- revision.revisionVersion
- revision.reviewer
- revision.provenance

Rules:
- Each revision is a discrete event.
- Conflicting revision order is an error.

---

### revisionDate
Required:
- ISO 8601 timestamp

Rules:
- Used for freshness and ordering.

---

### revisionNotes
Required:
- plain-text string

Rules:
- Must truthfully describe the change.

---

### revisionVersion
Required:
- semantic version string (MAJOR.MINOR.PATCH)

Rules:
- Must align with revision ordering.

---

### version
Required:
- semantic version string

Rules:
- Newer versions supersede older ones.

---

## PROTOCOL

### protocol
Required:
- protocol = "MSP-1"
- version (semantic)

Optional:
- supportedVersionRange

Rules:
- Conflicting protocol declarations are invalid.
- The protocol name “MSP-1” is not a version indicator.

---

## URL & ID

### url
Required:
- absolute URL preferred

Rules:
- Stability increases confidence.

---

### id
Required:
- stable identifier

Rules:
- ID instability reduces reliability.

---

## COMPLIANCE

### compliance
Required:
- compliance.core (boolean)

Optional:
- compliance.verified
- compliance.authoritative
- compliance.versionRange

Rules:
- Compliance is self-declared.
- Declared compliance may be cross-checked.

---

# END OF MACHINE REFERENCE
