# Datalastic MCP Server

Official [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server for [Datalastic](https://datalastic.com) — real-time vessel tracking, port data, marine weather, and maritime intelligence for any MCP-compatible AI client.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## Overview

The Datalastic MCP server exposes **25 tools** across six categories:

- **Live vessel positions** — track ships in real time by MMSI, IMO, or name
- **Vessel registry** — specs, dimensions, flag, and tonnage for 750,000+ vessels
- **Port data** — global port and terminal directory with UN/LOCODEs
- **Area search** — all vessels currently within a radius of any point or port
- **Marine weather** — current conditions and 7-day forecasts at any location
- **Maritime intelligence** *(Maritime Reports add-on)* — ownership, inspections, casualties, dry dock, engine specs, sale & purchase transactions

The server runs at `mcp.datalastic.com` — no local installation required.

---

## Getting Started

### Sign in with your Datalastic account *(recommended)*

No API key needed. Just add `https://mcp.datalastic.com/mcp` as the server URL in your client with no headers or credentials. When you connect, your client will automatically open a browser window to sign in with your Datalastic account. Once you approve, you're connected — the client handles everything else.

To disconnect, use the disconnect option in your MCP client or revoke access from your [Datalastic account settings](https://datalastic.com).

### API key

Sign up at [datalastic.com/pricing](https://datalastic.com/pricing/) and subscribe to a plan. Your API key is delivered by email and visible in your account dashboard.

For clients that support custom request headers in their config file, use the examples in the [Client Configuration](#client-configuration) section below. For Claude Code, use the CLI command instead.

---

## Client Configuration

### Claude Code

Run this command to add the server with your API key:

```
claude mcp add --scope user --transport http datalastic https://mcp.datalastic.com/mcp --header "X-Api-Key: YOUR_API_KEY"
```

For Claude Desktop, use OAuth (sign in with your Datalastic account above) — custom header authentication via config file is not yet supported.

### Cursor

Edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "datalastic": {
      "type": "http",
      "url": "https://mcp.datalastic.com/mcp",
      "headers": {
        "X-Api-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

### VS Code (GitHub Copilot)

Edit `.vscode/mcp.json` in your workspace, or your user-level `mcp.json`:

```json
{
  "servers": {
    "datalastic": {
      "type": "http",
      "url": "https://mcp.datalastic.com/mcp",
      "headers": {
        "X-Api-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

### Windsurf

Edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "datalastic": {
      "type": "http",
      "url": "https://mcp.datalastic.com/mcp",
      "headers": {
        "X-Api-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

### Gemini CLI

Edit `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "datalastic": {
      "httpUrl": "https://mcp.datalastic.com/mcp",
      "headers": {
        "X-Api-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

### Any Streamable HTTP client

```
URL:    https://mcp.datalastic.com/mcp
Header: X-Api-Key: YOUR_API_KEY
```

---

## Tools

13 tools are available to all plans. 11 additional tools become available with the **Maritime Reports** add-on.

### Vessel Tracking

| Tool | Description |
|------|-------------|
| `get_vessel` | Live position, speed, heading, and navigation status for a single vessel |
| `get_vessel_pro` | Like `get_vessel` plus recognized destination port, ETA, actual departure time, and draught |
| `get_vessel_info` | Static specifications: dimensions, tonnage, cargo capacity, year built, flag, callsign |
| `find_vessels` | Search the registry by name, type, flag, tonnage, or dimensions |
| `get_vessels_bulk` | Live positions for up to 100 vessels in one call |
| `get_vessel_history` | Historical AIS track for a vessel (data available since 2021-08-10) |

> **Choosing the right vessel tool:** start with `get_vessel` for a live position. Use `get_vessel_pro` only when you need the destination port, ETA, or draught.

### Ports

| Tool | Description |
|------|-------------|
| `find_ports` | Search the port registry by name, UN/LOCODE, country, type, or coordinates |
| `get_port` | Full port detail including terminals, operators, addresses, and coordinates |

### Area & Weather

| Tool | Description |
|------|-------------|
| `get_vessels_in_radius` | All vessels currently within a radius (max 50 NM) of a point, port, or vessel |
| `get_weather` | Marine weather at any location — current conditions and/or a 7-day forecast |

### Maritime Intelligence *(Maritime Reports add-on)*

| Tool | Description |
|------|-------------|
| `intel_ownership` | Beneficial owner, operator, technical and commercial manager, P&I club |
| `intel_inspections` | Port State Control inspection records — detentions and deficiencies |
| `intel_casualties` | Recorded incidents: groundings, collisions, fires, machinery failures |
| `intel_drydock` | Next dry-dock date, special-survey date, and IOPP certificate expiry |
| `intel_class` | Classification society, principal dimensions, and next survey dates |
| `intel_engine` | Main engine model, builder, propulsion type, and maximum continuous output |
| `intel_spd` | Sale-and-purchase and demolition transactions — buyer, seller, reported price |
| `intel_companies` | Maritime company profiles: owners, operators, managers, and contact details |
| `sea_route` | Schematic sea route and distance in km / NM between two ports or coordinates. For distance estimation and visualization — not for real-world navigation. |
| `estimated_vessel_position` | Estimated current position for vessels out of terrestrial AIS range (open ocean) |
| `intel_report_request` | Submit a full bulk export of any Maritime Reports dataset |

> **Don't have the add-on?** When your plan doesn't include Maritime Reports, the `intel_info` tool appears in its place. It describes what the add-on offers and how to enable it. Once you upgrade, the intelligence tools appear automatically in the same session — no reconnect needed.

### Bulk Reports *(async)*

| Tool | Description |
|------|-------------|
| `report_request` | Submit an async export: `vessel_list`, `port_list`, `inradius_history`, or `request_usage` |
| `report_status` | Poll a report job; returns a download URL when complete |
| `report_list` | List your recent report jobs and statuses |

> Reports are generated asynchronously and can take minutes. The server returns a `result_url` to download — it never fetches the file itself. Some exports (full vessel list, area history) can be large and consume API credits.

---

## Example Prompts

```
Where is the MSC Oscar right now and what is its destination?

Show me all tankers within 20 nautical miles of Rotterdam.

What is the weather forecast at the Port of Singapore for the next 7 days?

Who is the beneficial owner of IMO 9839179?

Get the Port State Control inspection history for the Ever Given.

What is the sea route distance from Shanghai to Los Angeles?

Find all bulk carriers built after 2018 flagged in the Marshall Islands.

Which vessels does Maersk operate?

Show me all vessels with dry docks scheduled in the next 30 days.

What incidents has this tanker been involved in over the past 5 years?
```

---

## Links

- [Datalastic website](https://datalastic.com)
- [API documentation](https://docs.datalastic.com)
- [Pricing & plans](https://datalastic.com/pricing/)
