# arrs-mcp-server Constitution

> **Agents**: Reference this document for architectural principles and non-negotiable project requirements.
> This is the authoritative source for project governance and development philosophy.

**Last Updated**: 2026-01-24

## Product Vision

An MCP (Model Context Protocol) server providing Claude with full control over a personal media management stack: Sonarr, Radarr, Radarr4k, Plex, and Sabnzbd. The server enables natural language interaction with these services through a unified conversational interface.

**Target Audience**: Developers using Claude (Desktop/Code) + Home server administrators

**Primary Experience**: Natural language commands to manage media - "Add Breaking Bad to Sonarr", "What's downloading?", "Find sci-fi movies in Plex"

## Core Principles

### I. Natural Language First

All tools should be designed for intuitive natural language interaction. Tool names, parameters, and responses must be clear enough that Claude can use them effectively without special prompting.

- **Semantic naming**: Tools use intuitive names Claude understands without domain knowledge (`tv_search`, `movie_add`, `library_search`, `downloads_status`)
- **Two categories**: Semantic tools for common operations, service-specific tools (`sonarr_*`, `radarr_*`) for admin/troubleshooting
- Parameters use plain English names, not API internal names
- Responses are formatted for Claude to interpret and relay to users
- Error messages explain what went wrong in user-friendly terms

**Rationale**: The entire purpose is to replace web UI interactions with conversation. Claude doesn't know what "Sonarr" is, but understands "tv_search".

### II. Safety by Default

Destructive or expensive operations require explicit intent. The system should prevent accidental damage to the media library.

- Radarr4k (4K movies) is ONLY used when explicitly requested with `quality: '4k'`
- Default quality is always `'hd'` (regular Radarr)
- Delete operations require explicit confirmation parameters
- No auto-pilot mode for bulk operations

**Rationale**: Accidentally downloading 4K content or deleting shows would be frustrating and potentially expensive (bandwidth/storage).

### III. Plugin Architecture

Each service (Sonarr, Radarr, Plex, Sabnzbd) is a self-contained module with its own tools, API client, and types. Adding a new service should not require changes to existing services.

- One directory per service under `src/services/`
- Each service exports its tools via a standard interface
- Shared utilities (HTTP client, error handling) are separate from service logic
- Configuration is service-specific but follows common schema

**Rationale**: Overseerr and other services may be added later. Clean separation enables this without refactoring.

### IV. Stateless Operation

The MCP server maintains no persistent state of its own. All state lives in the *arr applications and Plex/Sabnzbd.

- No database or file-based storage
- No caching of API responses
- Each request is independent
- Configuration (API keys, URLs) is read at startup

**Rationale**: Simplifies architecture, eliminates sync issues, reduces failure modes. The *arr apps are the source of truth.

### V. Two-Tier Tool Design

Tools are organized into Core (frequently used, prominent) and Extended (advanced, discoverable) tiers.

- Core tools handle 80% of use cases
- Extended tools are available but not prominently advertised
- A catalog tool can list available extended operations
- Both tiers use the same implementation patterns

**Rationale**: Keeps the primary interface clean while preserving full capability.

## Technology Stack

**Core Technologies**:
- TypeScript 5.x with strict mode
- Node.js 20+ LTS
- @modelcontextprotocol/sdk (MCP TypeScript SDK)
- zod for input validation and schema generation
- Native fetch for HTTP requests

**Deviation Process**: Any deviation from approved technologies MUST be documented in the feature's `plan.md` with clear justification.

> See [`tech-stack.md`](./tech-stack.md) for complete list of approved technologies.

## Development Workflow

### Code Review Requirements

- Solo project - self-review before merge
- PRs MUST include description of changes and testing performed
- Squash merge for clean history

### Quality Gates

Before merge, all PRs MUST pass:

1. TypeScript compilation with no errors (`tsc --noEmit`)
2. ESLint with no errors
3. Code formatting check (Prettier)
4. No secrets detected in code
5. Manual testing with Claude Desktop or Code

### Commit Standards

- Conventional Commits format: `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`
- Commit messages describe "why" not just "what"
- Each commit represents a logical unit of change

## Governance

### Authority

This Constitution supersedes all other development practices and guidelines.
When conflicts arise, Constitution principles take precedence.

### Amendment Process

1. Propose amendment via pull request to this file
2. Document rationale for change in PR description
3. Version increment follows semantic versioning
4. All dependent artifacts updated as part of amendment PR

### Runtime Guidance

For day-to-day development guidance, code style details, and project-specific conventions,
refer to the generated `CLAUDE.md` file at project root.

**Version**: 1.0.0 | **Ratified**: 2026-01-24 | **Last Amended**: 2026-01-24
