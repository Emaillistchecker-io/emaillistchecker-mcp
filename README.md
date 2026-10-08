# Emaillistchecker.io MCP server — email verification for AI agents

Remote [Model Context Protocol](https://modelcontextprotocol.io) server that tells an AI agent whether an email address can receive mail before anything is sent to it.

- **Endpoint:** `https://platform.emaillistchecker.io/mcp` (Streamable HTTP, JSON responses)
- **Official MCP Registry:** `io.emaillistchecker/email-verification`
- **Website:** https://www.emaillistchecker.io
- **Agent docs:** https://platform.emaillistchecker.io/agent-gateway · [OpenAPI](https://platform.emaillistchecker.io/agent-gateway/openapi.json) · [llms.txt](https://platform.emaillistchecker.io/llms.txt) · [agent card](https://www.emaillistchecker.io/.well-known/agent.json)

The server is hosted; there is nothing to install. This repository documents how to connect.

## Get a key (no account needed)

Connect without a key and call the `get_api_key` tool, or:

```bash
curl -X POST https://platform.emaillistchecker.io/agent-gateway/register \
  -H 'Content-Type: application/json' \
  -d '{"name":"my-agent","purpose":"verify signup emails"}'
```

The key is shown once. Each key gets **25 free verifications per day** (unused credits do not roll over) and expires after 30 days; register again when it does. Send it as `Authorization: Bearer <api_key>`.

## Connect

**Claude Code**

```bash
claude mcp add --transport http emaillistchecker https://platform.emaillistchecker.io/mcp --header "Authorization: Bearer YOUR_KEY"
```

**Claude Desktop / Cursor / VS Code (`mcp.json`-style config)**

```json
{
  "mcpServers": {
    "emaillistchecker": {
      "type": "http",
      "url": "https://platform.emaillistchecker.io/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `get_api_key` | Get a free key (works without a key) |
| `verify_email` | Verify one address → `valid`, `invalid`, `risky` or `unknown` (1 credit) |
| `verify_batch` | Verify up to 25 addresses per call, with per-address verdicts |
| `get_batch_status`, `list_lists`, `get_list` | Follow saved verification lists |
| `explain_result` | Explain a verdict in plain English, with a send recommendation |
| `estimate_cost`, `get_credit_balance` | Check credits before running |

## Verdicts

- `valid`: the mailbox exists and accepts mail
- `invalid`: the address or domain cannot receive mail
- `risky`: accept-all (catch-all) domain, role or disposable address, or a Microsoft 365 address the mail server couldn't confirm
- `unknown`: the receiving server gave no definitive answer

## Limits

10 requests per minute per key, one request in flight per key, and a shared daily capacity for free agent keys (HTTP 429 until 00:00 UTC when it is used up). Inbox placement, blacklist monitoring and email finder need a full account at https://www.emaillistchecker.io.

## REST API

The same key works on the REST API: `POST https://platform.emaillistchecker.io/api/v1/verify`, `POST https://platform.emaillistchecker.io/api/v1/verify/batch`, `GET https://platform.emaillistchecker.io/api/v1/credits`. See the [OpenAPI description](https://platform.emaillistchecker.io/agent-gateway/openapi.json).
