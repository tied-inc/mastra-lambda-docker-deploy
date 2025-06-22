# Mastra Lambda Docker Deploy

A weather-focused AI application built with the Mastra framework, designed for deployment on AWS Lambda using Docker containers.

## Key Feature: AWS Lambda Web Adapter Integration

This repository demonstrates how to integrate AWS Lambda Web Adapter into a Docker container for serverless deployment. The critical implementation is in the Dockerfile at line 10:

```dockerfile
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:0.9.0 /lambda-adapter /opt/extensions/lambda-adapter
```

This single line enables any HTTP server (running on port 8080) to work seamlessly with AWS Lambda's event-driven model. The adapter handles the translation between Lambda events and standard HTTP requests, eliminating the need to modify your application code for serverless deployment.

## Overview

This project provides weather-related AI services through conversational agents and automated workflows. It leverages the Open-Meteo API for weather data and OpenAI for intelligent responses, packaged in a production-ready Docker container optimized for AWS Lambda.

## Features

- **Weather Agent**: Conversational AI that provides location-based weather information
- **Weather Tool**: Direct weather data retrieval with geocoding support
- **Weather Workflow**: Multi-step process for weather analysis and activity planning
- **Lambda-Ready**: Containerized deployment with AWS Lambda Web Adapter
- **In-Memory Storage**: LibSQL database for fast, serverless operations

## Quick Start

### Prerequisites

- Node.js 18+
- Docker
- OpenAI API key

### Installation

```bash
npm install
```

### Environment Variables

Create a `.env` file:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

### Development

```bash
# Install dependencies
npm install

# Build the application
npx mastra build

# Run locally with Docker
docker build -t mastra-lambda .
docker run -p 8080:8080 -e OPENAI_API_KEY=your_key mastra-lambda
```

### Code Quality

```bash
# Check formatting and linting
npx biome check

# Auto-fix issues
npx biome check --write
```

## Architecture

### Core Components

- **Entry Point** (`src/mastra/index.ts`): Main Mastra configuration
- **Weather Agent** (`src/mastra/agents/weather-agent.ts`): GPT-4o-mini powered conversational interface
- **Weather Tool** (`src/mastra/tools/weather-tool.ts`): Open-Meteo API integration
- **Weather Workflow** (`src/mastra/workflows/weather-workflow.ts`): Activity planning pipeline

### Deployment

The application runs on port 8080 and uses:
- LibSQL in-memory storage for serverless compatibility
- Pino logger with info level
- AWS Lambda Web Adapter for HTTP request handling
- Multi-stage Docker build for optimized container size

## API Endpoints

Once deployed, the application exposes Mastra's standard endpoints for:
- Agent conversations
- Workflow execution
- Tool invocations

## Deployment to AWS Lambda

1. Build the Docker image:
   ```bash
   docker build -t mastra-lambda .
   ```

2. Push to Amazon ECR:
   ```bash
   aws ecr get-login-password --region your-region | docker login --username AWS --password-stdin your-account.dkr.ecr.your-region.amazonaws.com
   docker tag mastra-lambda:latest your-account.dkr.ecr.your-region.amazonaws.com/mastra-lambda:latest
   docker push your-account.dkr.ecr.your-region.amazonaws.com/mastra-lambda:latest
   ```

3. Create Lambda function using the container image

4. Set environment variables in Lambda console:
   - `OPENAI_API_KEY`: Your OpenAI API key

## Development

The project uses:
- **Biome** for formatting (2 spaces, double quotes) and linting
- **TypeScript** for type safety
- **Zod** for schema validation
- **CommonJS** module format

## External APIs

- **Open-Meteo**: Geocoding and weather data (no API key required)
- **OpenAI**: GPT-4o-mini for AI capabilities (API key required)

## Security

- Non-root user execution in Docker container
- Environment variable based configuration
- No hardcoded secrets or API keys
