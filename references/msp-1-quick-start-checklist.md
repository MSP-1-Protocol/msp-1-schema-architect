# MSP-1 Quick Start Implementation Checklist

Status: Guidance (non-normative)  
Audience: Site owners, developers, tool users  
Protocol baseline: MSP-1 v1.0.2  
Last updated: 2026-09-06  
Goal: Baseline MSP-1 adoption with minimal risk and minimal inference

---

## A) Preparation (Before Generating Anything)

☐ Identify the target:
- ☐ Single page
- ☐ Homepage / site root
- ☐ Entire site (progressive rollout)

☐ Confirm canonical URLs:
- ☐ Final URL structure known
- ☐ Redirects resolved
- ☐ Canonical URL chosen per page

☐ Decide scope:
- ☐ Page-level only
- ☐ Site-level only
- ☐ Both (recommended for homepage)

☐ Confirm discovery location:
- ☐ `/.well-known/msp.json` will be used as the canonical discovery endpoint
- ☐ No alternate or inferred discovery paths planned

---

## B) Generate MSP-1 Metadata

☐ Generate MSP-1 using:
- ☐ URL input, or
- ☐ Pasted HTML source

☐ Use the context that matches declaration scope:
- ☐ Page: `https://msp-1.org/context/msp-1-page.jsonld`
- ☐ Site: `https://msp-1.org/context/msp-1-site.jsonld`
- ☐ Do not use `/schema/` URLs as `@context` in new v1.0.2 declarations

☐ If homepage or site root:
- ☐ Generate inline page-level MSP-1
- ☐ Generate site-level MSP-1 for `/.well-known/msp.json`

☐ Ensure output contains the schema-required minimum:
- ☐ `@context`
- ☐ `protocol.name` set to `MSP-1`
- ☐ `protocol.version` set to `1.0.2`
- ☐ A `page` or `site` entity
- ☐ Page declaration: `page.id` and `page.url`
- ☐ Site declaration: `site.id`, `site.name`, and `site.url`

☐ Add recommended or contextual fields only when supported:
- ☐ `page.name` (optional human-readable page label)
- ☐ `description`
- ☐ `intent`
- ☐ `interpretiveFrame`
- ☐ `canonical` using object form with `url`
- ☐ `provenance`
- ☐ `revision`
- ☐ `trust` only when supported; do not emit it by default
- ☐ `discovery` (recommended)

☐ If `discovery` is included:
- ☐ `discovery.wellKnown` set to `/.well-known/msp.json`
- ☐ `discovery.canonical` set to `true`

Note: Older implementations without `discovery` remain valid.

☐ Confirm deprecated fields are not emitted:
- ☐ No `compliance` in new v1.0.2 output

---

## C) Human Review (Required)

☐ Review high-risk fields carefully:
- ☐ `intent` (is the purpose stated correctly?)
- ☐ `interpretiveFrame` (factual vs opinion vs editorial?)
- ☐ `authority` (identity and scope truthful?)
- ☐ `trust` (supported, not overstated, and distinct from authority?)
- ☐ `provenance` (AI involvement disclosed if applicable?)

☐ Review name and canonical handling:
- ☐ `page.name`, if present, is accurate and non-promotional
- ☐ `canonical`, if present, is an object containing the intended preferred `url`

☐ Review trust handling when present:
- ☐ `trust.level` is `low`, `medium`, or `high`
- ☐ `trust.verificationLevel`, if present, is `self-declared` or `verified`
- ☐ `verified` is not claimed without support

☐ Review discovery clarity:
- ☐ No reliance on inferred filenames
- ☐ No alternate well-known paths declared
- ☐ Discovery intent is explicit and unambiguous

☐ Edit wording for:
- ☐ Accuracy
- ☐ Scope correctness
- ☐ Neutral tone
- ☐ Absence of promotional language

☐ Remove or downgrade anything you cannot confidently support.

---

## D) Revision & Transparency

☐ Treat generation or editing as a revision event:
- ☐ `revisionDate` set (ISO 8601)
- ☐ `revisionNotes` explain what was generated or changed
- ☐ `revisionVersion` incremented if updating existing MSP-1

☐ If MSP-1 was auto-generated:
- ☐ Disclose this in `revisionNotes`

☐ If discovery was added or updated:
- ☐ Note this explicitly in `revisionNotes`

---

## E) Validate

☐ Validate JSON:
- ☐ Valid JSON syntax
- ☐ Conforms to the matching v1.0.2 page or site schema
- ☐ Resolves through the matching v1.0.2 JSON-LD context
- ☐ No invented, redefined, or overloaded MSP-1 core terms
- ☐ Any extension terms are optional, separately namespaced, documented, and gracefully degradable
- ☐ No missing required fields
- ☐ No deprecated `compliance` field in new output

☐ Spot-check semantics:
- ☐ No unintended claims
- ☐ No scope leakage (page vs site)
- ☐ No internal contradictions
- ☐ Discovery endpoint matches actual deployment

---

## F) Deploy

☐ Inline MSP-1:
- ☐ Embedded as JSON-LD in page markup
- ☐ One block per page

☐ Site-level MSP-1:
- ☐ Deployed at `/.well-known/msp.json`
- ☐ Publicly accessible (HTTP 200)
- ☐ Not blocked by robots, auth, or CDN rules

☐ Confirm:
- ☐ No Schema.org dependency implied
- ☐ MSP-1 stands alone
- ☐ Discovery does not rely on inference or probing
- ☐ Ignoring any optional extension does not impair the core declaration

---

## G) Post-Deployment

☐ Re-check after publishing:
- ☐ Page loads correctly
- ☐ JSON accessible to crawlers and agents
- ☐ No caching or CDN issues affecting `.well-known`

☐ Plan maintenance:
- ☐ Revisit MSP-1 when content meaning changes
- ☐ Update revisions when intent or framing shifts
- ☐ Periodically audit high-impact pages

---

## H) Adoption Mindset

☐ Prefer clarity over completeness  
☐ Prefer deterministic declaration over inference  
☐ Prefer conservative truth over confident error  
☐ Prefer hard failure over partial or misleading metadata  
☐ Prefer human review over automation-only workflows  

MSP-1 is a declaration of intent — treat it as such.
