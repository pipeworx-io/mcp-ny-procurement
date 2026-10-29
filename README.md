# mcp-ny-procurement

New York State Procurement MCP — State contract awards & procurement (keyless).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1856+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

| Tool | Description |
|------|-------------|
| `ny_procurement_awards` | Search NEW YORK STATE government contract awards & procurement from the official Procurement Report for State Authorities on data.ny.gov (the §2800 Public Authorities Law filings collected by the NY Authorities Budget Office). Each result is a procurement contract awarded by a New York State authority (the awarding agency) with the winning vendor, the contract dollar amount, the procurement type, the award/solicitation process, the description of goods/services, and award/begin dates. Filter by vendor, awarding agency/authority, keyword, procurement type, fiscal year, and/or a minimum amount; results are ranked by contract amount (biggest awards first) by default. Use this for questions like "who won the biggest New York State contracts", "top vendors to the Metropolitan Transportation Authority", or "NY state software contracts". This is NEW YORK STATE data (not federal, not New York City). |
| `ny_procurement_vendor` | Aggregate a vendor's NEW YORK STATE contract awards from the Procurement Report for State Authorities on data.ny.gov: total dollars awarded, number of contracts, and a breakdown by awarding agency/authority and by fiscal year. Matches the vendor name as a case-insensitive substring, so it also surfaces name variants (e.g. "IBM CORP" vs "IBM LC LODGING"). Use this to size up a single vendor's business with New York State authorities. This is NEW YORK STATE data (not federal, not New York City). |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "ny-procurement": {
      "url": "https://gateway.pipeworx.io/ny-procurement/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/ny-procurement/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1856+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ny_procurement_awards \
  -H 'Content-Type: application/json' \
  -d '{"vendor":"IBM","agency":"Metropolitan Transportation Authority","limit":20}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ny_procurement_awards`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-ny-procurement` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/ny-procurement/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "ny-procurement": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-ny-procurement"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Ny Procurement data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
