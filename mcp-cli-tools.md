# MCP/CLI Tools

Loom+ exposes team operations through MCP and CLI APIs. Use these tools to let agents create missions, update projects, search wiki content, manage meetings, and resolve Loom+ links.

## MCP Endpoint

Use:

```json
{
  "mcpServers": {
    "loom+": {
      "url": "https://YOUR_DOMAIN/api/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_ACCESS_TOKEN"
      }
    }
  }
}
```

Create or copy the access token from `Admin -> Access Token`.

## Tool Groups

- Knowledge: list/create knowledge bases, upsert documents, ask a knowledge base, ask all knowledge bases.
- Projects and missions: list projects, create missions, update missions, inspect mission logs.
- Coordination: create and inspect coordination issues.
- Meetings: create, update, delete, and list Google meetings.
- Identity binding: bind Telegram or other platform identity to a Loom+ account.
- Logs and links: resolve Loom+ resource links and inspect operational results in the Logs page.

## CLI API

The CLI API uses the same access token model:

- `POST /api/cli/login`
- `GET /api/cli/tools`
- `POST /api/cli/run`

Use `loomcli` for a terminal workflow.
