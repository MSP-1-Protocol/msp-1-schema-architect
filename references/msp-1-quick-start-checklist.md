# MSP-1 Quick Start Implementation Checklist

Status: Guidance (non-normative)  
Audience: Site owners, developers, tool users  
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

☐ If homepage or site root:
- ☐ Generate inline page-level MSP-1
- ☐ Generate site-level MSP-1 for `/.well-known/msp.json`

☐ Ensure output contains (at minimum):
- ☐ `protocol` (name + version)
- ☐ `page` or `site` entity
- ☐ `page.title` (required for pages)
- ☐ `page.url`
- ☐ `page.id`
- ☐ `page.canonical` (recommended for pages)
- ☐ `intent`
- ☐ `provenance`
- ☐ `revision`
- ☐ `trust` (default to conservative)
- ☐ `discovery` (recommended)

☐ If `discovery` is included:
- ☐ `discovery.wellKnown` set to `/.well-known/msp.json`
- ☐ `discovery.canonical` set to `true`

Note: Older implementations without `discovery` remain valid.

---

## C) Human Review (Required)

☐ Review high-risk fields carefully:
- ☐ `intent` (is the purpose stated correctly?)
- ☐ `interpretiveFrame` (factual vs opinion vs editorial?)
- ☐ `authority` (scope and level truthful?)
- ☐ `trust` (not overstated?)
- ☐ `provenance` (AI involvement disclosed if applicable?)

☐ Review title and canonical handling:
- ☐ `page.title` reflects the actual document title
- ☐ `page.title` is not paraphrased or promotional
- ☐ `page.canonical` matches the intended preferred URL

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
- ☐ Conforms to MSP-1 schemas / namespace
- ☐ No invented or overloaded fields
- ☐ No missing required fields

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
