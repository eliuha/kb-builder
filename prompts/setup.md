# Prompt: set up your knowledge base

Purpose: interview the user and fill the two config files that drive every build —
`config/organization.md` (who / what / where) and `config/sources.md` (the sources + trust tiers).
Run this once, right after forking. Re-run any time your scope or sources change.

How to use: run it in an agent that can edit files in this repo (e.g. Claude Code with the fork open),
or paste it into any capable LLM and copy its output into the two files. It asks; the user answers;
it writes the config. It does **not** build — building is a separate, explicit step.

---

```text
ROLE
You are setting up a citation-grounded knowledge-base builder for the user's organization. INTERVIEW
the user and fill two config files. Do not guess answers, and do not start building.

READ FIRST
- config/organization.md and config/sources.md — the templates you will fill.
- docs/method.md — the doctrine and the Source Trust Hierarchy you will map sources onto.
- docs/connectors.md — how sources are connected; if one isn't reachable yet, connect it first.

INTERVIEW — ask in short batches, one topic at a time; reflect answers back and confirm before writing.

A. ORGANIZATION & DOMAIN  → fills config/organization.md
   1. What does the organization do? (one or two lines; generic is fine if sensitive.)
   2. What will this KB cover — the domain — and what is explicitly OUT of scope?
   3. Who reads it, and at what level?
   4. Where will they consume it (AI assistant over MCP / chatbot / wiki / plain search)?
   5. Conventions: entry ID style; initial categories (or "let mining decide"); freshness window in
      days; any house style; output path (default kb/).
   6. What must NEVER appear in the KB (secrets, personal data, internal-only identifiers)?

B. SOURCES & TRUST  → fills config/sources.md
   Walk the source landscape from most to least authoritative. For EACH source capture:
   - NAME to cite it by (stable, human-readable — this is what entries will reference).
   - TRUST TIER — map it onto the hierarchy in docs/method.md (law > regulation > policy > standard >
     procedure > approved pattern > wiki/how-to > individual guidance > legacy/mirror).
   - ACCESS — how you reach it (URL / system / path / MCP tool).
   - FRESHNESS — read-live / snapshot / superseded.
   - ID / key, if any — recorded HERE only (this file is the registry); it must never appear in entries.
   Prompt specifically for: the authoritative system(s); wikis / design docs; and OPERATIONAL HISTORY
   (tickets / chat) — the last is low-trust for citing but is where the recurring QUESTIONS come from.
   Confirm the TRUST ORDER; if two source types could tie, ask the user which wins.

WRITE
- Fill both files, replacing every <placeholder>. Remove the "Example" blocks once real content is in.
- Show the user the filled files; fix whatever they correct.

DONE
- Confirm NO <placeholder> remains in either file, every source has a tier, and the trust order is set.
- Tell the user: "Setup complete. When you're ready, say 'build the knowledge base' and I'll run
  prompts/bootstrap-kb.md." Do NOT start building now.
```

---

## Notes for the operator
- This step only writes config. Building is separate and explicit.
- Sources you can't reach become honest gaps later — list them now rather than omitting them.
- Keep the config in version control; the build always reads the latest committed answers.
