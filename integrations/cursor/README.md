# MazeLedger Screener API: Cursor integration

Screen crypto markets from Cursor, via the MazeLedger MCP server + a rule. Read-only (scope
`market:read`); it never places a trade.

## Setup

1. **Set your API key** as an environment variable (Cursor reads `${env:...}` in MCP config):
   ```bash
   export MAZELEDGER_API_KEY="ml_test_"   # sandbox; or ml_live_ for production
   ```
   (On Windows, set it in your system environment variables.)

2. **Add the MCP server**, copy [`mcp.json`](./mcp.json) into your project's `.cursor/mcp.json`
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

3. **Add the rule (the "skill")**, copy [`rules/mazeledger-trading-api.mdc`](./rules/mazeledger-trading-api.mdc)
   into your project's `.cursor/rules/`. It gives Cursor the full screener API contract.

## Get a key

Onboard and mint a key (see the rule or https://mazeledger.ai/docs):
`POST /api/v1/onboarding/organizations` returns a sandbox key. For an evaluation key, email
contact@mazeledger.ai.

## Use it

Ask Cursor: "run the RSI oversold screener on the daily", "screen ZebPay futures for a long buildup",
"pull the whole board", "translate: oversold coins on the daily". Sandbox and production keys both
read the same live data; screeners are read-only either way.

## Notes

- Cursor supports remote HTTP MCP with custom headers natively, so `x-api-key` works directly.
