# Grounded KB Builder

> **Working name — provisional, pending brand/trademark clearance.**

Turn your organization's scattered rules into one **citation-grounded** knowledge base: every
statement traces to a named source, conflicts resolve by a trust hierarchy, and nothing ungrounded
ships. Markdown prompts + two config files — no SDK, no runtime. Runs in Claude Code or any capable agent.

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](./LICENSE)

## Use it

1. **Fork** this repo and open your fork in Claude Code.
2. **Connect your sources** so the agent can read your policies and docs — MCP connectors or a local
   export folder. See [`docs/connectors.md`](docs/connectors.md).
3. **Set up** — say *"help me set up my knowledge base."* Claude runs `prompts/setup.md` and fills:
   - `config/organization.md` — org, domain, audience, serving surface, conventions.
   - `config/sources.md` — your sources, each with a **trust tier** and how to reach it.
4. **Build** — say *"build the knowledge base."* Claude runs `prompts/bootstrap-kb.md`:
   mine → structure → build (`build-kb-entry.md`) → verify (`verify-citations.md`) → into `kb/`.
5. **Maintain** — say *"audit coverage"* now and then (`prompts/audit-coverage.md`).

*Just one entry?* Fill `prompts/build-kb-entry.md`, run it, verify with `prompts/verify-citations.md`.

## How it works

Five principles — **cite everything · cite by name · trust hierarchy · verify · treat like code** —
explained with a worked example in [`docs/method.md`](docs/method.md). The model *organizes and cites*
the rules that already exist; it never invents them. One ungrounded claim poisons trust in the base.

## Contents

| Path | What |
| --- | --- |
| `config/organization.md` | Your org, domain, audience, conventions (you fill). |
| `config/sources.md` | Your sources + trust tiers; the ID↔name registry (you fill). |
| `docs/connectors.md` | Connect your source systems so the agent can read them. |
| `prompts/setup.md` | Interview that fills the two config files. |
| `prompts/bootstrap-kb.md` | Build the whole KB from the config (orchestrator). |
| `prompts/build-kb-entry.md` | Build one atomic, cited KB entry. |
| `prompts/verify-citations.md` | Independently verify an entry's citations. |
| `prompts/audit-coverage.md` | Score coverage against real demand. |
| `docs/method.md` | The method, the trust hierarchy, a worked example. |
| `CLAUDE.md` | Instructions the agent loads when your fork is opened. |

## Contributing & license

See [CONTRIBUTING.md](./CONTRIBUTING.md) (contributions licensed Apache-2.0).
License: Apache 2.0 — [LICENSE](./LICENSE).
