# Installing the overpass.ch MCP server

overpass.ch is a hosted remote MCP server. There is nothing to download, build or run locally.

1. Add a remote MCP server named `overpass` with:
   - URL: `https://api.overpass.ch/mcp`
   - Transport: Streamable HTTP
   A client that supports OAuth opens overpass.ch on first use: the user signs in (or creates a free account at https://overpass.ch/signup) and clicks Allow. In Claude, the user can add overpass.ch from the Claude directory instead: https://claude.ai/directory/overpass-ch.
2. A client without OAuth uses an API key. Ask the user for their overpass.ch API key (keys are on https://overpass.ch/dashboard/keys) and add the server with:
   - URL: `https://api.overpass.ch/v1/mcp`
   - Header: `X-API-Key: <the key>`
   If the client does not support headers, use the URL `https://api.overpass.ch/v1/<the key>/mcp` instead.
3. Check the connection by calling the tool `account_status`. It shows the plan of the key and does not count as a request.

Do not write the key into files that are shared or committed; use an environment variable or the secret store of the client where it has one.
