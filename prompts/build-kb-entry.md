# Prompt: build one KB entry

Purpose: produce **one atomic, citation-grounded knowledge-base entry** from source excerpts and a
recurring question. Model-agnostic. Fill the three placeholders and run.

---

```text
You are building ONE atomic entry for a knowledge base of rules/requirements.

INPUT
- QUESTION: <the recurring question this entry must answer>
- SOURCES: <paste source excerpts; each with its SOURCE NAME and, if useful, a short locator>
- CATEGORY: <optional: the domain this belongs to, e.g. data-movement, access-control>

RULES
1. Record only what the SOURCES STATE. No opinions, no "should" you "know". A claim backed by a
   governing source (law/regulation/policy/standard/procedure) is a REQUIREMENT; a claim backed only
   by a lower-tier source (approved pattern/wiki/guidance) is a BEST PRACTICE and must be written
   "(best practice)". Never present a best practice as a requirement.
2. Every factual claim is IMMEDIATELY followed by a citation to a NAMED source (use the source's
   title/name, never an internal ID or number — IDs break across system migrations).
3. DETECT conflicts before writing. Compare the sources pairwise on the QUESTION and classify each:
     - AGREE      — same answer; cite the highest-tier source.
     - REFINE     — a narrower-scope source adds a caveat. If it is equal-or-higher tier, state the
                    general rule AND the scoped exception, each cited. If it is LOWER tier, the
                    governing rule stands and the caveat does NOT create an exception (see SILENCE).
     - CONTRADICT — same scope, opposite answers; resolve by rule 4.
4. RESOLVE a contradiction with the Source Trust Hierarchy (highest wins):
       law > regulation > policy > standard > procedure > approved pattern >
       wiki/how-to > individual guidance > legacy/mirror
   - SUPERSESSION: a retired/replaced source loses regardless of tier and is never cited as current.
     If the only governing source is superseded, the point is NOT GROUNDED.
   - SILENCE: a governing source that is *silent* on an exception outranks a lower-tier page that
     merely permits it — but assert silence ONLY if that governing source was actually provided and
     read for this question. If it was not, mark the point UNREACHABLE; do not assume silence.
   - SAME-TIER contradiction: break the tie in order — (a) explicit supersession, (b) narrower scope
     wins within its scope, (c) more recent EFFECTIVE date of the source (not the page's edit date).
     If none decides, set STATUS: NOT GROUNDED and record both in "## Conflicts resolved".
5. If NO source covers the question, output exactly:
       STATUS: NOT GROUNDED
   with a one-line note on what source would be needed. DO NOT GUESS or fill the gap.
6. Keep it atomic: one requirement/topic per entry. If the question spans several, say so and
   propose the split — do not merge unrelated rules.

OUTPUT (Markdown with YAML frontmatter; nothing else)
---
id: <kebab-case-stable-id>
title: <short imperative title>
category: <CATEGORY or best fit>
tags: [<a>, <b>]
sources_consulted:
  - <source name 1>
  - <source name 2>
last_verified: <YYYY-MM-DD>
status: grounded            # grounded | best-practice | not-grounded
---

## Requirement
<one or more statements. Each statement is followed by its citation, e.g.:
"Data classified X must be encrypted in transit. (<Source Name>)">

## Applies when
<one line describing the scenario/trigger this entry answers>

## Conflicts resolved
<omit if none. One line per conflict: "<Source A> (tier) said X vs <Source B> (tier) said Y;
<winner> wins by <tier | supersession | silence | scope | effective-date>." If unresolved:
"NOT GROUNDED — <the decision a human must make>.">
```

---

## Notes for the operator
- Paste **verbatim** source excerpts, not summaries — the model can only cite what it can see.
- Prefer several short, high-trust sources over one long low-trust page.
- Run every produced entry through `verify-citations.md` before accepting it.
- `NOT GROUNDED` is a success, not a failure: it marks a real gap instead of inventing a rule.
- Paste the governing source IN FULL for the topic — the model may only assert "silence" on a source
  it actually saw. A missing excerpt is UNREACHABLE, not silence.
- Mark any source you know is superseded; a superseded source never wins a conflict.
