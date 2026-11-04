# overpass.ch MCP server

[![mcptry](https://mcptry.com/badge/ch.overpass/overpass)](https://mcptry.com/servers/ch.overpass/overpass)

Hosted Overpass API with an MCP server. AI assistants connected to it query OpenStreetMap data for the whole planet, find places by name, look up heights, and answer from current data.

The data covers every street, shop, building and place mapped in OpenStreetMap and is updated every minute. The Medium and Large plans can also query the map at a past date, back to September 2012.

This repository holds the documentation and the registry entry of the hosted server at `https://api.overpass.ch/v1/mcp`. There is nothing to install.

- Website: https://overpass.ch
- Setup guide: https://overpass.ch/guides/overpass-mcp-server
- Docs: https://overpass.ch/docs#mcp
- Official MCP Registry: `ch.overpass/overpass`

## Setup

1. Create a free account and an API key at https://overpass.ch/signup.
2. Add the server to your assistant, either as a URL with the key in it:

   ```
   https://api.overpass.ch/v1/YOUR_API_KEY/mcp
   ```

   or as `https://api.overpass.ch/v1/mcp` with the key in the `X-API-Key` header (or `Authorization: Bearer YOUR_API_KEY`).

Transport: Streamable HTTP. Protocol versions 2026-07-28 and 2025-03-26 to 2025-11-25. Listing the tools does not need a key. Create a separate key for each assistant, so that you can revoke it on its own.

### Claude (web, desktop and mobile apps)

Customize → Connectors → **+ Add** → **Add custom connector**. Name: `overpass.ch`. URL: the URL with your key. Authentication: **No sign in**.

### ChatGPT

Open https://chatgpt.com/plugins, select **+** and **Add custom MCP server**. Enter a name, the URL with your key under **Connection**, and no authentication. Confirm the warning and select **Create as a plugin**. Your ChatGPT plan or workspace has to allow custom MCP servers.

### Claude Code

```sh
claude mcp add --transport http overpass https://api.overpass.ch/v1/mcp \
  --header "X-API-Key: YOUR_API_KEY"
```

### Cursor

`~/.cursor/mcp.json`, with the key in the environment variable `OVERPASS_KEY`:

```json
{
  "mcpServers": {
    "overpass": {
      "url": "https://api.overpass.ch/v1/mcp",
      "headers": { "X-API-Key": "${env:OVERPASS_KEY}" }
    }
  }
}
```

### VS Code

`.vscode/mcp.json`. VS Code asks for the key the first time and stores it:

```json
{
  "inputs": [
    { "type": "promptString", "id": "overpass-key", "description": "overpass.ch API key", "password": true }
  ],
  "servers": {
    "overpass": {
      "type": "http",
      "url": "https://api.overpass.ch/v1/mcp",
      "headers": { "X-API-Key": "${input:overpass-key}" }
    }
  }
}
```

### Other clients

Other clients that speak MCP over Streamable HTTP take the URL with the key, or the URL without it and the `X-API-Key` header.

### As an Agent Plugin

This repository is also an [Agent Plugin](https://agent-plugins.org): `plugin.json` and `mcp.json` add the server `overpass` at `https://api.overpass.ch/v1/mcp`. A plugin cannot carry your key, so add the header `X-API-Key` with your key to the server `overpass` in the settings of your client. Without it the tools are listed, and every call answers that a key is missing.

## Tools

| Tool | What it does | Counts as |
|---|---|---|
| `overpass_query` | Runs an Overpass QL or XML query on the full planet. The answer starts with counts and the date of the data, then one element per line. Answers over 30,000 characters are cut and can be read in parts with `offset`. | 1 request |
| `overpass_examples` | Finds tested example queries for a topic, from a cookbook of 99 queries. | free |
| `geocode_search` | Places, addresses and points of interest by name, in English, German or French, with an `overpass_area_id` for searching inside that place. | 1 geocoding request |
| `geocode_reverse` | The address, street, city or country at a point. | 1 geocoding request |
| `elevation_points` | Heights of points: a 2 m grid in Switzerland and Liechtenstein (swissALTI3D), a 30 m grid elsewhere on land (Copernicus), depths at sea (GEBCO). | 1 lookup per point |
| `elevation_profile` | Distance, ascent, descent and heights along a path. | 1 lookup per sample |
| `account_status` | Plan, use this month and the date of the data. | free |

All tools only read data. Overpass queries through MCP stop after 120 s at most.

## Example questions

- How many pharmacies in Zürich are within 200 m of a tram stop?
- Which mountain huts in Valais lie above 2,500 m?
- List every EV charger in the canton of Bern with operator and power, as a table.
- How many cafés did Lausanne have in 2015, and how many today? (Medium and Large plans)

## Plans

Tool calls count like the same calls of the REST API.

| Plan | Price a month | Overpass requests a month | Queries at a time | History |
|---|---|---|---|---|
| Free | 0 | 10,000 (30 a minute) | 1 | no |
| Small | CHF/EUR 19 or USD 25 | 100,000 | 2 | no |
| Medium | CHF/EUR 59 or USD 79 | 1 million | 6 | yes |
| Large | CHF/EUR 149 or USD 199 | 3 million | 10 | yes |

The free plan is for evaluation and development, and for personal, educational and non-profit projects; commercial use needs a paid plan. Every plan includes 100,000 geocoding requests a month. Prices exclude VAT, and a year costs ten months. Details: https://overpass.ch/pricing

## FAQ

**Do I need an API key?** Yes, for tool calls. The free plan works. Listing the tools works without a key.

**What happens when a result is very large?** The answer says how many elements there were. The assistant can read the next part with `offset`, raise `max_chars` up to 200,000, or narrow the query (`out count;`, `out center 50;`, a smaller area).

**How is this different from MCP servers that use overpass-api.de?** The public server overpass-api.de allows about 100 queries a day for anything that runs regularly, refuses scripts without their own User-Agent and bans addresses after repeated 429 errors. overpass.ch identifies you by your API key, publishes its limits and tells the assistant how long to wait when it reaches one.

**What is logged?** Tool calls are logged like API requests: the id of the key, never the key, and queries only as a hash.

## Data and licence

Data © OpenStreetMap contributors, available under the Open Database Licence (https://www.openstreetmap.org/copyright). Heights in Switzerland and Liechtenstein © swisstopo. overpass.ch is operated from Switzerland, with servers in Finland (EU). Contact: https://overpass.ch/contact
