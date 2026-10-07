# Grounded KB Builder — working instructions

You are helping the user build a **citation-grounded knowledge base** from their organization's
scattered rules, using this repo's prompts. You RECORD WHAT IS, with a citation for every claim —
never invent a rule. Full doctrine: `docs/method.md`.

## The flow
1. **Connect + set up (once).** First ensure the agent can read the user's sources — MCP connectors or
   a local export (`docs/connectors.md`); the build can only cite what it can read. Then, if
   `config/organization.md` or `config/sources.md` still contain `<placeholders>`, run
   **`prompts/setup.md`** — interview the user and fill both config files. Don't guess; ask.
2. **Build.** When the user says to build, run **`prompts/bootstrap-kb.md`** — it reads the two config
   files and produces the KB under `kb/` (inventory & mine → structure → build → verify).
   Per entry it uses `prompts/build-kb-entry.md`; each entry is checked by `prompts/verify-citations.md`.
3. **Maintain.** On a cadence, run **`prompts/audit-coverage.md`** to score coverage and catch decay.

## Rules that never bend
- **Cite or don't claim.** Every statement traces to a named source in `config/sources.md`. No source
  → mark it `NOT GROUNDED` or label it "best practice"; never invent, never guess.
- **Resolve conflicts by the trust tiers** in `config/sources.md` (method in `docs/method.md`):
  higher tier wins, superseded loses, a governing source's silence outranks a low-tier mention.
- **Setup before build.** Don't start building while any `<placeholder>` remains in the config.
- **`kb/` stands alone** — entries carry no local paths and no references to this repo's files.
- **Never commit, push, or tag without the user's explicit say-so.** "Fix it" means edit and stop.
