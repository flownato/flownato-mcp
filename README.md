# Flownato MCP

[![MCP Badge](https://lobehub.com/badge/mcp/flownato-flownato-mcp)](https://lobehub.com/mcp/flownato-flownato-mcp)

A remote, read-only [Model Context Protocol](https://modelcontextprotocol.io) server for mobile UI/UX design research. Built for product designers and developers, it gives MCP-compatible assistants (Claude, Cursor, Codex, Windsurf and others) access to Flownato's library of real mobile app screens, user flows and UI patterns, with every answer tied to published screen evidence.

- **Endpoint:** `https://agent.flownato.com/mcp` (Streamable HTTP)
- **Auth:** OAuth 2.1 authorization code with PKCE. Your browser opens Flownato sign-in and a read-only consent screen. No API keys to paste.
- **Access:** every signed-in Flownato account can use every tool. Free accounts get the same access as on the website: complete journeys that flownato.com marks free, plus the first 3 screens of every other journey. Premium opens every screen.
- **Website:** https://www.flownato.com/mcp

## What it does

Flownato records real mobile apps (mostly Indian consumer apps such as Swiggy, Zepto, MakeMyTrip, BookMyShow, Nykaa, plus IKEA and Wikipedia), then publishes reviewed screens, complete journeys and a UI pattern taxonomy. The MCP server lets an assistant search and compare that evidence instead of guessing what an interface looks like.

Typical questions it can answer with citations:

- "How do grocery apps handle the delivery slot step in checkout?"
- "Show me phone-number entry screens across booking apps."
- "Compare the onboarding journey in Cricbuzz and FanCode."
- "Which apps use a bottom sheet for store selection?"

## Tools

| Tool | Purpose |
|---|---|
| `whoami` | Reports the authenticated membership, what that tier can see, and the client. |
| `search_ui_library` | Finds concrete journey examples for a research question. |
| `search_ui_screens` | Screen-level search with exact interface text matching. |
| `analyze_ui_pattern` | Measures one screen pattern, UI element, flow pattern or flow action across an optional app or category scope. |
| `explore_ui_patterns` | Discovers recurring patterns when you do not yet know which one to analyze. |
| `compare_ui_flows` | Compares the same product moment across two or three named apps. |
| `inspect_app_library` | Summarizes one app's published coverage, patterns, elements and journeys. |
| `check_ui_presence` | Scoped yes/no presence checks with evidence. |
| `inspect_library_coverage` | Paginated inventory of what is published. |
| `inspect_ui_resources` | Exact release-bound follow-up retrieval for returned `resourceRef` objects. |
| `get_screen_image` | Returns a published screen image inline after another tool supplies its asset ID. |

All tools are read-only. Results cite the exact journey and step identifiers they were built from, and pattern counts are descriptive, never presented as best practice.

## Connect

In clients that accept a remote `mcpServers` configuration, use:

```json
{
  "mcpServers": {
    "flownato": {
      "type": "http",
      "url": "https://agent.flownato.com/mcp"
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport http flownato https://agent.flownato.com/mcp
```

On first use the client opens `https://api.flownato.com/oauth/authorize` in your browser. Sign in with your Flownato account and approve read-only access. Tokens are short lived and refresh silently until you revoke the grant.

Discovery documents:

- Protected resource metadata: `https://agent.flownato.com/.well-known/oauth-protected-resource/mcp`
- Authorization server metadata: `https://api.flownato.com/.well-known/oauth-authorization-server`

## Access and limits

| Plan | What the MCP returns |
|---|---|
| Not signed in | The endpoint answers `401` with OAuth discovery metadata, so your client opens Flownato sign-in. Creating an account is free. |
| Free account | All eleven tools. Journeys marked free on flownato.com (up to three per app) return every screen. Other journeys return their first 3 screens; the rest come back with `locked: true`, no asset ID and no image URL. Every result carries an `access` block with the locked counts and an upgrade link, and `get_screen_image` answers `PREMIUM_REQUIRED` for a locked screen. |
| Premium | All eleven tools with the complete library. |

Both tiers are metered at a default safety limit of 500 tool calls per UTC day.

Prompts, arguments and results are not stored. Screenshot URLs are signed and short lived, and the image endpoint rechecks membership when the image is fetched.

## Privacy and scope

The server reads only the published product catalog: reviewed, immutable releases with privacy redaction applied. It has no access to raw crawl data, model output or other users' accounts. It answers UI and product-design research questions and declines unrelated requests.

## Source

This repository documents the hosted service. The server itself is part of Flownato's private product codebase (Python, FastAPI) and is not open source.

## Marketplace metadata

[`lhm.plugin.json`](lhm.plugin.json) declares the existing LobeHub listing's category,
remote endpoint, and all eleven MCP tool schemas. Its category is `developer`
(displayed as **Developer Skills** on LobeHub), reflecting its use for UI/UX design
and product development.

The endpoint requires OAuth before protocol initialization and tool discovery.
An unauthenticated request returning `401` is expected; use the protected-resource
metadata above to start sign-in. Directory crawlers cannot discover the tool list
from an unauthenticated connection alone.

The server currently exposes tools, with no MCP prompt templates or MCP resources.
Returned `resourceRef` objects are arguments to `inspect_ui_resources`; they are
not entries in the MCP `resources/list` capability. Keep marketplace declarations
consistent with the server's actual capabilities.

The public badge above supports LobeHub's README claim-status check. Prefer this
public verification route when available. Updating this public listing does not
justify granting a directory read/write access to private GitHub repositories.

Already verified maintainers can update the existing listing with the official
[`@lobehub/market-cli`](https://lobehub.com/publish-mcp/skill.md) (Node.js 22 or newer):

```bash
# Sign in to LobeHub, then check existing GitHub verification:
npx -y @lobehub/market-cli login
npx -y @lobehub/market-cli github status

# Requires ownership verification; run after reviewing lhm.plugin.json:
npx -y @lobehub/market-cli plugin update --dir "$PWD"
```

Review requested permissions before connecting any GitHub account. If a claim or
update requires broad `repo` or `workflow` access, leave that connection disabled
and request a narrower verification method from LobeHub.

When tool signatures change, refresh `tools` from the authenticated server's
`tools/list` response before publishing. Keep `prompts` and `resources` empty
unless the server implements them. The listing version remains `1.0.0` for this
metadata correction; it does not represent a new server release. Verify the
[public listing](https://lobehub.com/mcp/flownato-flownato-mcp?activeTab=score)
after each update.

## Links

- Product: https://www.flownato.com
- MCP page: https://www.flownato.com/mcp
- Pattern library: https://www.flownato.com/patterns
- Contact: hello@flownato.com
