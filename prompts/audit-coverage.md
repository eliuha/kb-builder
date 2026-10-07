# Prompt: audit KB coverage

Purpose: measure what the knowledge base actually covers — against real demand — and produce a
repeatable, comparable COVERAGE REPORT. Run it on an existing KB, on a cadence; diff successive
reports to watch coverage rise and catch decay. Complements `verify-citations.md` (which checks one
entry's facts); this checks the base as a whole.

How to use: give it the KB (all entries), the Question Register from `bootstrap-kb.md` Phase 1, and —
if available — a log of real queries the KB was asked. Set FRESHNESS_DAYS. It scores coverage and
returns a prioritized shortfall list.

---

```text
ROLE
You audit coverage of a knowledge base of rules. Coverage is not "how many entries exist" — it is
"what share of the questions people actually ask has a fresh, grounded, findable answer." An entry
that is unsourced, stale, duplicated, or unfindable does not count as coverage.

INPUT
- QUESTION REGISTER: <ranked questions with frequency + impact — the demand side>
- KB: <all entries: frontmatter + body>
- QUERY LOG: <optional: real queries served, and whether each was answered>
- FRESHNESS_DAYS: <age beyond which last_verified is STALE, e.g. 180>

METHOD
1. MATCH each register question to at most one primary entry.
2. Classify each question:
   - COVERED    : a grounded entry answers it AND ranks first when you search its terms.
   - PARTIAL    : an entry exists but is incomplete, partly grounded, or not clearly the top hit.
   - GAP-TOPIC  : no entry, but a source exists -> author one.
   - GAP-SOURCE : no entry and no governing authority -> needs a human owner; cannot be written.
   - STALE      : an entry exists but last_verified is older than FRESHNESS_DAYS (re-verify).
3. FINDABILITY: for each COVERED/PARTIAL, search the KB with the question's own words; confirm the
   intended entry is the top result. If a different entry wins, record a findability defect.
4. REDUNDANCY (over-coverage): find entries that overlap — same topic in two files, duplicated
   blocks, or non-distinct short_answers competing for one query. Each is a defect: merge, or keep
   one and redirect the other. Any answer needing 3+ cross-reference hops -> consolidate.
5. SCORE:
       coverage% = COVERED questions / all register questions
   Compute a second, impact-weighted coverage%. Report both.

OUTPUT (same shape every run, so runs are comparable)
### Coverage summary
- coverage%: <n> (<covered>/<total>)   impact-weighted: <n>
- counts: COVERED <> · PARTIAL <> · GAP-TOPIC <> · GAP-SOURCE <> · STALE <>
- findability defects: <> · redundancy defects: <>
### Shortfall (ordered by frequency x impact)
| Question | Status | Defect | Action | Owner |
| --- | --- | --- | --- | --- |
### Redundancy
| Entries | Overlap | Fix (merge / redirect) |
### Stale (re-verify queue, oldest first)
| Entry | last_verified | Age |

RULES
- Judge coverage only against the QUESTION REGISTER, not against how many files exist.
- Never count an unsourced, NOT GROUNDED, stale, or unfindable entry as COVERED.
- A GAP-SOURCE is not fixable by writing — never resolve it by inventing a rule.
```

---

## Notes for the operator
- Keep each report; diff it next run. Rising coverage% and a shrinking stale queue are the signal.
- Feed the loop: every QUERY LOG miss (nothing returned, wrong entry ranked, NOT GROUNDED) becomes a
  new Question Register entry, then flows into the build/verify loop.
- Fill GAP-TOPIC with `build-kb-entry.md`; clear STALE by re-running `verify-citations.md` on aged
  entries; escalate GAP-SOURCE to the human who owns that authority.
