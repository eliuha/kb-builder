# Organization & Domain

> Config for the KB builder — fill every `<placeholder>` (run `prompts/setup.md` to be guided).
> Drives scope, structure, tone, and what must never appear. Generic / synthetic is fine.

## Organization
- Name: <organization name — generic is fine>
- What it does: <one or two lines>

## Domain & scope
- This KB covers: <the domain, e.g. "application-security requirements">
- In scope: <topics/areas included>
- Out of scope: <what to exclude>

## Audience & serving
- Readers: <who reads it, and at what level>
- Consumed via: <AI assistant over MCP / chatbot / wiki / plain search>
  (search-served → each entry's `short_answer` must stand on its own)

## Conventions
- Entry ID style: <kebab-case-stable-id | CATEGORY-NNN>
- Initial categories: <list, or "let mining decide">
- Freshness window (days): <e.g. 180 — a `last_verified` older than this is STALE>
- House style: <e.g. expand acronyms on first use; plain statement followed by its citation>
- Output path for the built KB: <default: kb/>

## Never appears in the KB
<Secrets, credentials, personal data, internal-only identifiers, raw source ID numbers in entry
bodies, anything not cleared for this KB's audience. The build and the gates treat this as a hard stop.>

---
### Example (delete, replace with your own)
- Name: Acme Corp — online payments.
- Domain: data-handling rules for engineering teams. In scope: storage, movement, classification.
  Out of scope: HR / legal policy.
- Readers: engineers + security reviewers, mixed levels. Consumed via an AI assistant over MCP.
- IDs: kebab-case. Categories: let mining decide. Freshness: 180 days. Expand acronyms on first use.
  Output: kb/.
- Never: customer data, credentials, internal hostnames, raw policy ID numbers in entry bodies.
