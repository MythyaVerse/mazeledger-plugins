# MazeLedger Bot Trading API (for Codex)

> Add this content to your project's `AGENTS.md` (or `~/.codex/AGENTS.md`) so Codex knows how to use
> the MazeLedger Bot Trading API + the `mazeledger-bots` MCP server.

Build trading bots that place **spot (INR)** and **futures (USDT-M)** orders on ZebPay. You write the
bot logic; MazeLedger handles execution, risk caps, idempotency, audit, and a sandbox. Each end-user
brings their **own ZebPay keys (BYO)** — no custody.

Base URL `https://mazeledger.ai` · Auth via the `mazeledger-bots` MCP server (configured in
`~/.codex/config.toml`, authenticated with `MAZELEDGER_API_KEY`). MCP tools: `list_symbols`,
`get_account`, `get_positions`, `place_futures_order`, `close_position`. Sandbox key (`ml_test_…`)
simulates; live key (`ml_live_…`) places real orders.

## Get a key (one-time)
```
POST /api/v1/onboarding/organizations { "name": "Acme", "country": "IN" }   -> sandbox key (once)
POST /api/v1/organizations/{id}/exchange-connections { "apiKey": "...", "apiSecret": "..." }
POST /api/v1/organizations/{id}/risk-acknowledgment { "acknowledge": true }
POST /api/v1/organizations/{id}/api-keys { "mode": "live" }                  -> live key
```

## End-users
`POST /api/v1/users { externalUserId, apiKey, apiSecret }`, then pass `endUserId` on trading calls.

## Futures
`POST /api/v1/futures/orders { endUserId?, symbol, side, notionalUsd|quantity, leverage, type, clientOrderId }`;
`GET /futures/orders/{id}`, `/futures/positions`, `POST /futures/positions/{id}/close`, `/futures/account`,
`/exchange/symbols?symbol=`.

## Spot (INR; ₹99 min)
`POST /api/v1/spot/orders { endUserId?, symbol, side:"buy", amountInr }` (buy) or `{ side:"sell", quantity }`;
`GET /spot/orders/{id}`, `/spot/account`.

## Reporting
`GET /api/v1/orders?externalUserId=&status=`, `/users/{externalUserId}/orders`, `/stats`.

## Building a bot
Decide → size (`list_symbols`/`get_account`) → place with a stable `clientOrderId` (idempotent) →
track (`get_positions`/`/orders`) → `close_position`. Ideas: DCA, grid, TP/SL, copy-trading.

## Safety
Sandbox first. Per-org risk caps + exchange minimums → `422 RISK_CAP_REJECTED`. Idempotency via
`clientOrderId`; ambiguous failures recorded `unknown` (re-query before retry). BYO keys trade-only.
Full docs: `https://mazeledger.ai/docs`.
