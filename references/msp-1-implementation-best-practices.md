# MSP-1 Implementation Best Practices

Status: Guidance (non-normative)  
Audience: Site owners, implementers, tool builders  
Protocol baseline: MSP-1 v1.0.2  
Last updated: 2026-09-06  

---

## 1) MSP-1 is a declaration layer, not an autopilot
MSP-1 is designed to reduce ambiguity for humans and AI agents by declaring intent, provenance, trust, and authority signals in a consistent way.

MSP-1 does not guarantee correctness. It increases interpretability.

---

## 2) LLM-generated MSP-1 requires human review
When an LLM (or any automated tool) generates MSP-1 metadata from raw content, it must infer meaning from the page. That inference can be imperfect.

**Best practice:** Treat generated MSP-1 as a *first draft*.

- Review it for accuracy, scope, tone, and unintended claims.
- Edit and fine-tune before publishing.
- Do not publish metadata you do not endorse as true.

Suggested workflow:
1. Generate MSP-1 from URL or HTML
2. Review fields with highest risk of misrepresentation:
   - intent  
   - interpretiveFrame  
   - authority  
   - trust  
   - provenance  
3. Correct scope, wording, and confidence
4. Publish
5. Re-validate after deployment

---

## 3) Prefer conservative declarations over ambitious ones
When uncertain, choose the most conservative truthful value.

Examples:
- Trust: do not emit a trust declaration by default. If supported, use `level` (`low`, `medium`, or `high`) conservatively and keep verification separate through `verificationLevel` (`self-declared` or `verified`).
- Authority: emit only when supplied, requested, or clearly supported; identify the responsible person, organization, or system and keep the claim scope-bound.
- Provenance: disclose AI involvement when applicable.

---

## 4) Separate facts, intent, and interpretation
To reduce downstream ambiguity:
- Use `description` for concise factual summaries.
- Use `intent` for the purpose of the page or site.
- Use `interpretiveFrame` to declare how the content should be read (e.g., factual, editorial, speculative, satirical).

Avoid mixing opinion into factual framing.

---

## 5) Keep identifiers and URLs stable
MSP-1 works best when `id` and `url` remain stable.

- Use canonical URLs when possible.
- Avoid changing IDs across revisions.
- If URLs change, use redirects and the `canonical` object consistently.
- In v1.0.2, `canonical` should be expressed as an object containing `url`.

---

## 6) Use revision metadata as a change log
When MSP-1 changes, record why.

- Include `revisionDate` and `revisionNotes`.
- Use `revisionVersion` for semantic version tracking.
- Treat revisions as auditable events, not cosmetic updates.

---

## 7) Publish site-level MSP-1 for discovery
For baseline adoption:

- Publish `/.well-known/msp.json` as the canonical MSP-1 discovery endpoint.
- Publish page-level inline MSP-1 JSON-LD on key pages (or all pages where feasible).

Site-level declarations establish identity and default posture; pages may override as needed.

Where supported, include the `discovery` object to explicitly declare the canonical discovery endpoint and reduce inference-based behavior.

Older implementations without `discovery` remain valid.

---

## 8) Keep JSON-LD context and validation schema separate
MSP-1 v1.0.2 uses different resources for semantic resolution and structural validation.

- Page context: `https://msp-1.org/context/msp-1-page.jsonld`
- Site context: `https://msp-1.org/context/msp-1-site.jsonld`
- Page schema: `https://msp-1.org/schema/msp-1-page.json`
- Site schema: `https://msp-1.org/schema/msp-1-site.json`

Use `/context/` resources as `@context`. Use `/schema/` resources for validation. Do not interchange them in new v1.0.2 declarations.

---

## 9) Prefer declared discovery over inferred discovery
Automated agents and tooling should not rely on filename or path inference to locate MSP-1 declarations.

**Best practice:**
- Declare discovery explicitly.
- Prefer deterministic resolution over heuristic probing.
- Avoid alternate or inferred filenames.

Inference increases variance and unnecessary processing.

---

## 10) Validate, then spot-check
Validation catches structural errors; it cannot catch semantic dishonesty.

Best practice:
- Validate JSON structure (schema compliance).
- Spot-check semantics (truth, scope, and unintended claims).
- Periodically audit high-impact pages.

---

## 11) Don’t treat MSP-1 as SEO markup
MSP-1 is not a ranking mechanism.

- Avoid promotional language in `description`, `trust`, and `authority`.
- Write for fidelity and clarity, not persuasion.

---

## 12) Treat compliance as deprecated compatibility metadata
New v1.0.2 declarations should not emit `compliance`.

- When reviewing legacy input, identify `compliance` as deprecated or advisory.
- Do not automatically convert compliance claims into trust or authority.
- Preserve meaningful legacy information only when it can be mapped truthfully to an active field without changing meaning.

---

## 13) Misuse harms the ecosystem
Overstating trust, authority, or provenance undermines MSP-1 adoption.

If you cannot support a claim, do not declare it.

---

## 14) Tooling notes for GPT and validator builders
Status: Guidance (non-normative)

### 14.1 Content extraction limitations
Automated tools may not have access to fully rendered DOMs (e.g., heavy JavaScript, client-side routing, paywalls).

Best practice:
- Prefer static HTML where available.
- If key metadata cannot be confidently extracted, tools should:
  - Request pasted HTML, OR
  - Generate conservative MSP-1 with uncertainty noted.

Tools must not assume missing content implies absence of intent or authority.

---

### 14.2 Missing or incomplete `<head>` elements
Pages without `<head>` metadata increase inference risk.

Best practice:
- Derive optional `name`, `description`, and `intent` from visible content only when necessary and supported.
- Do not invent a `page.title` field; the v1.0.2 page minimum is `id` and `url`.
- Explicitly note uncertainty in `revisionNotes` when head metadata is absent.

---

### 14.3 Handling inference and uncertainty
All automated MSP-1 generation involves inference.

Tooling SHOULD:
- Default to conservative values.
- Avoid speculative claims about authority or trust.
- Record uncertainty using `revisionNotes` or equivalent explanatory fields.

Example:
> “Certain declarations were inferred from visible content and may require human review.”

---

### 14.4 Revision metadata for generated output
When a tool generates MSP-1:
- Treat generation as a revision event.
- Populate `revisionDate`.
- Use `revisionNotes` to disclose automated generation.
- Use `ai-assisted` provenance for the generated declaration context when applicable.

This supports auditability and transparency.

---

### 14.5 Validation scope
Validators should distinguish between:
- **Structural validity** (schema compliance)
- **Semantic plausibility** (truth, scope, intent alignment)

Only structural validity can be automated reliably.  
Semantic evaluation must remain advisory.

---

### 14.6 Fail safely (required vs optional fields)
If **optional** information cannot be derived without guesswork:
- Prefer omission over fabrication.
- Omit trust unless it is supported; if emitted, keep `level` conservative and verification status separate.
- Omit interpretive framing unless a neutral frame is supported by the source.

If **required** information cannot be derived:
- Tools MUST halt generation
- Tools MUST NOT emit partial MSP-1 metadata
- Tools SHOULD request additional input (e.g., pasted HTML)

Silent omission of required fields is not a safe failure mode.

For v1.0.2:
- A page entity requires `id` and `url`.
- A site entity requires `id`, `name`, and `url`.
- A complete declaration also requires the matching `@context` and a `protocol` object identifying MSP-1 v1.0.2.

---

## 15) Treat generation as compilation, not “best effort”
MSP-1 generation tools should behave like compilers, not content assistants.

Best practice:
- Required fields must always be emitted
- Invalid or incomplete metadata must not be published
- Deterministic output is preferred over creative recovery

A hard failure is safer than a misleading declaration.

---

## 16) Extend by namespace, not by altering the core
MSP-1 extensions may add optional semantic structures without expanding or redefining the minimal core.

Best practice:
- Use a separate, durable extension namespace.
- Do not place extension terms in the MSP-1 core namespace.
- Do not redefine or overload core terms.
- Document the extension context, schema, version, compatibility, and processing behavior.
- Ensure the core MSP-1 declaration remains useful when the extension is ignored.
- Treat experimental extensions as optional and non-core.

Undocumented local fields are not an MSP-1 extension.
