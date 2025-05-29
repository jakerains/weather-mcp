# Weather MCP Server - Project Map

## Overview
A remote Model Context Protocol (MCP) server providing weather information tools via Cloudflare Workers with OAuth authentication.

## Architecture

### Core Components

#### MCP Server (`src/index.ts`)
- **MyMCP Class**: Extends McpAgent to provide weather functionality
- **Server Identity**: "Weather" v1.0.0
- **Tools Provided**:
  - `get-forecast`: Weather forecasts by lat/lng coordinates
  - `get-alerts`: Weather alerts by US state code

#### OAuth Provider Setup
- **Route**: `/sse` - MCP Server-Sent Events endpoint
- **Authentication**: OAuth 2.0 with user authorization flow
- **Endpoints**:
  - `/authorize` - User authorization page
  - `/token` - Token exchange endpoint
  - `/register` - Client registration endpoint

#### Web Application (`src/app.ts`)
- **Framework**: Hono for Cloudflare Workers
- **Homepage**: Renders weather server documentation
- **Auth Pages**: Login/approval screens for OAuth flow

#### UI Layer (`src/utils.ts`)
- **Layout System**: Responsive design with Tailwind CSS
- **Content Rendering**: Markdown to HTML conversion for documentation
- **OAuth Screens**: Login and approval interfaces

### External Integrations

#### National Weather Service API
- **Base URL**: `https://api.weather.gov`
- **Authentication**: User-Agent header identification
- **Data Format**: GeoJSON responses
- **Coverage**: US territories only

#### Weather Tools Implementation

##### get-forecast Tool
- **Input**: `latitude` (number), `longitude` (number)
- **Process**:
  1. Query NWS points API for grid coordinates
  2. Retrieve forecast URL from grid point data
  3. Fetch forecast periods from forecast endpoint
  4. Format and return forecast data
- **Output**: Formatted text with weather periods

##### get-alerts Tool
- **Input**: `state` (2-letter state code)
- **Process**:
  1. Query NWS alerts API for specified state
  2. Parse alert features from response
  3. Format alert information
- **Output**: Formatted text with active alerts

### Deployment Configuration

#### Cloudflare Workers
- **Platform**: Serverless edge computing
- **Auto-deployment**: GitHub integration for continuous deployment
- **Environment**: Production URL at `weather-mcp.genaijake.workers.dev`

#### OAuth Configuration
- **KV Storage**: User sessions and OAuth state
- **Scopes**: 
  - `read_profile` - Basic profile information
  - `read_data` - Access weather data
  - `write_data` - Store weather preferences

### File Structure

```
weather-mcp/
├── src/
│   ├── index.ts          # Main MCP server with weather tools
│   ├── app.ts            # Web application and OAuth routes
│   └── utils.ts          # UI components and utilities
├── static/
│   ├── README.md         # Homepage content (weather documentation)
│   └── img/              # Static assets
├── docs/
│   ├── CHANGELOG.md      # Version history and changes
│   ├── project-map.md    # This file - project structure
│   └── weathermcp.md     # Original weather server reference
├── package.json          # Dependencies and scripts
├── tsconfig.json         # TypeScript configuration
├── wrangler.jsonc        # Cloudflare Workers configuration
└── README.md             # Main project documentation
```

### Development Workflow

#### Local Development
- **Command**: `npm run dev`
- **URL**: `http://localhost:8787`
- **MCP Endpoint**: `http://localhost:8787/sse`

#### Testing
- **Weather APIs**: Validated against live NWS endpoints
- **OAuth Flow**: Functional authentication and authorization
- **MCP Inspector**: Compatible with standard MCP clients

#### Deployment
- **Auto-deploy**: Push to main branch triggers deployment
- **Manual deploy**: `npm run deploy`
- **Environment**: Production Cloudflare Workers

### Integration Points

#### MCP Clients
- **Claude Desktop**: Configure with mcp-remote proxy
- **MCP Inspector**: Direct SSE connection for testing
- **Custom Clients**: Standard MCP protocol compatibility

#### Authentication Flow
1. Client connects to `/sse` endpoint
2. OAuth redirect to `/authorize`
3. User login and scope approval
4. Token exchange via `/token`
5. Authenticated MCP session established

### Error Handling

#### Weather API Errors
- Invalid coordinates (non-US locations)
- NWS API downtime or rate limiting
- Network connectivity issues
- Malformed state codes

#### OAuth Errors
- Invalid client credentials
- User authorization rejection
- Token exchange failures
- Session timeout handling

### Security Considerations

#### API Access
- User-Agent identification for NWS API
- Rate limiting through Cloudflare
- OAuth scope-based permissions

#### Data Privacy
- No persistent weather data storage
- OAuth tokens in secure KV storage
- HTTPS-only communication

This project successfully transforms a demo MCP server into a production-ready weather information service while maintaining the robust OAuth authentication framework. 