# 🌤️ Weather MCP Server

> A powerful, remote Model Context Protocol (MCP) server that brings real-time weather data to AI assistants and applications.

[![Deployed on Cloudflare Workers](https://img.shields.io/badge/Deployed%20on-Cloudflare%20Workers-orange?style=for-the-badge&logo=cloudflare)](https://weather-mcp.genaijake.workers.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)](https://typescriptlang.org)
[![MCP Compatible](https://img.shields.io/badge/MCP-Compatible-blue?style=for-the-badge)](https://modelcontextprotocol.io)

---

## 🎯 What This Does

Transform any AI assistant into a weather-savvy companion! This MCP server connects your AI to live weather data from the National Weather Service, providing:

- **🌡️ Detailed Weather Forecasts** - Get multi-day forecasts for any US location
- **⚠️ Real-time Weather Alerts** - Access active weather warnings and advisories
- **🔐 Secure OAuth Access** - Protected with industry-standard authentication
- **🌍 Global Deployment** - Accessible worldwide via Cloudflare's edge network

---

## 🚀 Quick Start

### For AI Assistant Users

**Live Server URL:** `https://weather-mcp.genaijake.workers.dev/sse`

#### Connect with Claude Desktop
Add this to your Claude Desktop configuration:

```json
{
  "mcpServers": {
    "weather": {
      "command": "npx",
      "args": ["mcp-remote", "https://weather-mcp.genaijake.workers.dev/sse"]
    }
  }
}
```

#### Test with MCP Inspector
```bash
npx @modelcontextprotocol/inspector
# Then connect to: https://weather-mcp.genaijake.workers.dev/sse
```

---

## 🛠️ Available Tools

### 🌦️ `get-forecast`
Get detailed weather forecasts for any US location using coordinates.

**Parameters:**
- `latitude` (number): Latitude (-90 to 90)
- `longitude` (number): Longitude (-180 to 180)

**Example Request:**
```json
{
  "latitude": 37.7749,
  "longitude": -122.4194
}
```

**Example Response:**
```
Forecast for 37.7749, -122.4194:

Tonight:
Temperature: 52°F
Wind: 5 mph W
Partly cloudy with patchy fog after midnight

Tuesday:
Temperature: 68°F
Wind: 10 mph NW
Sunny with areas of morning fog
---
```

### 🚨 `get-alerts`
Retrieve active weather alerts and warnings for any US state.

**Parameters:**
- `state` (string): Two-letter state code (e.g., "CA", "TX", "FL")

**Example Request:**
```json
{
  "state": "CA"
}
```

**Example Response:**
```
Active alerts for CA:

Event: High Wind Warning
Area: San Francisco Bay Area
Severity: Moderate
Status: Actual
Headline: High Wind Warning until Tuesday 10:00 AM PST
---
```

---

## 💡 Usage Examples

### Weather Planning Assistant
```
User: "What's the weather like in Seattle this week?"
AI: Uses get-forecast tool with Seattle coordinates
Response: Detailed 7-day forecast with temperatures and conditions
```

### Emergency Weather Monitoring
```
User: "Are there any severe weather alerts in Florida?"
AI: Uses get-alerts tool with state code "FL"
Response: Current hurricane, tornado, or severe weather warnings
```

### Travel Weather Briefing
```
User: "I'm flying to Denver tomorrow, any weather concerns?"
AI: Combines forecast and alerts for comprehensive travel weather brief
```

---

## 🌍 Data Source & Coverage

Powered by the **[National Weather Service API](https://weather.gov/)** 🇺🇸
- ✅ **Official Government Data** - Trusted, accurate weather information
- ✅ **Real-time Updates** - Live alerts and current conditions
- ✅ **Comprehensive Coverage** - All US states and territories
- ❌ **US Only** - Due to NWS API limitations

---

## 🏗️ Local Development

```bash
# Clone and setup
git clone <your-repo-url>
cd weather-mcp
npm install

# Start development server
npm run dev
# Server available at: http://localhost:8787/sse

# Deploy to production
npm run deploy
```

---

## 🔒 Authentication & Security

This server implements **OAuth 2.0** for secure access:

1. **🔑 Client Registration** - Register your MCP client
2. **👤 User Authorization** - Users approve weather data access
3. **🎫 Token Exchange** - Secure token-based authentication
4. **🛡️ Scoped Permissions** - Granular access control

---

## 📊 Status & Monitoring

| Component | Status | Notes |
|-----------|--------|-------|
| 🌐 **Server** | ✅ Live | `weather-mcp.genaijake.workers.dev` |
| 🌦️ **Weather API** | ✅ Active | National Weather Service |
| 🔐 **OAuth** | ✅ Functional | User authentication working |
| 📡 **MCP Protocol** | ✅ Compatible | Latest MCP SDK |

---

<div align="center">

**⭐ Star this repo if you find it useful!**

Made with ❤️ and lots of ☕ for the AI community

</div>
