# Project Context

## Identity
- **Name**: arrs-mcp-server
- **Type**: MCP Server
- **Stage**: Greenfield
- **Criticality**: TBD

## Target Services
- Sonarr (TV show management)
- Radarr (Movie management)
- Radarr4k (4K movie management - separate instance)
- Plex (Media server)
- Sabnzbd (Usenet download client)

## Infrastructure
- Host: QNAP NAS with Container Station
- Access: Port-forwarded URLs for each service
- Environment: Containerized services

## Team
- **Size**: 1 (Solo)
- **Composition**: Single developer/maintainer

## Criticality
- **Level**: Home Production
- **Expectation**: Reliable but not mission-critical

## Tech Stack (Decided)
- Language: TypeScript
- Runtime: Node.js
- Pattern: MCP SDK (TypeScript)

## Related Projects
- clawdbot (Node.js)
- ai-assist MCP (TypeScript)

## Constraints
- Must integrate with 5 different service APIs
- Services accessible via port-forwarded URLs

## Existing Materials
- Empty git repository
