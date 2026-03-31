# MSP-1 Protocol Specification (Validator-Safe)

## Core Requirement: Page-Level JSON-LD
- **@context**: MUST be "https://msp-1.org/schema/msp-1-page.json"
- **@type**: "MSPPage"

## Field Contracts
1. **page.title**: MANDATORY primitive string. (e.g., "Home | Provider")
2. **page.name**: OPTIONAL object. 
   - **Allowed Keys**: "short" (Required if object exists), "display" (Optional).
   - **Forbidden Keys**: "full", "long", "title".
3. **Identifiers**: `page.id`, `page.url`, and `page.canonical` are REQUIRED.
4. **Provenance**: MUST include `generatedAt` (ISO-8601) and `method`.

## Strict Constraints
- NEVER use a string for `page.name`.
- NEVER include unsupported keys in the `page.name` object.
- If a property is not in the source, use a deterministic fallback or omit if optional.