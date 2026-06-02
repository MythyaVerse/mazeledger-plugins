# MazeLedger Bots — Claude Code plugin

Build **spot & futures trading bots** on ZebPay via the [MazeLedger Bot Trading API](https://mazeledger.ai/docs),
right from Claude Code. The plugin bundles:

- a **skill** (`mazeledger-trading-api`) — the full API contract + how to build bots, and
- a **hosted MCP server** (`mazeledger-bots`) — tools to place/close orders, read accounts & positions.

You build the bot logic and UI; MazeLedger handles execution, risk caps, idempotency, audit, and a
sandbox. Each end-user brings their **own ZebPay keys (BYO)** — no custody of funds.

## Install

```bash
# 1. Add the marketplace (the public plugins repo)
/plugin marketplace add MythyaVerse/mazeledger-plugins

# 2. Install the plugin (you'll be prompted for your API key, stored securely)
/plugin install mazeledger-bots@mazeledger
```

When prompted:
- **MazeLedger API Key** — your `ml_test_…` (sandbox) or `ml_live_…` (live) key (see *Get a key* below).
- **API Base URL** — leave as `https://mazeledger.ai` (override only if self-hosting).

## Get a key (one-time)

Onboard your organization and mint a key (full flow in the skill / [docs](https://mazeledger.ai/docs)):

```
POST /api/v1/onboarding/organizations { "name": "Acme", "country": "IN" }   -> sandbox key (shown once)
POST /api/v1/organizations/{id}/exchange-connections { "apiKey": "...", "apiSecret": "..." }
POST /api/v1/organizations/{id}/risk-acknowledgment { "acknowledge": true }
POST /api/v1/organizations/{id}/api-keys { "mode": "live" }                  -> live key
```

Start with the **sandbox** key — simulated fills, no money, no credentials.

## Use it

Just ask Claude, e.g.:
- "Build a bot that DCAs ₹500 into BTC every morning for each of my users."
- "Place a 5x DOGEUSDT futures order for user-123 and show me the position."
- "What's the spot balance for user-123? Sell half their DOGE."
- `/build-bot` — guided bot scaffolding.

The MCP tools (`place_futures_order`, `get_positions`, `close_position`, `get_account`,
`list_symbols`) are available to Claude once installed; the skill carries the full spot + futures +
reporting contract.

## Safety

- **Sandbox first.** Integrate against `ml_test_…` (free), then switch to `ml_live_…`.
- Per-org **risk caps** (order size, leverage, daily notional, open positions) + exchange minimums.
- **Idempotency** via `clientOrderId`; ambiguous failures recorded `unknown` (never lost).
- BYO keys should be **trade-only** (no withdrawal).

## Notes

- The MCP is a remote streamable-HTTP server at `<base_url>/api/v1/mcp` (header `x-api-key`).

Support: partners@mazeledger.ai
