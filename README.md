# Programscape MCP server

Ask your AI assistant which startup credits, programs and perks fit your company, answered from checked records with
official sources.

**Server URL:** `https://programscape.com/api/mcp` (Streamable HTTP)

Programscape is an independent map of startup credits, programs and perks: cloud and AI credits, accelerator programs
and product discounts from AWS, Google Cloud, Microsoft Azure, OpenAI, Anthropic, NVIDIA, Stripe and more. Every
record carries its official source and the date it was last checked. Providers can't pay for placement.

This repository holds no server code. Programscape runs the server; this page explains how to connect to it.

## Connect

### Claude

Find **Programscape** in Claude's connector directory and add it, or add it yourself:
Settings → Connectors → **Add custom connector**, with the URL `https://programscape.com/api/mcp`.

More, with questions to try: [programscape.com/claude](https://programscape.com/claude).

### Claude Code

```bash
claude mcp add --transport http programscape https://programscape.com/api/mcp
```

### VS Code

In `.vscode/mcp.json`:

```json
{
  "servers": {
    "programscape": { "type": "http", "url": "https://programscape.com/api/mcp" }
  }
}
```

### Cursor

In `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "programscape": { "url": "https://programscape.com/api/mcp" }
  }
}
```

The server follows the standard protocol, so other MCP clients can connect too. Claude is the client we test.

## Tools

| Tool | What it does | Sign-in |
| --- | --- | --- |
| `build_route` | Returns the review Programscape would show a founder: the programs worth their time now, each one's standing and the questions that settle it, and what can wait | No |
| `profile_questions` | Returns the questions, with answer options, that shape a review | No |
| `search_programs` | Searches the catalog of startup credits, programs and discounts | No |
| `get_program` | Returns one program's record: what it offers, who decides, exclusions, sources and the date checked | No |
| `compare_programs` | Returns programs that serve the same goal across providers, side by side, with no winner | No |
| `whats_changed` | Returns what the latest checks against providers' own pages found | No |
| `save_plan_draft` | Saves a review as a one-day draft and returns a link to keep it on programscape.com | No |
| `my_plan` | Returns the founder's saved plan | Yes |
| `update_my_plan` | Saves answers and program choices to the founder's plan. Your assistant asks you before each save | Yes |
| `portfolio_perks` | Returns the deals a founder's venture firm or studio arranged, labelled as the partner's terms | Yes |
| `partner_perks_admin` | For a partner's admins: what needs a look on their perks list | Yes |

## Sign-in

Searching, comparing and building a review need no account. The first time a plan tool is used, your assistant asks
you to sign in to Programscape once, with Google or a one-time email code. There's no password. Sign-in is OAuth 2.1
with dynamic client registration and PKCE. You can disconnect an assistant from **Your plan** on programscape.com.

## What it never does

- Say you qualify for a program. The provider decides.
- Add amounts into a total. Each figure is that program's own, often an "up to" maximum with conditions.
- Pick a winning provider.

## Privacy and support

- Privacy notice: [programscape.com/privacy](https://programscape.com/privacy). The server receives the answers your
  assistant passes to a tool, never your conversation.
- Questions and suggestions: [programscape.com/suggest](https://programscape.com/suggest)

Programscape is made by Forgotten Things Labs LLC.
