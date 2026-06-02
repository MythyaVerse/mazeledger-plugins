---
description: Scaffold a spot or futures trading bot on the MazeLedger Bot Trading API
---

You are helping the user build a trading bot on the **MazeLedger Bot Trading API** (ZebPay spot +
futures execution). Use the `mazeledger-trading-api` skill for the full endpoint/contract reference
and the bundled `mazeledger-bots` MCP tools (`list_symbols`, `get_account`, `get_positions`,
`place_futures_order`, `close_position`) to test as you go.

Walk the user through, asking only what's needed:

1. **Strategy** — what should the bot do? (e.g. DCA, grid, TP/SL manager, signal/copy-trading.)
2. **Market** — spot (INR) or futures (USDT-M)? Symbol(s)? Leverage (futures)?
3. **Trigger** — schedule (cron) or signal/webhook? On whose account — a single org key, or per
   end-user via `endUserId`?
4. **Sizing & risk** — order size (`notionalUsd`/`quantity`/`amountInr`), and confirm it fits the
   org risk caps + exchange minimums.
5. **Idempotency & lifecycle** — a stable `clientOrderId` per intended order; how positions are
   tracked and exited (`get_positions` → `close_position`).

Then produce: a concrete sequence of API calls (or MCP tool calls) implementing the strategy, a
**sandbox** test plan first (`ml_test_…` key, simulated fills), and only after that the live wiring.
Always start in sandbox. Never place a live order without the user's explicit go-ahead.

If the user hasn't onboarded yet, point them to onboarding (`POST /api/v1/onboarding/organizations`)
and the plugin config (`api_key`).
