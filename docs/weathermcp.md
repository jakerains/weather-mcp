Directory structure:
└── cskiro-mcp-weather-server/
    ├── README.md
    ├── package.json
    ├── tsconfig.json
    ├── docs/
    │   └── architecture/
    └── src/
        └── index.ts


Files Content:

================================================
FILE: README.md
================================================
# MCP Weather Server

A weather server application built with the Model Context Protocol (MCP) that provides access to real-time weather data and alerts.

## Overview

MCP Weather Server is a TypeScript application that serves as a backend for weather-related applications using the MCP SDK. It provides tools for accessing current weather conditions, forecasts, and weather alerts through the National Weather Service (NWS) API.

## Features

- **US Weather Forecasts**: Get detailed forecast data for any US location via latitude/longitude
- **Weather Alerts**: Retrieve active weather alerts for any US state
- **MCP Integration**: Built as an MCP server for seamless AI assistant integration
- **Command-line Interface**: Easy to install and run as a CLI tool

## Architecture

The application is built using the Model Context Protocol (MCP) framework:

1. **MCP Server Layer**: Handles communication with MCP clients
2. **Weather Tools**: Implements forecast and alert functionality
3. **External API Integration**: Connects to the National Weather Service (NWS) API

### Component Structure

The server includes two primary weather tools:

- **get-forecast**: Retrieves detailed weather forecasts for a location specified by latitude and longitude
- **get-alerts**: Obtains active weather alerts for a specified US state

These tools communicate with the National Weather Service API to provide reliable weather data.

## Tech Stack

- Node.js
- TypeScript
- MCP SDK (@modelcontextprotocol/sdk)
- National Weather Service API
- Zod (for input validation)

## Getting Started

### Prerequisites

- Node.js (v18 or higher)
- npm or yarn

### Installation

```bash
# Clone the repository
git clone https://github.com/cskiro/MCP-Weather-Server.git

# Navigate to the project directory
cd MCP-Weather-Server

# Install dependencies
npm install

# Build the project
npm run build

# Make the CLI executable
chmod +x build/index.js

# Install globally (optional)
npm install -g .
```

### Usage

You can use the server in two ways:

1. **Direct invocation**:
```bash
weather
```

2. **Through an MCP-compatible client/assistant**:
Configure your MCP client to use the weather tool for forecasts and weather alerts.

## API Usage

### Get Weather Forecast

```typescript
// Example: Get forecast for San Francisco
{
  latitude: 37.7749,
  longitude: -122.4194
}
```

### Get Weather Alerts

```typescript
// Example: Get alerts for California
{
  state: "CA"
}
```

## Data Sources

This server uses the [National Weather Service API](https://weather.gov/), which provides official weather data for the United States. Due to the limitations of this data source, the application currently only supports US-based locations.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the ISC License - see the LICENSE file for details.



================================================
FILE: package.json
================================================
{
  "name": "mcp-weather-server",
  "version": "1.0.0",
  "main": "index.js",
  "type": "module",
  "bin": {
    "weather": "./build/index.js"
  },
  "scripts": {
    "build": "tsc && node -e \"require('fs').chmodSync('build/index.js', '755')\""
  },
  "files": [
    "build"
  ],
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "@types/node": "^22.13.9",
    "typescript": "^5.8.2"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.6.1",
    "zod": "^3.24.2"
  }
}



================================================
FILE: tsconfig.json
================================================
{
    "compilerOptions": {
      "target": "ES2022",
      "module": "Node16",
      "moduleResolution": "Node16",
      "outDir": "./build",
      "rootDir": "./src",
      "strict": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "forceConsistentCasingInFileNames": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules"]
  }



================================================
FILE: src/index.ts
================================================
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const NWS_API_BASE = "https://api.weather.gov";
const USER_AGENT = "weather-app/1.0";

// Create server instance
const server = new McpServer({
  name: "weather",
  version: "1.0.0",
});

// Helper function for making NWS API requests
async function makeNWSRequest<T>(url: string): Promise<T | null> {
    const headers = {
      "User-Agent": USER_AGENT,
      Accept: "application/geo+json",
    };
  
    try {
      const response = await fetch(url, { headers });
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      return (await response.json()) as T;
    } catch (error) {
      console.error("Error making NWS request:", error);
      return null;
    }
  }
  
  interface AlertFeature {
    properties: {
      event?: string;
      areaDesc?: string;
      severity?: string;
      status?: string;
      headline?: string;
    };
  }
  
  // Format alert data
  function formatAlert(feature: AlertFeature): string {
    const props = feature.properties;
    return [
      `Event: ${props.event || "Unknown"}`,
      `Area: ${props.areaDesc || "Unknown"}`,
      `Severity: ${props.severity || "Unknown"}`,
      `Status: ${props.status || "Unknown"}`,
      `Headline: ${props.headline || "No headline"}`,
      "---",
    ].join("\n");
  }
  
  interface ForecastPeriod {
    name?: string;
    temperature?: number;
    temperatureUnit?: string;
    windSpeed?: string;
    windDirection?: string;
    shortForecast?: string;
  }
  
  interface AlertsResponse {
    features: AlertFeature[];
  }
  
  interface PointsResponse {
    properties: {
      forecast?: string;
    };
  }
  
  interface ForecastResponse {
    properties: {
      periods: ForecastPeriod[];
    };
  }

  // Register weather tools
server.tool(
    "get-alerts",
    "Get weather alerts for a state",
    {
      state: z.string().length(2).describe("Two-letter state code (e.g. CA, NY)"),
    },
    async ({ state }) => {
      const stateCode = state.toUpperCase();
      const alertsUrl = `${NWS_API_BASE}/alerts?area=${stateCode}`;
      const alertsData = await makeNWSRequest<AlertsResponse>(alertsUrl);
  
      if (!alertsData) {
        return {
          content: [
            {
              type: "text",
              text: "Failed to retrieve alerts data",
            },
          ],
        };
      }
  
      const features = alertsData.features || [];
      if (features.length === 0) {
        return {
          content: [
            {
              type: "text",
              text: `No active alerts for ${stateCode}`,
            },
          ],
        };
      }
  
      const formattedAlerts = features.map(formatAlert);
      const alertsText = `Active alerts for ${stateCode}:\n\n${formattedAlerts.join("\n")}`;
  
      return {
        content: [
          {
            type: "text",
            text: alertsText,
          },
        ],
      };
    },
  );
  
  server.tool(
    "get-forecast",
    "Get weather forecast for a location",
    {
      latitude: z.number().min(-90).max(90).describe("Latitude of the location"),
      longitude: z.number().min(-180).max(180).describe("Longitude of the location"),
    },
    async ({ latitude, longitude }) => {
      // Get grid point data
      const pointsUrl = `${NWS_API_BASE}/points/${latitude.toFixed(4)},${longitude.toFixed(4)}`;
      const pointsData = await makeNWSRequest<PointsResponse>(pointsUrl);
  
      if (!pointsData) {
        return {
          content: [
            {
              type: "text",
              text: `Failed to retrieve grid point data for coordinates: ${latitude}, ${longitude}. This location may not be supported by the NWS API (only US locations are supported).`,
            },
          ],
        };
      }
  
      const forecastUrl = pointsData.properties?.forecast;
      if (!forecastUrl) {
        return {
          content: [
            {
              type: "text",
              text: "Failed to get forecast URL from grid point data",
            },
          ],
        };
      }
  
      // Get forecast data
      const forecastData = await makeNWSRequest<ForecastResponse>(forecastUrl);
      if (!forecastData) {
        return {
          content: [
            {
              type: "text",
              text: "Failed to retrieve forecast data",
            },
          ],
        };
      }
  
      const periods = forecastData.properties?.periods || [];
      if (periods.length === 0) {
        return {
          content: [
            {
              type: "text",
              text: "No forecast periods available",
            },
          ],
        };
      }
  
      // Format forecast periods
      const formattedForecast = periods.map((period: ForecastPeriod) =>
        [
          `${period.name || "Unknown"}:`,
          `Temperature: ${period.temperature || "Unknown"}°${period.temperatureUnit || "F"}`,
          `Wind: ${period.windSpeed || "Unknown"} ${period.windDirection || ""}`,
          `${period.shortForecast || "No forecast available"}`,
          "---",
        ].join("\n"),
      );
  
      const forecastText = `Forecast for ${latitude}, ${longitude}:\n\n${formattedForecast.join("\n")}`;
  
      return {
        content: [
          {
            type: "text",
            text: forecastText,
          },
        ],
      };
    },
  );

  async function main() {
    const transport = new StdioServerTransport();
    await server.connect(transport);
    console.error("Weather MCP Server running on stdio");
  }
  
  main().catch((error) => {
    console.error("Fatal error in main():", error);
    process.exit(1);
  });

