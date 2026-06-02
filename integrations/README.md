# MazeLedger Bots — AI client integrations

Build spot & futures trading bots (via the [MazeLedger Bot Trading API](https://mazeledger.ai/docs))
from any major AI coding client. Each integration = the **MCP server** (`mazeledger-bots`) + an
**instructions file** (the "skill"). Same API key works across all of them: `ml_test_…` (sandbox,
simulated, free) or `ml_live_…` (real orders).

| Client | MCP config | Instructions | Auth | Folder |
|--------|-----------|--------------|------|--------|
| **Claude Code** | plugin `.mcp.json` (auto, prompts for key) | bundled Skill | `x-api-key` header | [`../plugins/mazeledger-bots`](../plugins/mazeledger-bots) |
| **Cursor** | `.cursor/mcp.json` (`url` + `x-api-key` header) | `.cursor/rules/*.mdc` | `x-api-key` header (`${env:…}`) | [`cursor/`](./cursor) |
| **Codex** | `~/.codex/config.toml` (`url` + `bearer_token_env_var`) | `AGENTS.md` | `Authorization: Bearer` (env var) | [`codex/`](./codex) |

All point at the same hosted MCP endpoint: `https://mazeledger.ai/api/v1/mcp` (the server accepts
the org key via **either** `x-api-key` **or** `Authorization: Bearer`, so every client works).

## Quickstart (any client)
1. Get an API key — `POST /api/v1/onboarding/organizations` → sandbox key (full flow at
   https://mazeledger.ai/docs).
2. Install the integration for your client (see its folder's README).
3. Set the key (Claude Code prompts; Cursor/Codex use the `MAZELEDGER_API_KEY` env var).
4. Ask your assistant to build/run a bot — start in sandbox.

## Tools exposed (MCP)
`list_symbols`, `get_account`, `get_positions`, `place_futures_order`, `close_position`. The
instructions file adds the full REST contract (spot + futures + onboarding + end-users + reporting).

