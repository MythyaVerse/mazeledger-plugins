# MazeLedger Screener API: Codex (OpenAI Codex CLI) integration

Screen crypto markets from Codex, via the MazeLedger MCP server + AGENTS.md. Read-only (scope
`market:read`); it never places a trade.

## Setup

1. **Set your API key**:
   ```bash
   export MAZELEDGER_API_KEY="ml_test_"   # sandbox; or ml_live_ for production
   ```

2. **Add the MCP server**, merge [`config.toml`](./config.toml) into `~/.codex/config.toml`
   (or `.codex/config.toml` in a trusted project):
   ```toml
   [mcp_servers.mazeledger-bots]
   url = "https://mazeledger.ai/api/v1/mcp"
   bearer_token_env_var = "MAZELEDGER_API_KEY"
   ```
   Or run `codex mcp add` interactively. Codex's HTTP MCP sends `Authorization: Bearer
   $MAZELEDGER_API_KEY`; the MazeLedger MCP accepts the org key via Bearer.

3. **Add the instructions**, copy [`AGENTS.md`](./AGENTS.md) content into your project's `AGENTS.md`
   (or `~/.codex/AGENTS.md`) so Codex knows the screener API.

## Get a key

Onboard and mint a key (see AGENTS.md or https://mazeledger.ai/docs). For an evaluation key, email
contact@mazeledger.ai.

## Use it

Ask Codex: "run the RSI oversold screener on the daily", "screen ZebPay futures for a long buildup",
"pull the whole board", "translate: oversold coins on the daily". Sandbox and production keys both
read the same live data; screeners are read-only either way.

## Notes

- Codex HTTP MCP supports `bearer_token_env_var` (Authorization: Bearer); the MazeLedger MCP also
  accepts the key via `x-api-key`.
- `codex mcp login mazeledger-bots` is available if OAuth is added later (not required today).
