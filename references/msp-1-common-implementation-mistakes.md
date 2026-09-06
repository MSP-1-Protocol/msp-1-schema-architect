# MSP-1 Common Implementation Mistakes

Status: Guidance (non-normative)  
Audience: Site owners, developers, tool builders  
Protocol baseline: MSP-1 v1.0.2  
Last updated: 2026-09-06  
Purpose: Prevent avoidable errors and misuse

---

## 1) Treating MSP-1 as SEO markup
**Mistake:** Writing MSP-1 declarations to influence rankings or promotion.

**Why it’s a problem:**  
MSP-1 is not designed for ranking manipulation. Over-promotional language increases ambiguity and reduces trust.

**Better approach:**  
Use neutral, factual language. Declare what *is*, not what you hope systems will do.

---

## 2) Overstating trust or authority
**Mistake:** Declaring `authoritative`, `verified`, or “official” status without independent support.

**Why it’s a problem:**  
Inflated trust claims undermine the entire trust signal layer.

**Better approach:**  
Do not emit trust by default. When trust context is supported, use `level` (`low`, `medium`, or `high`) conservatively. Express verification separately through `verificationLevel` (`self-declared` or `verified`). Keep authority in the `authority` term rather than encoding it as a trust level.

---

## 3) Letting LLM output ship unreviewed
**Mistake:** Publishing auto-generated MSP-1 without human review.

**Why it’s a problem:**  
All automated generation involves inference. Unchecked output may misrepresent intent, tone, or authority.

**Better approach:**  
Treat generated MSP-1 as a draft. Review and edit before deployment.

---

## 4) Mixing facts, opinion, and framing
**Mistake:** Using factual `description` fields for opinion or editorial framing.

**Why it’s a problem:**  
It blurs interpretation boundaries for downstream agents.

**Better approach:**  
- Facts → `description`  
- Purpose → `intent`  
- Lens or tone → `interpretiveFrame`  

Keep each concern isolated.

---

## 5) Scope leakage (site vs page)
**Mistake:** Declaring site-wide authority or trust in a page-level block.

**Why it’s a problem:**  
It creates false inheritance and misleads interpretation.

**Better approach:**  
Use `/.well-known/msp.json` for site defaults. Override only where necessary at the page level.

---

## 6) Unstable or changing identifiers
**Mistake:** Changing `id` values across revisions or pages.

**Why it’s a problem:**  
Unstable identifiers break continuity and reduce reliability.

**Better approach:**  
Choose IDs once and keep them stable. Use revision metadata to record changes instead.

---

## 7) Omitting revision metadata
**Mistake:** Updating MSP-1 without recording why or when.

**Why it’s a problem:**  
It removes auditability and obscures content lifecycle.

**Better approach:**  
Always update `revisionDate` and `revisionNotes` when MSP-1 changes.

---

## 8) Inventing or overloading MSP-1 core terms
**Mistake:** Adding undocumented fields to the MSP-1 core namespace or redefining existing terms to fit local needs.

**Why it’s a problem:**  
It breaks interoperability and tool compatibility.

**Better approach:**  
Use only defined MSP-1 core terms in a core declaration. If additional semantics are needed, use a documented, optional extension with its own namespace, context, schema, version, and graceful-degradation behavior. Do not present local fields as MSP-1 core.

---

## 9) Assuming missing data implies meaning
**Mistake:** Treating absent fields as intentional signals.

**Why it’s a problem:**  
Absence often reflects extraction or tooling limitations, not intent.

**Better approach:**  
Interpret missing fields conservatively. Do not infer intent or trust from silence.

---

## 10) Treating validation as semantic truth
**Mistake:** Assuming schema-valid MSP-1 is semantically correct.

**Why it’s a problem:**  
Validation checks structure, not honesty, scope accuracy, or intent alignment.

**Better approach:**  
Use validation as a baseline check. Apply human judgment for meaning and truth.

---

## 11) Publishing MSP-1 without canonical discovery
**Mistake:** Adding page-level MSP-1 declarations without a clear, canonical discovery anchor.

**Why it’s a problem:**  
Without an explicit discovery signal, downstream systems may rely on inference or heuristic probing, which can lead to inconsistent resolution.

**Better approach:**  
Publish `/.well-known/msp.json` as the canonical discovery endpoint.  
Where supported, include the `discovery` object to make this explicit and reduce inference-based behavior.

*Note: Older implementations without `discovery` remain valid.*

---

## 12) Relying on inferred discovery paths
**Mistake:** Assuming automated agents will correctly infer MSP-1 discovery locations based on naming patterns.

**Why it’s a problem:**  
Inference introduces variance. Different systems may probe different filenames or paths, increasing ambiguity and unnecessary processing.

**Better approach:**  
Declare discovery explicitly. Prefer deterministic resolution over pattern completion.  
Use the canonical endpoint and avoid alternate or inferred filenames.

---

## 13) Treating MSP-1 as “set and forget”
**Mistake:** Publishing MSP-1 once and never revisiting it.

**Why it’s a problem:**  
Content evolves; meaning changes.

**Better approach:**  
Revisit MSP-1 whenever:
- Content intent changes  
- Editorial posture shifts  
- Ownership or authority changes  

---

## 14) Omitting required page or site fields
**Mistake:** Publishing a page without `page.id` and `page.url`, or a site without `site.id`, `site.name`, and `site.url`.

**Why it’s a problem:**  
Missing required fields cause schema validation failure and force downstream systems to fall back to inference.

**Better approach:**  
Ensure the correct required fields are present for the declared scope. `page.name` is optional and may be added as an accurate human-readable label. Do not invent or require `page.title` in new v1.0.2 output.

---

## 15) Treating generation tools as “best effort” instead of deterministic
**Mistake:** Allowing MSP-1 generators to emit partial, inferred, or schema-incomplete output.

**Why it’s a problem:**  
Partial output creates silent failures that look valid but are not reliably interpretable.

**Better approach:**  
Treat MSP-1 generation like compilation:
- Required fields must always be emitted  
- Missing data should halt output or be explicitly documented  
- Determinism is preferred over creativity  

---

## 16) Using a schema URL as `@context`
**Mistake:** Using `https://msp-1.org/schema/msp-1-page.json` or `https://msp-1.org/schema/msp-1-site.json` as the JSON-LD context for new v1.0.2 output.

**Why it’s a problem:**  
JSON-LD context resolution and JSON Schema validation are separate functions in v1.0.2. A schema URL is not the active semantic context resource.

**Better approach:**  
Use the matching `/context/` JSON-LD resource in `@context`, and use the matching `/schema/` resource only for structural validation. Treat schema-as-context usage in v1.0.1 declarations as legacy compatibility input rather than silently copying it into new output.

---

## 17) Emitting deprecated compliance metadata
**Mistake:** Adding `compliance` to a new v1.0.2 declaration or treating it as a required conformance signal.

**Why it’s a problem:**  
`compliance` is deprecated compatibility metadata. Reusing it can blur the distinction between validation, trust, and authority.

**Better approach:**  
Omit `compliance` from new output. When reviewing legacy declarations, surface it as an advisory and preserve meaningful information only when it maps truthfully to an active field without changing meaning.

---

## Final Reminder
MSP-1 rewards restraint, clarity, and honesty.

When unsure:
- Declare less, not more  
- Prefer conservative truth over confident error  
- Remember that misuse by a few harms adoption for many
