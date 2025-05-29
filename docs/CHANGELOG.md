# Changelog

All notable changes to this weather MCP server project will be documented in this file.

## [1.0.0] - 2025-01-26

### Added
- **Weather Forecasts**: Implemented `get-forecast` tool that retrieves detailed weather forecasts for US locations using latitude/longitude coordinates
- **Weather Alerts**: Implemented `get-alerts` tool that retrieves active weather alerts for US states using two-letter state codes
- **National Weather Service Integration**: Added integration with the NWS API for official weather data
- **Comprehensive Documentation**: Updated README and static content to reflect weather functionality
- **Input Validation**: Added Zod schema validation for tool parameters (latitude/longitude bounds, state code format)
- **Error Handling**: Implemented robust error handling for API failures and invalid locations
- **User-Agent Headers**: Added proper User-Agent identification for NWS API requests

### Changed
- **Server Identity**: Changed from generic "Demo" server to "Weather" server (v1.0.0)
- **Application Branding**: Updated all UI elements from "MCP Remote Auth Demo" to "Weather MCP Server"
- **OAuth Scopes**: Updated OAuth permission descriptions to reflect weather-specific functionality
- **Tool Description**: Replaced simple "add" math tool with comprehensive weather tools
- **API Endpoints**: Maintained OAuth endpoints but updated tool functionality

### Technical Details
- **API Base**: `https://api.weather.gov` (National Weather Service)
- **Supported Locations**: US territories only (NWS API limitation)
- **Data Format**: GeoJSON responses from NWS API
- **Authentication**: OAuth 2.0 with secure user authorization
- **Deployment**: Cloudflare Workers with auto-deployment via GitHub

### Migration Notes
- This version replaces the demo math tools with production-ready weather tools
- All existing OAuth and authentication functionality remains unchanged
- The server URL structure (`/sse`, `/authorize`, `/token`, `/register`) remains the same
- Backward compatibility: This is a breaking change for clients expecting math tools

### Testing
- ✅ Weather API endpoints validated
- ✅ Forecast retrieval tested (San Francisco coordinates)
- ✅ Alert system tested (California state alerts)
- ✅ Error handling verified for invalid coordinates and states
- ✅ OAuth flow maintained and functional 