# Connect your sources

The builder can only cite what the agent can actually **read**. Before building, give the agent access
to your policy and document systems, and record how each source is reached in `config/sources.md`
(the `Access` column). Do this before setup so the agent can read while it works.

## Two ways to provide access

### 1. MCP connectors — preferred, for live sources
Expose each source system to the agent as an MCP server with **read-only** tools (list / search / get):

- policy / GRC system (policies, standards, procedures)
- wiki / knowledge base, design & architecture docs
- document store / file share
- ticket / request system (mined for the recurring questions)

In Claude Code, add one connector per system. Project-scoped config lives in `.mcp.json` at the repo
root, or use the CLI:

```
claude mcp add <name> -- <command that starts the server>
```

Generic `.mcp.json` shape (one entry per source system):

```json
{
  "mcpServers": {
    "policy": { "command": "<server>", "args": ["<...>"], "env": { "TOKEN": "${POLICY_TOKEN}" } },
    "wiki":   { "command": "<server>", "args": ["<...>"] }
  }
}
```

- **Read-only.** Grant search/read scopes only — never write or delete.
- **Auth out of band.** Put tokens in environment variables or a secret manager, not in committed files.

### 2. Local export folder — simplest, a snapshot
Export the relevant policies/docs into a folder the agent can read (e.g. `sources/`). Mark these
`snapshot` in `config/sources.md` and re-export when they change. Good for a first run, or for sources
with no connector. A public web page with a stable URL can be fetched directly — record the URL as its access.

## Record it, then verify

- In `config/sources.md`, the `Access` column names how the agent reaches each source: the MCP tool
  name, the folder path, or the URL. `prompts/setup.md` walks you through this.
- **Verify before building:** for each source, confirm the agent gets real content back (search/get a
  known document). A source the agent cannot read becomes a coverage **gap**, never a silent guess —
  fix access now or mark it unreachable.

## Security

- Read-only tools, least privilege.
- Secrets live in env vars or a secret manager — never in `config/` or `kb/`.
- Connectors may expose data that must not enter the KB. Honor the **never-appears** list in
  `config/organization.md`; the build and gates treat it as a hard stop.
