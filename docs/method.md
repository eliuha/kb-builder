# The method

Grounded KB Builder encodes one idea: **an LLM should organize and cite the rules that already
exist — never invent them.** Everything else follows.

## Principles

1. **Record what IS, with citations.** Trust is binary: one invented or unsourced claim, found once,
   and readers stop believing any of it. Record only what a source states; every statement carries
   its citation. Opinions don't belong in the base; a claim supported only by a non-governing source
   is allowed but must be labeled "best practice", never dressed as a requirement.
2. **Cite by name, not by ID.** Identifiers (policy numbers, ticket IDs, doc keys) rot as systems
   migrate; names survive. Cite "the Data Classification Standard," not "STD-4471." Keep any ID↔name
   map *outside* the entries and enforce, via review, that bodies contain no raw IDs.
3. **Resolve conflicts with a Source Trust Hierarchy.** Rank source *types* once, apply the rank
   mechanically (see below). The subtle rule: a governing source's **silence** outranks a low-tier
   page that merely mentions the topic.
4. **Verify, don't trust.** The model that wrote an entry is the wrong judge of it. A **separate**
   pass — fresh context, ideally a different model — re-checks each claim against its cited source for
   support, strength (must vs should), and scope, and
   returns `GROUNDED / PARTIAL / NOT GROUNDED / UNREACHABLE`. Nothing ungrounded ships. Run verifiers
   in parallel; it scales.
5. **`NOT GROUNDED` is a feature.** When no authoritative source answers, the correct output is
   "NOT GROUNDED," not a plausible guess. Gaps are information: they show where a rule is missing,
   ambiguous, or lives only in someone's head. Measure coverage against real demand — the questions
   people actually ask — not against how many entries exist.
6. **Treat the KB like code.** Atomic files (one rule, machine-readable frontmatter); automated
   validation (structure, citations, forbidden content) before merge; a merge is not a publish — cut
   versioned releases so consumers pin a known state; keep content separate from tooling.

## The Source Trust Hierarchy

When two sources disagree, a rule decides — before seniority or edit-recency do. Rank once, apply
every time.

| Tier | Source type | Why here |
| ---: | --- | --- |
| 1 | **Law** | Non-negotiable, external, binding. |
| 2 | **Regulation** | Binding rules issued under law. |
| 3 | **Policy** | The organization's governing "must". |
| 4 | **Standard** | Specific, testable requirements under a policy. |
| 5 | **Procedure** | How to satisfy a standard. |
| 6 | **Approved pattern** | Blessed, reusable implementation. |
| 7 | **Wiki / how-to** | Helpful, but often stale and unsanctioned. |
| 8 | **Individual guidance** | A person's answer; useful, not authoritative. |
| 9 | **Legacy / mirror** | Old copies; lowest trust, cite only if nothing better. |

Adapt the labels to your org, but keep two properties: the order is **explicit**, and it is
**applied mechanically**.

A claim's **label follows its highest supporting tier**: a governing source (law → procedure) makes it
a **requirement**; only lower-tier support makes it a **best practice** (say so); no support makes it
`NOT GROUNDED`.

**Rules of application**
1. **Higher tier wins, always** — even if the lower-tier page is newer or more detailed.
2. **Superseded loses, regardless of tier.** A retired/replaced source never wins and is never cited
   as current; if the only governing source is superseded, the point is `NOT GROUNDED`.
3. **Silence outranks a low-tier mention** — but assert a governing source is silent only if you
   actually read it on this point. Unread ≠ silent.
4. **Refine vs contradict.** A narrower-scope source of equal-or-higher tier refines the rule within
   its scope (state both, each cited); a lower-tier "exception" does not carve one out (rule 3).
5. **Same-tier contradiction:** break the tie in order — explicit supersession → narrower scope →
   more recent effective date. If none decides, `NOT GROUNDED` and escalate.
6. **Record the resolution.** In "conflicts resolved": winner and loser, each with its tier, and which
   rule decided.

## Worked example (synthetic)

All names below are **fictional**, for illustration only.

**Input to `build-kb-entry.md`:**
```text
QUESTION: Must customer data be encrypted when sent between internal services?
SOURCES:
- SOURCE NAME: Acme Information Security Policy
  "All customer data must be protected with encryption in transit and at rest across all Acme systems."
- SOURCE NAME: Acme Network Standard
  "Traffic between internal services must use TLS 1.2 or higher. Plaintext internal traffic is prohibited."
- SOURCE NAME: Team Wiki — Service Setup
  "For internal-only services you can skip TLS to simplify local testing."
CATEGORY: data-movement
```

**Output entry:**
```markdown
---
id: internal-service-data-encryption-in-transit
title: Encrypt customer data in transit between internal services
category: data-movement
tags: [encryption, tls, internal-traffic]
sources_consulted: [Acme Information Security Policy, Acme Network Standard, Team Wiki — Service Setup]
last_verified: 2026-08-20
status: grounded
---
## Requirement
Customer data must be encrypted in transit across all systems. (Acme Information Security Policy)
Traffic between internal services must use TLS 1.2 or higher; plaintext internal traffic is
prohibited. (Acme Network Standard)
## Applies when
Any internal service-to-service call that carries customer data.
## Conflicts resolved
Team Wiki — Service Setup said TLS could be skipped for internal-only services; the Acme Network
Standard (higher tier) prohibits plaintext internal traffic, so the Standard wins.
```

**Output from `verify-citations.md`:**

| Claim | Cited source | Verdict | Evidence | Fix |
| --- | --- | --- | --- | --- |
| Customer data encrypted in transit | Acme Information Security Policy | GROUNDED | "…encryption in transit and at rest across all Acme systems." | — |
| Internal traffic must use TLS 1.2+ | Acme Network Standard | GROUNDED | "Traffic between internal services must use TLS 1.2 or higher." | — |
| Plaintext internal traffic prohibited | Acme Network Standard | GROUNDED | "Plaintext internal traffic is prohibited." | — |

`SUMMARY: 3 GROUNDED, 0 PARTIAL, 0 NOT GROUNDED, 0 UNREACHABLE; 0 stale; 0 label/supersession flags. SHIP: yes.`

The low-tier wiki exception was **overridden**, not recorded as a rule. Had the wiki been the *only*
source touching internal traffic — with no governing source — the entry would have been `NOT GROUNDED`.

## Why LLMs, then?
Not to know the rules — to do the tedious, high-volume work of reading scattered sources, drafting
atomic entries, attaching citations, and checking them, at a scale humans won't sustain. The
discipline lives in the prompts; the leverage comes from the model.
