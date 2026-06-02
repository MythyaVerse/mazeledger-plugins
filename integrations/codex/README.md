# MazeLedger Bots — Codex (OpenAI Codex CLI) integration

Build spot & futures trading bots from Codex, via the MazeLedger MCP server + AGENTS.md.

## Setup

1. **Set your API key**:
   ```bash
   export MAZELEDGER_API_KEY="ml_test_…"   # sandbox; or ml_live_… for real orders
   ```

2. **Add the MCP server** — merge [`config.toml`](./config.toml) into `~/.codex/config.toml`
   (or `.codex/config.toml` in a trusted project):
   ```toml
   [mcp_servers.mazeledger-bots]
   url = "https://mazeledger.ai/api/v1/mcp"
   bearer_token_env_var = "MAZELEDGER_API_KEY"
   ```
   Or run `codex mcp add` interactively. Codex's HTTP MCP sends `Authorization: Bearer
   $MAZELEDGER_API_KEY`; the MazeLedger MCP accepts the org key via Bearer.

3. **Add the instructions** — copy [`AGENTS.md`](./AGENTS.md) content into your project's `AGENTS.md`
   (or `~/.codex/AGENTS.md`) so Codex knows the API + how to build bots.

## Get a key

Onboard and mint a key (see AGENTS.md or https://mazeledger.ai/docs).

## Use it

Ask Codex: "Place a 5x DOGEUSDT futures order for user-123", "DCA ₹500 into BTC", "close the
position". Start with a sandbox key (simulated, free).

## Notes

- Codex HTTP MCP supports `bearer_token_env_var` (Authorization: Bearer) — the MazeLedger MCP was
  updated to accept the key via Bearer in addition to `x-api-key`.
- `codex mcp login mazeledger-bots` is available if OAuth is added later (not required today).
