# MazeLedger Screeners, Claude Code plugin

Screen crypto markets in natural language via the [MazeLedger Screener API](https://mazeledger.ai/docs),
right from Claude Code. The plugin bundles:

- a **skill** (`mazeledger-screener-api`), the full screener API contract, and
- a **hosted MCP server** (`mazeledger-screeners`), tools to list, run, and explain screeners.

65 curated screeners on real ZebPay futures and live spot market data. Read-only (scope market:read);
it never places a trade.

## Install

```bash
# 1. Add the marketplace (from the git repo that hosts it)
/plugin marketplace add MythyaVerse/mazeledger-plugins

# 2. Install the plugin (you will be prompted for your API key, stored securely)
/plugin install mazeledger-screeners@mazeledger
```

When prompted:
- **MazeLedger API Key**, your `ml_test_` (sandbox) or `ml_live_` (production) key, scope market:read.
- **API Base URL**, leave as `https://mazeledger.ai` (override only if self-hosting).

## Get a key (one-time)

Onboard your organization and mint a key (full flow in the skill / [docs](https://mazeledger.ai/docs)):

```
POST /api/v1/onboarding/organizations { "name": "Acme", "country": "IN" }   -> sandbox key (market:read), shown once
```

For an evaluation key, email contact@mazeledger.ai.

## Use it

Just ask Claude, e.g.:
- "Screen for oversold coins on the daily."
- "Run the futures long-buildup screener, top 20."
- "List every screener and what each one does."
- "Pull the whole board for futures on 1h."
- `/screen`, guided screening.

The MCP tools (`list_screeners`, `run_screener`, `get_screener_universe`, `get_screener_board`,
`translate_screener`, `list_symbols`, `compare_symbols`) are available to Claude once installed; the
skill carries the full REST contract.

## Notes

- Read-only. The screener API needs only the `market:read` scope; it never places an order.
- The MCP is a remote streamable-HTTP server at `<base_url>/api/v1/mcp` (header `x-api-key`).
- Every match is a candidate, not advice.

Support: contact@mazeledger.ai
