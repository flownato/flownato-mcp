# Flownato MCP

A remote, read-only [Model Context Protocol](https://modelcontextprotocol.io) server for mobile UI research. It gives MCP-compatible assistants (Claude, Cursor, Codex, Windsurf and others) access to Flownato's library of real mobile app screens, user flows and UI patterns, with every answer tied to published screen evidence.

- **Endpoint:** `https://agent.flownato.com/mcp` (Streamable HTTP)
- **Auth:** OAuth 2.1 authorization code with PKCE. Your browser opens Flownato sign-in and a read-only consent screen. No API keys to paste.
- **Access:** connecting is free and `whoami` works on any account. Library research tools require a Flownato Premium membership.
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
| `whoami` | Reports the authenticated membership and client. Works on free accounts. |
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

Claude Desktop, Claude Code, Cursor and most clients accept a remote server entry like this:

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
| Free account | `whoami`, plus a structured Premium-required response from library tools before any catalog query runs |
| Premium | All eleven tools, metered at a default safety limit of 500 tool calls per UTC day |

Prompts, arguments and results are not stored. Screenshot URLs are signed and short lived, and the image endpoint rechecks membership when the image is fetched.

## Privacy and scope

The server reads only the published product catalog: reviewed, immutable releases with privacy redaction applied. It has no access to raw crawl data, model output or other users' accounts. It answers UI and product-design research questions and declines unrelated requests.

## Source

This repository documents the hosted service. The server itself is part of Flownato's private product codebase (Python, FastAPI) and is not open source.

## Links

- Product: https://www.flownato.com
- MCP page: https://www.flownato.com/mcp
- Pattern library: https://www.flownato.com/patterns
- Contact: hello@flownato.com
