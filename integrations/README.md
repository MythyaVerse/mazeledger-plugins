# MazeLedger Screener API: AI client integrations

Screen crypto markets across 65 curated screeners on real ZebPay futures and live spot market data
(via the [MazeLedger Screener API](https://mazeledger.ai/docs)) from any major AI coding client.
Each integration = the **MCP server** (`mazeledger-screeners`) + an **instructions file** (the "skill").
Read-only (scope `market:read`); it never places a trade. Same API key works across all of them:
`ml_test_` (sandbox) or `ml_live_` (production), both read the same live data.

| Client | MCP config | Instructions | Auth | Folder |
|--------|-----------|--------------|------|--------|
| **Claude Code** | plugin `.mcp.json` (auto, prompts for key) | bundled Skill | `x-api-key` header | [`../plugins/mazeledger-screeners`](../plugins/mazeledger-screeners) |
| **Cursor** | `.cursor/mcp.json` (`url` + `x-api-key` header) | `.cursor/rules/*.mdc` | `x-api-key` header (`${env:...}`) | [`cursor/`](./cursor) |
| **Codex** | `~/.codex/config.toml` (`url` + `bearer_token_env_var`) | `AGENTS.md` | `Authorization: Bearer` (env var) | [`codex/`](./codex) |

All point at the same hosted MCP endpoint: `https://mazeledger.ai/api/v1/mcp` (the server accepts
the org key via **either** `x-api-key` **or** `Authorization: Bearer`, so every client works).

## Quickstart (any client)
1. Get an API key: `POST /api/v1/onboarding/organizations` returns a sandbox key (full flow at
   https://mazeledger.ai/docs). For an evaluation key, email contact@mazeledger.ai.
2. Install the integration for your client (see its folder's README).
3. Set the key (Claude Code prompts; Cursor/Codex use the `MAZELEDGER_API_KEY` env var).
4. Ask your assistant to run a screener: read the catalog first, then run a named screener, the
   manual builder, or raw filters.

## Tools exposed (MCP)
`list_screeners`, `run_screener`, `get_screener_universe`, `get_screener_board`, `translate_screener`,
`list_symbols`, `compare_symbols`. The instructions file adds the full REST contract (catalog,
universe, run, board, translate, the signed push webhook).
