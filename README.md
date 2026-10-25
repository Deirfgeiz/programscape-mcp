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
The same hosted service answers every client at this URL; there is no hosted package to install or update.

## Tools

| Tool | What it does | Sign-in |
| --- | --- | --- |
| `build_route` | Returns the review Programscape would show a founder: the programs worth their time now, each one's standing and the questions that settle it, and what can wait | No |
| `profile_questions` | Returns the questions, with answer options, that shape a review | No |
| `search_programs` | Searches the catalog of startup credits, programs and discounts | No |
| `get_program` | Returns one program's record: what it offers, who decides, exclusions, sources and the date checked | No |
| `compare_programs` | Returns programs that serve the same goal across providers, side by side, with no winner | No |
| `whats_changed` | Returns what the latest checks against providers' own pages found | No |
| `save_plan_draft` | Before sign-in, saves a review as a one-day draft and returns a link to keep it on programscape.com | No |
| `my_plan` | Returns the founder's saved plan and its current review, with saved application progress and reported balances | Yes |
| `update_my_plan` | Saves the answers, program choices and application progress the founder supplies. Your assistant asks you before each save | Yes |
| `report_balance` | Records what a provider shows as left on one kept, approved program, in the provider's unit, with the reading time and who reported it; optionally sets a low-balance reminder | Yes |
| `portfolio_perks` | For a founder who joined a venture firm's or studio's perks through its invite link: returns that portfolio's deals, labelled as the partner's terms. Joining no portfolio returns no portfolio deals | Yes |
| `partner_perks_admin` | For an admin of a partner's perks list: returns what needs a look. Founders who are not admins cannot read that list's administration details | Yes |

The connector lists 12 tools before sign-in and 11 afterward, when the temporary draft tool is replaced by direct
saves to the account's plan. Catalog search, comparisons and a new review work without an account. Sign-in gives a
tool access only to the plans and partner lists that account may read or edit. If several plans are available,
choose a plan from the returned choices and send its `planId` when reading or saving it.

An application marked approved records what the founder reported; the provider still decides. Reported balances
stay separate from awards and from other programs. A runway estimate from the founder's local router is labelled
as an estimate and never sets a program's expiration date.

### Continuing a saved plan

Call `my_plan` with `{}` to read a single available plan. When several plans are available, the MCP tool result sets
`isError: true` and includes `structuredContent.selectionRequired: true` with named plan choices. Clients should
inspect those structured choices, ask which company the founder means, retain any previously cached plan data,
and retry with the chosen `planId`. Use that ID in later calls:

```json
{ "planId": "<chosen-plan-id>" }
```

`update_my_plan` changes only supplied fields. It can save `answers`, `keep`, `setAside` and progress for a program
already kept on the selected plan. Include the version returned by `my_plan` as `expectedVersion` to refuse a save
if someone else changed the plan in between. For example, after the founder reports submitting an application:

```json
{
  "planId": "<chosen-plan-id>",
  "expectedVersion": 3,
  "progress": [
    {
      "programId": "<kept-program-id>",
      "pursuit": "waiting",
      "nextAction": "Check the provider's response",
      "lifecycle": { "submittedOn": "2026-10-25" }
    }
  ]
}
```

Progress includes `pursuit`, `nextAction`, `reportedResult`, `resultNote` and lifecycle dates (`submittedOn`,
`approvedOn`, `declinedOn`, `startedOn`, `expiresOn`). A supplied `null` clears that field; omitted fields keep their
saved values. The save receipt returns the saved version, when it was updated, who last saved it and the progress
fields that changed. Readback labels progress as reported by the plan's team, including the history of programs
set aside. It does not verify a provider decision. Awards, balances and reminder numbers have their own controls;
progress patches cannot change them.

## Sign-in

Searching, comparing and building a review need no account. The first time a plan tool is used, your assistant asks
you to sign in to Programscape once, with Google or a one-time email code. There's no password. Sign-in is OAuth 2.1
with dynamic client registration and PKCE. You can disconnect an assistant from **Your plan** on programscape.com.

To continue in a fresh conversation, ask your assistant to read your saved Programscape plan after connecting the
same Programscape account. Saved answers, choices and application progress belong to that plan.

## The local credit router

[Programscape's open-source credit router](https://github.com/Deirfgeiz/programscape-router) is a separate optional
program that runs on your own machine with your own provider keys. It can report provider balances to your saved
plan after you sign in. The hosted connector above works without the router. Router releases are published as
[`@programscape/router` on npm](https://www.npmjs.com/package/@programscape/router).

## Public listings and updates

- [Claude connector directory](https://claude.ai/directory/programscape)
- [Official MCP Registry entry](https://registry.modelcontextprotocol.io/v0.1/servers/com.programscape%2Fprogramscape/versions/latest)

`server.json` versions the registry's connection metadata. The hosted server reports its own implementation
version during MCP `initialize`; router versions belong to the npm package above. A registry metadata version is
not a separate hosted installation. [Publishing and verification](PUBLISHING.md) explains how these surfaces are
kept current.

## What it never does

- Say you qualify for a program. The provider decides.
- Add amounts into a total. Each figure is that program's own, often an "up to" maximum with conditions.
- Pick a winning provider.

## Privacy and support

- Privacy notice: [programscape.com/privacy](https://programscape.com/privacy). The server receives the answers your
  assistant passes to a tool, never your conversation.
- Questions and suggestions: [programscape.com/suggest](https://programscape.com/suggest)

Programscape is made by Forgotten Things Labs LLC.
