# MSP-1 Schema Architect

This repository contains the canonical definition of the **MSP-1 Schema Architect** role — a protocol-aware implementation responsible for generating valid, schema-compliant MSP-1 declarations from a URL or raw HTML input.

The Schema Architect is maintained independently of any specific LLM platform. It is available as a system prompt for direct model use and as a native skill file for platforms that support the Agent Skills standard.

> This repository was initiated with ChatGPT and has been developed collaboratively across multiple frontier models — reflecting the model-agnostic philosophy at the core of the MSP-1 protocol itself.

---

## Repository Structure

```
msp-1-schema-architect/
├── SYSTEM_PROMPT.md      — Role definition for use with any LLM via direct prompt
├── SKILL.md              — Native skill file for platforms supporting the Agent Skills standard
└── references/           — Shared reference documents used by both implementations
    ├── MSP-1_Technical_Spec.md
    ├── msp-1-core-spec_machine.md
    ├── msp-1-schema_machine.md
    ├── msp-1-namespace_machine.md
    ├── msp-1-implementation-best-practices.md
    ├── msp-1-common-implementation-mistakes.md
    ├── msp-1-quick-start-checklist.md
    ├── msp-1-inline-example.json
    └── msp-example.json
```

The `references/` folder is model-agnostic. Both `SYSTEM_PROMPT.md` and `SKILL.md` draw from the same shared reference layer — the protocol spec documents that define correct MSP-1 behavior regardless of platform.

---

## Implementations

### System Prompt (`SYSTEM_PROMPT.md`)
A direct prompt definition of the Schema Architect role. Use this with any LLM — frontier models, open-source models, or locally run models — by loading it as a system prompt alongside the `references/` documents.

Public hosted implementations:
- **ChatGPT**: [msp-1.org/gpt](https://msp-1.org/gpt)
- **Google Gemini**: [msp-1.org/gem](https://msp-1.org/gem)

### Skill File (`SKILL.md`)
A native implementation for platforms supporting the [Agent Skills open standard](https://code.claude.com/docs/en/skills). The skill auto-invokes when relevant and loads reference files on demand.

Public hosted implementation:
- **Claude**: [msp-1.org/tools](https://msp-1.org/tools)

Download the packaged skill: [`msp-1-generator.zip`](https://msp-1.org/tools/msp-1-generator.zip)

**To install on Claude:**
1. Download and unzip `msp-1-generator.zip`
2. Go to **Settings → Customize → Skills** in Claude
3. Upload the `msp-1-generator` folder
4. The skill activates automatically — prompt Claude with any URL or HTML to generate MSP-1 output

> Requires Claude Pro, Max, Team, or Enterprise with Code Execution enabled.

---

## What the Schema Architect Does

- Accepts a URL or raw HTML as input
- Generates page-level MSP-1 as JSON-LD (for embedding in `<head>`)
- Generates site-level MSP-1 as raw JSON (for deployment at `/.well-known/msp.json`)
- Enforces validator-gate rules and schema field constraints
- Applies conservative defaults per protocol best practices
- Flags inferred fields and recommends human review before publishing

---

## Scope

- Defines the Schema Architect role and behavioral guardrails
- Provides reference implementations across multiple LLM platforms
- Serves as a starting point for adaptation to additional environments

## Out of Scope

- Protocol definition (see [`msp-1-spec`](https://msp-1.org/spec/))
- Canonical schemas (see [`msp-1-schemas`](https://github.com/MSP-1-Protocol/msp-1-schemas))
- Validation enforcement or governance

---

## References

- MSP-1 Schemas: [github.com/MSP-1-Protocol/msp-1-schemas](https://github.com/MSP-1-Protocol/msp-1-schemas)
- MSP-1 Documentation: [msp-1.org](https://msp-1.org)
- MSP-1 Validator: [msp-1.org/validator](https://msp-1.org/validator/)

---

Licensed under the Apache License 2.0.
