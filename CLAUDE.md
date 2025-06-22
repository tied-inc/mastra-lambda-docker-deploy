# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Commands

### Development
- `npm install` - Install dependencies
- `npx biome check` - Run linter and formatter checks
- `npx biome check --write` - Apply linter and formatter fixes
- `npx mastra build` - Build the Mastra application (outputs to `.mastra/output/`)

### Docker
- `docker build -t mastra-lambda .` - Build Docker image for AWS Lambda deployment
- `docker run -p 8080:8080 mastra-lambda` - Run container locally

## Project Architecture

This is a Mastra framework application designed for AWS Lambda deployment using Docker containers. The application provides weather-related AI services through agents and workflows.

### Core Structure
- **Entry Point**: `src/mastra/index.ts` - Main Mastra configuration with server, storage, and logging
- **Agents**: AI-powered conversational interfaces in `src/mastra/agents/`
- **Tools**: Reusable functions for external API calls in `src/mastra/tools/`
- **Workflows**: Multi-step processes in `src/mastra/workflows/`

### Key Components

**Mastra Instance** (`src/mastra/index.ts`):
- Runs on port 8080 (configured for AWS Lambda Adapter)
- Uses LibSQL in-memory storage (change to `file:../mastra.db` for persistence)
- Pino logger with info level
- Registers all agents and workflows

**Weather Agent** (`src/mastra/agents/weather-agent.ts`):
- Uses OpenAI GPT-4o-mini model
- Memory persistence to file storage
- Integrated with weather tool for location-based queries

**Weather Tool** (`src/mastra/tools/weather-tool.ts`):
- Fetches data from Open-Meteo APIs (geocoding + weather)
- Structured input/output with Zod schemas
- Weather code mapping for human-readable conditions

**Weather Workflow** (`src/mastra/workflows/weather-workflow.ts`):
- Two-step process: fetch weather → plan activities
- Includes a separate agent for activity recommendations
- Formatted output for travel/activity planning

### Deployment Architecture
- **Docker**: Multi-stage build with Node.js 22 Alpine
- **Lambda Adapter**: Uses AWS Lambda Web Adapter for HTTP requests
- **Build Process**: `npx mastra build` creates `.mastra/output/index.mjs`
- **Runtime**: Non-root user (mastra:nodejs) for security

### Configuration Files
- `biome.json` - Code formatting (2 spaces, double quotes) and linting
- `package.json` - CommonJS module type, minimal scripts
- `Dockerfile` - Production-ready Lambda container with security hardening

### External Dependencies
- Open-Meteo API for geocoding and weather data (no API key required)
- OpenAI for LLM capabilities (requires OPENAI_API_KEY environment variable)