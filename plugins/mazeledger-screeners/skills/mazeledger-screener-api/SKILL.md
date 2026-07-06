---
name: mazeledger-screener-api
description: Use when screening crypto markets via the MazeLedger Screener API (ZebPay futures + spot). Covers getting an org API key, the self-describing catalog, running the 65 named screeners / the manual builder / raw filters, per-coin futures coverage, the bulk board, natural-language screening, the bundled MCP tools, and the signed push webhook. Read-only (scope market:read); it never trades. Examples: "screen for oversold coins on the daily", "run the futures long-buildup screener", "list every screener", "pull the whole board".
---

# MazeLedger Screener API

Screen crypto markets across 65 curated screeners on real ZebPay futures and live spot market data. Read-only (scope `market:read`); it never places a trade.

Base URL: `https://mazeledger.ai`. Auth: `x-api-key` header. Scope: `market:read`.

## This plugin gives you two things

1. **MCP tools** (server `mazeledger-screeners`, authed with your configured API key):
   - `list_screeners`, the catalog: 65 named screeners with their parameters
   - `run_screener`, run a named screener, the manual builder, or raw filters
   - `get_screener_universe`, supported coins + per-coin coverage
   - `get_screener_board`, every Live screener's matches in one pull
   - `translate_screener`, plain English to a validated query, then runs it
   - `list_symbols` / `compare_symbols`, symbol metadata + a side-by-side compare
2. **This skill**, the full REST contract for the screener API.

Sandbox keys (`ml_test_`) and production keys (`ml_live_`) both read the same live data; screeners are read-only either way.

## Get an API key (one-time, web session)

```
POST /api/v1/onboarding/organizations { "name": "Acme", "country": "IN" }
   -> first sandbox key (ml_test_), scope market:read
```
Put the key in the plugin config (`api_key`). For an evaluation key, email contact@mazeledger.ai.

## Discover, then run

Read the catalog once; it lists every screener and every parameter you can send.

```
GET  /api/v1/screener/catalog                        # 65 named definitions, 12 manual dimensions, raw filter spec, thresholds, limits
GET  /api/v1/screener/universe?market=spot|futures   # supported coins + coverage (futures: sourceFamilies[] / candleCoverage)
```

Run a screener three ways. Provide exactly ONE of `definition` / `manualFilters` / `filters`:

```
GET  /api/v1/screener/run?definition=rsi-oversold&market=futures&timeframe=1h&top=20
POST /api/v1/screener/run  { "definition": "rsi-oversold", "market": "futures", "timeframe": "1h", "top": 20 }
POST /api/v1/screener/run  { "manualFilters": [{"dimension":"RSI (14)","value":"Oversold (< 30)","logic":"RSI(14) < 30"}], "market": "futures" }
POST /api/v1/screener/run  { "filters": { "rsiBelow": 30, "adxAbove": 25, "bb": "squeeze" }, "market": "futures" }   # full per-coin technical panel under "indicators"
```

All modes return a normalized `ScreenerRunResult { mode, status, matchedCount, matches:[{symbol,pair,last,metric,signal,indicators?}], blockers[], disclaimer }`. Every response carries a not-advice `disclaimer`. Timeframes: `5m / 15m / 1h / 4h / 1D / 1W`.

## Futures

Pass `market=futures` to screen ZebPay perpetual futures. Technical and open-interest screeners run on ZebPay futures OHLCV where covered (the liquid priority set) and Binance-derived data otherwise; funding-rate, long/short-ratio, and basis run live from OKX. Candles are fetched live at scan time, so a futures signal reflects a bar close within tens of seconds. Per-coin coverage is in the universe's `sourceFamilies[]` / `candleCoverage`: liquid coins are always live; newer coins still building ~1yr history are reported pending, never faked. A screen returns `status:"data_unavailable"` only when nothing is covered.

## Bulk board and natural language

```
GET  /api/v1/screener/board?market=futures&timeframe=1h&top=5     # every Live screener's matches in ONE cached pull
POST /api/v1/screener/translate { "prompt": "oversold coins on the daily", "market": "futures" }   # NL -> validated query -> runs it
```

MCP equivalents: `get_screener_board`, `translate_screener`.

## Push (do not poll)

Register a webhook endpoint (`POST /api/v1/organizations/{id}/webhook-endpoints` `{ url, events:["screener.snapshot"] }`) and a signed board snapshot is POSTed about every 15 minutes. Verify the `x-mazeledger-signature` HMAC (`sha256` of `timestamp` + `.` + `body`).

## Notes

- Read-only. The screener API never places an order; it needs only the `market:read` scope.
- Every match is a candidate, not advice; each response carries a `disclaimer`.

Full reference and OpenAPI: `https://mazeledger.ai/docs` and `https://mazeledger.ai/api/openapi`.
