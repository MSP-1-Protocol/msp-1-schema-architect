# MSP-1 Schema Architect — Reference Documents

This folder contains the shared reference documents used by both the `SYSTEM_PROMPT.md` and `SKILL.md` implementations of the MSP-1 Schema Architect.

These files are model-agnostic. They represent the protocol knowledge layer that any compliant Schema Architect implementation — regardless of platform or model — draws from to generate valid MSP-1 declarations.

## Contents

| File | Purpose |
|------|---------|
| `MSP-1_Technical_Spec.md` | Concise validator-gate rules and field contracts |
| `msp-1-core-spec_machine.md` | Required and optional fields per entity — normative |
| `msp-1-schema_machine.md` | Field types, enums, and schema validation structures |
| `msp-1-namespace_machine.md` | Canonical namespace identifiers |
| `msp-1-implementation-best-practices.md` | Guidance on inference, uncertainty, and correct usage |
| `msp-1-common-implementation-mistakes.md` | Known failure modes and how to avoid them |
| `msp-1-quick-start-checklist.md` | Checklist for complete, valid MSP-1 output |
| `msp-1-inline-example.json` | Reference example of a page-level declaration |
| `msp-example.json` | Reference example of a site-level declaration |

## Usage

- **With `SYSTEM_PROMPT.md`**: Load alongside the system prompt when running the Schema Architect on any LLM
- **With `SKILL.md`**: Loaded automatically by the skill on demand — no manual loading required
- **Custom integrations**: Use these files as the knowledge foundation for your own MSP-1 generator tooling

The MSP-1 specification and schemas remain the canonical source of protocol truth.
Further documentation at [msp-1.org](https://msp-1.org).
