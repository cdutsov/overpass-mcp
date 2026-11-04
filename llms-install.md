# Installing the overpass.ch MCP server

overpass.ch is a hosted remote MCP server. There is nothing to download, build or run locally.

1. Ask the user for their overpass.ch API key. If they do not have one, they create a free account and a key at https://overpass.ch/signup (keys are on https://overpass.ch/dashboard/keys).
2. Add a remote MCP server named `overpass` with:
   - URL: `https://api.overpass.ch/v1/mcp`
   - Transport: Streamable HTTP
   - Header: `X-API-Key: <the key>`
   If the client does not support headers, use the URL `https://api.overpass.ch/v1/<the key>/mcp` instead.
3. Check the connection by calling the tool `account_status`. It shows the plan of the key and does not count as a request.

Do not write the key into files that are shared or committed; use an environment variable or the secret store of the client where it has one.
