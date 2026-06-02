---
name: mazeledger-trading-api
description: Use when building or operating spot/futures trading bots on the MazeLedger Bot Trading API (ZebPay execution). Covers onboarding, API keys, registering end-users with their own keys, placing/closing spot & futures orders, reading balances/positions, history & stats, the bundled MCP tools, and sandbox-vs-live. Examples: "build a bot that DCAs into BTC", "place a futures order for a user", "how do I onboard to MazeLedger", "use the trading MCP", "close a position".
---

# MazeLedger Bot Trading API

Build trading bots that place **spot (INR)** and **futures (USDT-M)** orders on ZebPay. You write the
bot logic + UI; MazeLedger handles execution, risk caps, idempotency, audit, and a sandbox. Each
end-user brings their **own ZebPay API keys (BYO)** — MazeLedger never custodies funds.

Base URL: `https://mazeledger.ai` · Auth: `x-api-key` header.

## This plugin gives you two things

1. **MCP tools** (server `mazeledger-bots`, authed with your configured API key):
   - `list_symbols` — MIN_NOTIONAL / precision for a symbol
   - `get_account` — balance
   - `get_positions` — open positions
   - `place_futures_order` — place a futures order
   - `close_position` — close a position (reduce-only)
   Sandbox keys (`ml_test_…`) simulate; live keys (`ml_live_…`) place real orders.
2. **This skill** — the full REST contract for everything (incl. spot + onboarding + reporting).

## Get an API key (one-time, web session)

```
POST /api/v1/onboarding/organizations { "name": "Acme", "country": "IN" }
   -> first sandbox key (ml_test_…)
POST /api/v1/organizations/{id}/exchange-connections { "apiKey": "...", "apiSecret": "..." }   # or per end-user
POST /api/v1/organizations/{id}/risk-acknowledgment { "acknowledge": true }   # activates live
POST /api/v1/organizations/{id}/api-keys { "mode": "live" }   # mint a live key
```
Put the key in the plugin config (`api_key`). Start with a sandbox key — no money, no credentials.

## Register end-users (multi-user bots)

```
POST /api/v1/users { "externalUserId": "user-123", "apiKey": "...", "apiSecret": "...", "label": "Jane" }
GET  /api/v1/users                       # list + count
GET/DELETE /api/v1/users/{externalUserId}
```
Then pass `endUserId` on any trading call to route to that user's credentials.

## Futures (USDT-M)

```
POST /api/v1/futures/orders
{ "endUserId": "user-123", "symbol": "DOGEUSDT", "side": "BUY", "type": "MARKET",
  "notionalUsd": 6, "leverage": 5, "clientOrderId": "idem-key" }   # or "quantity"
GET  /api/v1/futures/orders/{id}
GET  /api/v1/futures/positions?endUserId=
POST /api/v1/futures/positions/{id}/close?endUserId=
GET  /api/v1/futures/account?endUserId=
GET  /api/v1/exchange/symbols?symbol=DOGEUSDT
```

## Spot (INR; ₹99 minimum)

```
POST /api/v1/spot/orders
{ "endUserId": "user-123", "symbol": "BTC", "side": "buy", "amountInr": 500 }   # market buy
{ "symbol": "BTC", "side": "sell", "quantity": 0.001 }                          # market sell
{ "symbol": "BTC", "side": "buy", "type": "LIMIT", "quantity": 0.001, "priceInr": 5000000 }
GET /api/v1/spot/orders/{id} ; GET /api/v1/spot/account
```

## Reporting

```
GET /api/v1/orders?externalUserId=&status=&limit=    # org-wide history
GET /api/v1/users/{externalUserId}/orders            # per-user history
GET /api/v1/stats                                    # users, orders total/today, by status, by market
```

## Building a bot — the pattern

A "bot" is your logic calling these primitives on a schedule/signal. Typical loop:
1. Decide (your strategy) what to trade for which user.
2. Optionally `list_symbols` / `get_account` to size the order.
3. `place_futures_order` / `POST /spot/orders` with a stable `clientOrderId` (idempotent).
4. Track via `get_positions` / `GET /orders` / `/stats`; `close_position` on exit.

Example bot ideas: DCA (recurring small buys), grid (laddered limits), TP/SL manager (watch
positions, close on target), copy-trading (mirror a signal across users).

## Safety

- **Sandbox first** — integrate against `ml_test_…` with zero risk, then a live key.
- **Risk caps** per org: `max_order_usd`, `max_daily_notional_usd`, `max_leverage`, `max_open_positions`,
  plus exchange minimums. A breach returns `422 RISK_CAP_REJECTED` with a reason.
- **Idempotency** via `clientOrderId`. **Ambiguous failures** are recorded `unknown`, never lost —
  re-query status/positions before retrying.
- BYO keys should be **trade-only** (no withdrawal). MazeLedger does not custody funds.

Full reference + OpenAPI: `https://mazeledger.ai/docs` and `https://mazeledger.ai/api/openapi`.
