# MazeLedger Bots — Cursor integration

Build spot & futures trading bots on ZebPay from Cursor, via the MazeLedger MCP server + a rule.

## Setup

1. **Set your API key** as an environment variable (Cursor reads `${env:…}` in MCP config):
   ```bash
   export MAZELEDGER_API_KEY="ml_test_…"   # sandbox; or ml_live_… for real orders
   ```
   (On Windows, set it in your system environment variables.)

2. **Add the MCP server** — copy [`mcp.json`](./mcp.json) into your project's `.cursor/mcp.json`
   (or merge into `~/.cursor/mcp.json` for all projects):
   ```json
   {
     "mcpServers": {
       "mazeledger-bots": {
         "url": "https://mazeledger.ai/api/v1/mcp",
         "headers": { "x-api-key": "${env:MAZELEDGER_API_KEY}" }
       }
     }
   }
   ```
   Reload Cursor; the `mazeledger-bots` tools appear in Settings → MCP.

3. **Add the rule (the "skill")** — copy [`rules/mazeledger-trading-api.mdc`](./rules/mazeledger-trading-api.mdc)
   into your project's `.cursor/rules/`. It gives Cursor the full API contract + bot-building guidance.

## Get a key

Onboard and mint a key (see the rule or https://mazeledger.ai/docs):
`POST /api/v1/onboarding/organizations` → sandbox key → connect ZebPay keys → risk-ack → live key.

## Use it

Ask Cursor: "Place a 5x DOGEUSDT futures order for user-123", "DCA ₹500 into BTC for each user",
"close user-123's position". Start with a sandbox key (simulated, free).

## Notes

- Cursor supports remote HTTP MCP with custom headers natively, so `x-api-key` works directly.
