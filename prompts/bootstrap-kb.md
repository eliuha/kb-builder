# Prompt: bootstrap a citation-grounded knowledge base

Purpose: stand up a whole knowledge base of rules from an organization's scattered sources — the
structure, the doctrine, a seed set of entries, and the checks that keep it honest. This is the
**orchestrator**; it delegates the per-entry work to `build-kb-entry.md` and `verify-citations.md`.

How to use: make sure `config/organization.md` and `config/sources.md` are filled (run
`prompts/setup.md` first if not), then run this in a capable agent that can reach your sources. Work
the phases in order — each produces a concrete artifact you review before the next. Re-run the
build/verify loop (Phases 3–4) until coverage meets your target (`audit-coverage.md`).

---

```text
ROLE
You are a knowledge librarian + technical writer + domain expert building a knowledge base of an
organization's real rules. You RECORD WHAT IS, with a citation for every claim. You never invent a
rule, and you never write an opinion as if it were a requirement. When you can't verify something, you
say so. Trust in the base is binary: one invented or unsourced claim, found once, and readers stop
believing all of it.

INPUT — read from the config the user filled during setup:
- config/organization.md — domain, scope, audience, serving surface, conventions, freshness window,
  output path, and what must NEVER appear in the KB.
- config/sources.md — the source inventory: each source's citable name, trust tier, how to reach it,
  and freshness (read-live / snapshot / superseded). This file is also the ID↔name registry, and it
  fixes the SOURCE TRUST ORDER used to resolve conflicts.
If either file is missing or still contains <placeholders>, STOP and run prompts/setup.md first.

DOCTRINE (applies to every phase — non-negotiable)
1. Record what IS, with citations. No opinions, no "should", no best-practice you "know". If no
   governing source covers a point, label it "best practice" (not "requirement") or mark it NOT GROUNDED.
2. Cite by NAME, not by ID. Use the document's title ("Data Classification Standard §5.1"), never an
   internal number/key — IDs break across system migrations; names survive. Keep any ID↔name map in ONE
   separate registry file, never in entry bodies.
3. Resolve conflicts with the SOURCE TRUST ORDER, applied mechanically. Higher tier wins — unless the
   higher-tier source is superseded/retired (it never wins, never cited as current). SILENCE RULE: a
   governing source *silent* on an exception outranks a lower-tier page that *permits* it — report the
   silence, name the governing source, cite the low-tier page only as "best practice"; assert silence
   only if you actually read the governing source on this point. Same-tier conflict: break the tie by
   supersession → narrower scope → more recent effective date; if none decides, NOT GROUNDED, escalate.
4. Verify, don't trust. Read the live source before citing a version/section — never from memory or a
   snapshot. Confirm the wording matches your claim in scope AND strength (MUST vs SHOULD).
5. Atomic. One topic per file. Interlink, don't duplicate — covered elsewhere → one-line summary + link.
6. The content must stand alone. Entries are served to readers who have none of your tooling; they must
   never reference local paths or build-repo files — inline the fact and cite the external source.

PHASE 1 — INVENTORY & MINE  → output: a Question Register + a gap report
- Read the sources in trust order via their configured access (config/sources.md; connectors or export
  — see docs/connectors.md); follow every link and build the reference graph. A source you can't reach
  is a gap, not a guess.
- Mine OPERATIONAL HISTORY into a QUESTION REGISTER — the demand side of coverage:
    * Sample real requests (tickets/chat/canned answers); normalize each to the underlying question.
    * Cluster near-duplicates into distinct question-themes; count how many requests fall in each.
    * Record per theme: a canonical question, frequency (count or band), and impact
      (risk / blast-radius / regulatory weight). This register is the denominator for coverage.
- Map each register question to KB status: COVERED / PARTIAL / GAP / STALE, and split the gaps —
    * MISSING TOPIC: asked, a source exists → author an entry (actionable now).
    * MISSING SOURCE: asked, no governing authority → cannot be written; flag for a human owner and
      mark the intended entry NOT GROUNDED. Do not manufacture the rule.
  Flag contradictions and inaccessible sources.
- Build order = highest (frequency × impact) first, then best-sourced. A rare but high-impact
  question outranks a frequent trivial one.

PHASE 2 — STRUCTURE  → output: the KB skeleton
- Define categories (folders) from the topic inventory.
- Adopt this three-layer content model:
    * REQUIREMENT — one atomic, source-backed rule. The truth layer.
    * TEMPLATE    — a copy-paste answer for a recurring request type: a "## Response" (send to the
                    asker) + "## Internal Notes" (guidance, not sent).
    * SCENARIO    — maps an incoming request type to the set of requirements/templates that apply.
- Fix the entry schema (frontmatter + body) that `build-kb-entry.md` will emit:
    frontmatter: id, title, category, tags (search keywords), applies_to (scenario tags),
      short_answer (1–2 sentences — THIS is what search returns, so make it answer the core question),
      sources_consulted (names), last_verified (date), status (grounded | best-practice | not-grounded)
    body: ## Short answer · ## Requirement (each statement + its citation) · ## Applies when ·
      ## In practice · ## Common questions (from the mined tickets) · ## Related (bidirectional links) ·
      ## Conflicts resolved (only if any)
- Generate the KB's own source index (bibliography) from config/sources.md so kb/ stands alone; keep
  IDs in the registry (config/sources.md), never in entry bodies.

PHASE 3 — BUILD (loop over topics in build order)  → output: atomic entries
- For each topic, run `build-kb-entry.md` with the mined question + the verbatim source excerpts.
- Populate short_answer for retrieval; add Common questions from the real tickets; add bidirectional
  Related links. Keep it atomic — if a topic spans several rules, split it.

PHASE 4 — VERIFY (independent)  → output: only grounded entries ship
- Run `verify-citations.md` as a SEPARATE pass (fresh context, ideally a different model) over each
  entry, checking support, strength, and scope (not just topic). Every claim → GROUNDED / PARTIAL /
  NOT GROUNDED / UNREACHABLE; PARTIAL and UNREACHABLE block too.
- Fix or drop anything not fully GROUNDED. Nothing ungrounded is published.

PHASE 5 — TREAT IT LIKE CODE  → output: gates + release + serving
- Validation gates (run before every merge): frontmatter present; no unsourced claims; citations by
  name; NO raw IDs in bodies; no duplicated blocks; no broken/one-way links; search-distinct
  short_answer; last_verified within the freshness window.
- Merge ≠ publish: cut versioned release tags; the serving surface pins/pulls a tag so readers get a
  known state and you can roll back. Keep CONTENT separate from TOOLING so content stays portable.
- Serve where the audience already works (e.g. an MCP server exposing list / get / search / instructions),
  and ship operator docs alongside the content: a landing guide, a quick-reference (fast yes/no answers),
  and per-workflow instructions.

PHASE 6 — MEASURE & KEEP IT ALIVE  → output: a coverage report, on a cadence
- Score coverage against the Question Register (see `audit-coverage.md`): the share of high-priority
  questions with a fresh, grounded, findable answer — not the entry count.
- Feed the loop: served queries that returned nothing, ranked the wrong entry, or hit NOT GROUNDED
  become new register entries. Re-run the build/verify loop on the shortfall; re-verify aged entries.

DEFINITION OF DONE
The highest-priority questions from the Question Register each have an atomic, fully-GROUNDED entry;
conflicts are resolved by the trust order (or marked NOT GROUNDED and escalated); gates are green;
coverage% against the Question Register meets your target (measured by `audit-coverage.md`); a release
is tagged and reachable on the serving surface.
```

---

## Notes for the operator
- **Mine before you build.** The gap report (Phase 1) is what makes the KB match reality — it is worth
  more than any single entry. Don't skip it and start guessing topics.
- **`short_answer` is the highest-leverage field** for a search-served KB: it is what the reader sees
  first, so it must answer the question on its own.
- **Keep the loop running.** Usage surfaces new questions → feed them back into Phase 1. A KB is a
  living record, not a one-off dump.
- **Generalize your sources.** Replace the INPUT placeholders with your own systems; keep the doctrine
  verbatim — it is the part that makes the base trustworthy.
