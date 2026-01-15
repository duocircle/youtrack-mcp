# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Essential Development Commands

```bash
# Build and compilation
npm run build              # Compile TypeScript to dist/
npm run watch             # Watch mode for development

# Testing
npm test                  # Run main test suite (test queries)
npm run test:jest         # Jest-based tests
npm run test:enterprise   # Test OAuth2 and notification features

# Development
npm run dev               # Development mode with tsx
npm start                 # Run compiled server

# Code quality
npm run lint              # ESLint TypeScript files
npm run format            # Prettier formatting
```

## High-Level Architecture

This is a YouTrack MCP (Model Context Protocol) server with a modular, domain-driven architecture:

### Core Architecture Pattern
- **Base Layer**: `BaseAPIClient` in `src/api/base/` provides HTTP client, caching, error handling, and MCP response formatting
- **Domain Layer**: Domain-specific API clients in `src/api/domains/` (issues, projects, agile boards, etc.)
- **Client Factory**: `ClientFactory` creates configured `YouTrackClient` instances with all domain clients
- **12 Unified Tools**: Consolidated from 71+ individual tools to 12 powerful, multi-purpose tools

### Key Architectural Components

1. **Authentication System** (`src/auth/`)
   - Token-based authentication (permanent tokens)
   - OAuth2 with PKCE for browser-based auth
   - Automatic token refresh and secure storage

2. **Notification System** (`src/notifications/`)
   - WebSocket-based real-time notifications
   - Polling fallback for compatibility
   - Subscription management with filters

3. **Error Prevention Architecture**
   - Multi-layered validation (parameter formats, resource existence)
   - Backward compatibility for legacy tool names
   - Smart error suggestions for unknown tools
   - Custom `ValidationError` class with MCP error conversion

### Important Patterns

- **ES Modules**: Always use `.js` extensions in imports (even for TypeScript files)
- **TypeScript Strict Mode**: Full type safety enforced
- **Factory Pattern**: Use `ClientFactory` for creating clients
- **Domain Segregation**: Each domain has its own API client
- **MCP Tool Structure**: Each tool returns structured MCP responses

### Configuration Requirements

Environment variables:
- `YOUTRACK_URL` (required): YouTrack instance URL
- `YOUTRACK_TOKEN` (required): Permanent API token
- `PROJECT_ID` (optional): Default project ID
- `LOG_LEVEL` (optional): Logging level (default: info)

### Testing Approach

- **Integration Tests**: Use `npm test` to run query-based tests against actual YouTrack API
- **Unit Tests**: Use `npm run test:jest` for isolated component testing
- **Enterprise Features**: Use `npm run test:enterprise` for OAuth2 and notification testing

### Critical Implementation Notes

1. **Validation Formats**:
   - Project IDs: `PROJECT`, `TEST-1`, or `0-18` format
   - Issue IDs: `PROJECT-123` or `3-511` format
   - Dates: `YYYY-MM-DD` format with actual date verification
   - Durations: `2h`, `30m`, `1d` time formats

2. **Tool Consolidation**: The 12 unified tools (`projects`, `issues`, `query`, `comments`, `agile_boards`, `knowledge_base`, `analytics`, `admin`, `time_tracking`, `auth`, `notifications`, `subscriptions`) replace 71+ individual tools with powerful multi-purpose functionality

3. **Error Handling**: Always use the error handler from `src/api/base/error-handler.ts` for consistent MCP error responses

4. **Caching**: Intelligent caching is built into `BaseAPIClient` - respect cache settings and TTL configurations