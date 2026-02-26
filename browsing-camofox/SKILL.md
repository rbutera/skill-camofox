---
name: browsing-camofox
description: >-
  Browse the web using the camofox-mcp MCP server backed by a CamoFox anti-detect browser.
  Use when you need to fetch or interact with web pages — especially client-side rendered (CSR)
  apps, SPAs, or JavaScript-heavy sites where normal HTTP fetch returns empty or incomplete HTML.
  Also use when anti-detect fingerprinting is needed (e.g. scraping sites with bot detection).
  Provides navigation, clicking, typing, scrolling, screenshots, tab management, and search macros.
compatibility: >-
  Requires the camofox MCP server to be configured and a running CamoFox
  server instance. Works with any MCP-compatible agent.
---

# Browsing with CamoFox

Use the `camofox` MCP server to browse web pages through a real anti-detect browser (Camoufox, a Firefox fork with C++ fingerprint spoofing). No local browser is launched by the MCP server — it proxies to a remote CamoFox instance via REST.

## When to use this instead of fetch

- **Client-side rendered (CSR) / SPA pages**: Normal `fetch` or `curl` returns a bare HTML shell with no content. CamoFox renders JavaScript fully.
- **Bot-protected sites**: Sites with Cloudflare, DataDome, or similar bot detection. CamoFox spoofs browser fingerprints at the C++ level.
- **Interactive workflows**: Filling forms, clicking buttons, navigating multi-step flows.
- **Any page where `fetch` returned incomplete or empty content** — retry with CamoFox.

## Core workflow

1. **Navigate** to the URL and get an accessibility snapshot:

```
camofox:camofox_navigate  →  { url: "https://example.com" }
```

The snapshot returns a text representation of the page with element refs (e1, e2, e3…).

2. **Interact** with elements using their refs:

```
camofox:camofox_click   →  { ref: "e5" }
camofox:camofox_type    →  { ref: "e12", text: "search query" }
camofox:camofox_press   →  { key: "Enter" }
camofox:camofox_scroll  →  { direction: "down" }
```

3. **Re-snapshot** after interactions to see updated content:

```
camofox:camofox_snapshot
```

4. **Extract data** from the snapshot text, or use `camofox:camofox_links` to get all links on the page.

## Tool reference

| Tool | Purpose |
|---|---|
| `camofox:camofox_health` | Check CamoFox server is reachable |
| `camofox:camofox_navigate` | Open URL, returns accessibility snapshot |
| `camofox:camofox_snapshot` | Re-fetch current page snapshot |
| `camofox:camofox_click` | Click element by ref |
| `camofox:camofox_type` | Type text into an element |
| `camofox:camofox_press` | Press a keyboard key |
| `camofox:camofox_scroll` | Scroll page (up/down/left/right) |
| `camofox:camofox_navigate_tab` | Navigate existing tab to new URL |
| `camofox:camofox_search` | Use a search macro (see below) |
| `camofox:camofox_links` | Extract all links from current page |
| `camofox:camofox_screenshot` | Take screenshot (returns file path) |
| `camofox:camofox_tabs` | List open tabs |
| `camofox:camofox_close` | Close a tab |
| `camofox:camofox_close_all` | Close all tabs |
| `camofox:camofox_back` | Browser back |
| `camofox:camofox_forward` | Browser forward |

## Search macros

Use `camofox:camofox_search` with a macro name to search specific platforms:

`@google_search` · `@youtube_search` · `@amazon_search` · `@reddit_search` · `@wikipedia_search` · `@twitter_search` · `@yelp_search` · `@spotify_search` · `@linkedin_search` · `@instagram_search` · `@tiktok_search` · `@twitch_search`

## Tips

- Always call `camofox:camofox_health` first if you're unsure the server is up.
- After any interaction (click, type, press), call `camofox:camofox_snapshot` to see the result.
- Use `camofox:camofox_close` when done to close the tab you're using
- The MCP server is stateless — all browser state lives on the CamoFox server. Multiple agents can share one CamoFox instance using different `CAMOFOX_USER` values.
