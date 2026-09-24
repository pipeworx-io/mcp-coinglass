# mcp-coinglass

CoinGlass MCP — wraps the CoinGlass Derivatives API v4 (coinglass.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `coinglass_funding_rate` | Funding rates for BTC across exchanges (perpetual futures). Returns per-exchange current funding rate, interval, and next funding time for a coin symbol. Example: coinglass_funding_rate({ symbol: "BTC", _apiKey: "your-key" }) |
| `coinglass_open_interest` | Open interest for BTC across exchanges (perpetual futures). Returns per-exchange open interest in USD plus 24h change for a coin symbol. Example: coinglass_open_interest({ symbol: "BTC", _apiKey: "your-key" }) |
| `coinglass_liquidations` | Recent liquidations for BTC (aggregated long vs short liquidation USD over the latest period). Example: coinglass_liquidations({ symbol: "BTC", _apiKey: "your-key" }) |
| `coinglass_long_short_ratio` | Long/short ratio for BTC (global account long vs short percentage and ratio on an exchange). Example: coinglass_long_short_ratio({ symbol: "BTC", exchange: "Binance", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "coinglass": {
      "url": "https://gateway.pipeworx.io/coinglass/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/coinglass/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/coinglass_funding_rate`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "coinglass": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-coinglass"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-coinglass
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Coinglass data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
