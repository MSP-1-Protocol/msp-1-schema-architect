# MSP-1 Namespace — Machine-First Reference

Protocol: MSP-1  
Status: Canonical  
Scope: Normative  
Namespace base: https://msp-1.org/ns/

---

## GLOBAL RULES
- Namespace terms define canonical meaning.
- Terms MUST NOT be redefined or overloaded.
- Absence of a term does not imply invalidity.
- Namespace terms are protocol-stable identifiers.
- MSP-1 namespace is schema-agnostic.

---

## CORE NAMESPACE TERMS

### author
Namespace ID:
- https://msp-1.org/ns/author

Meaning:
- Entity responsible for creating content.

Notes:
- May represent a human or organization.
- Primary attribution signal.

---

### authority
Namespace ID:
- https://msp-1.org/ns/authority

Meaning:
- Declares authoritative standing within a defined scope.

Notes:
- Always scope-bound.
- Does not imply global authority.

---

### canonical
Namespace ID:
- https://msp-1.org/ns/canonical

Meaning:
- Identifies the authoritative representation of a resource.

Notes:
- Used for de-duplication.
- Does not imply trust or authority.

---

### discovery
Namespace ID:
- https://msp-1.org/ns/discovery

Meaning:
- Declares the canonical discovery mechanism for MSP-1 metadata.

Notes:
- Used to explicitly identify the MSP-1 well-known endpoint.
- Reduces reliance on inferred or heuristic discovery behavior.
- Absence of `discovery` does not invalidate MSP-1 metadata.

---

### wellKnown
Namespace ID:
- https://msp-1.org/ns/wellKnown

Meaning:
- Specifies the canonical well-known location for MSP-1 discovery.

Notes:
- Typically expressed as a root-relative path.
- Must resolve to the authoritative MSP-1 discovery endpoint.
- Intended for use within the `discovery` object.

---

### compliance
Namespace ID:
- https://msp-1.org/ns/compliance

Meaning:
- Declares level of MSP-1 protocol implementation.

Notes:
- Self-declared.
- May be validated externally.

---

### description
Namespace ID:
- https://msp-1.org/ns/description

Meaning:
- Concise explanation of a resource.

Notes:
- Optimized for clarity and disambiguation.

---

### id
Namespace ID:
- https://msp-1.org/ns/id

Meaning:
- Stable identifier for a resource or entity.

Notes:
- Should persist across revisions.

---

### intent
Namespace ID:
- https://msp-1.org/ns/intent

Meaning:
- Declares why a resource exists.

Notes:
- Constrains interpretation and reuse.

---

### interpretiveFrame
Namespace ID:
- https://msp-1.org/ns/interpretiveFrame

Meaning:
- Declares assumptions or interpretive constraints.

Notes:
- Must be applied when evaluating claims.

---

### name
Namespace ID:
- https://msp-1.org/ns/name

Meaning:
- Human-readable display name for a resource.

Notes:
- Intended for presentation or labeling.
- MUST NOT replace `title` when a document title is required.

---

### title
Namespace ID:
- https://msp-1.org/ns/title

Meaning:
- Document title of a page or resource.

Notes:
- Represents the observable document title.
- MUST be present for page-level entities.
- MUST NOT be paraphrased or repurposed as a display label.
- Distinct from `name`, which is optional and presentation-oriented.

---

### page
Namespace ID:
- https://msp-1.org/ns/page

Meaning:
- Defines a web page as a scoped MSP-1 entity.

Notes:
- Inherits site context unless overridden.
- Page-level entities require a `title`.

---

### parent
Namespace ID:
- https://msp-1.org/ns/parent

Meaning:
- Declares hierarchical relationship.

Notes:
- Used for structural reasoning.

---

### protocol
Namespace ID:
- https://msp-1.org/ns/protocol

Meaning:
- Declares protocol identity and version.

Notes:
- Required to interpret MSP-1 metadata.
- The protocol name “MSP-1” is not a version indicator.

---

### provenance
Namespace ID:
- https://msp-1.org/ns/provenance

Meaning:
- Documents content origin and lineage.

Notes:
- Primary trust signal.

---

### reviewer
Namespace ID:
- https://msp-1.org/ns/reviewer

Meaning:
- Independent evaluator of content.

Notes:
- Distinct from author.

---

### revision
Namespace ID:
- https://msp-1.org/ns/revision

Meaning:
- Discrete update event.

Notes:
- Represents lifecycle changes.

---

### revisionDate
Namespace ID:
- https://msp-1.org/ns/revisionDate

Meaning:
- Timestamp of a revision.

Notes:
- ISO 8601 format required.

---

### revisionNotes
Namespace ID:
- https://msp-1.org/ns/revisionNotes

Meaning:
- Description of changes made.

Notes:
- Must be truthful and clear.

---

### revisionVersion
Namespace ID:
- https://msp-1.org/ns/revisionVersion

Meaning:
- Semantic version associated with a revision.

Notes:
- MAJOR.MINOR.PATCH format.

---

### role
Namespace ID:
- https://msp-1.org/ns/role

Meaning:
- Role held by an entity.

Notes:
- Contextualizes authority and responsibility.

---

### section
Namespace ID:
- https://msp-1.org/ns/section

Meaning:
- Subdivision of a page.

Notes:
- Enables granular interpretation.

---

### site
Namespace ID:
- https://msp-1.org/ns/site

Meaning:
- Canonical identity of a website.

Notes:
- Root semantic entity.

---

### trust
Namespace ID:
- https://msp-1.org/ns/trust

Meaning:
- Reliability and verification signal.

Notes:
- Interpreted relative to provenance and authority.

---

### type
Namespace ID:
- https://msp-1.org/ns/type

Meaning:
- Classification of a resource or entity.

Notes:
- Informs handling and reasoning.

---

### url
Namespace ID:
- https://msp-1.org/ns/url

Meaning:
- Canonical or identifying URL.

Notes:
- Stability increases confidence.

---

### version
Namespace ID:
- https://msp-1.org/ns/version

Meaning:
- Semantic version identifier.

Notes:
- Indicates state or compatibility.

---

# END OF NAMESPACE REFERENCE
