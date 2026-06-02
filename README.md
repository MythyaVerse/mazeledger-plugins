# MazeLedger Plugins

AI-client plugins & integrations for the **MazeLedger Bot Trading API** — build spot & futures
trading bots in natural language. Your AI assistant talks to MazeLedger's hosted MCP
server; MazeLedger handles execution, risk caps, idempotency, audit, and a free sandbox. Each
end-user brings their **own ZebPay API keys (BYO)** — MazeLedger never custodies funds.

- **API base:** https://mazeledger.ai · **Docs:** https://mazeledger.ai/docs · **OpenAPI:** https://mazeledger.ai/api/openapi
- **MCP endpoint:** `https://mazeledger.ai/api/v1/mcp` (auth via `x-api-key` or `Authorization: Bearer`)
- **Keys:** `ml_test_…` (sandbox — simulated, free) or `ml_live_…` (real orders). **Start in sandbox.**
- **Supported exchange:** ZebPay — spot (INR) + futures (USDT-M); end-users connect their own exchange API keys (BYO).

## Install

### Claude Code (one command)

```
/plugin marketplace add MythyaVerse/mazeledger-plugins
/plugin install mazeledger-bots@mazeledger
```

You'll be prompted for your MazeLedger API key (stored securely). Then ask Claude to build/run a
bot, or run `/build-bot` for a guided scaffold.

### Cursor

Copy [`integrations/cursor/mcp.json`](integrations/cursor/mcp.json) into your `.cursor/mcp.json`, add
the rule from [`integrations/cursor/rules/`](integrations/cursor/rules), and set `MAZELEDGER_API_KEY`.
Details: [`integrations/cursor/README.md`](integrations/cursor/README.md).

### Codex

Merge [`integrations/codex/config.toml`](integrations/codex/config.toml) into `~/.codex/config.toml`,
use [`integrations/codex/AGENTS.md`](integrations/codex/AGENTS.md), and set `MAZELEDGER_API_KEY`.
Details: [`integrations/codex/README.md`](integrations/codex/README.md).

## MCP tools

`list_symbols`, `get_account`, `get_positions`, `place_futures_order`, `close_position`. The bundled
skill/instructions add the full REST contract (spot + futures + onboarding + end-users + reporting).

## What's in this repo

| Path | Purpose |
|------|---------|
| `.claude-plugin/marketplace.json` | Claude Code marketplace manifest |
| `plugins/mazeledger-bots/` | The Claude Code plugin — manifest, MCP config, skill, `/build-bot` |
| `integrations/` | Cursor & Codex MCP configs + instructions |

## Safety

Sandbox first. Per-org **risk caps** (order size, leverage, daily notional, open positions) +
exchange minimums. BYO keys should be **trade-only** (no withdrawal). Idempotency via
`clientOrderId`; ambiguous failures are recorded `unknown`, never lost.

---

License: [MIT](LICENSE) · Support: contact@mazeledger.ai
