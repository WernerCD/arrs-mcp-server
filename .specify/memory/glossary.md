# Glossary

> **Agents**: Reference this for domain terminology.

**Last Updated**: 2026-01-24

## Services

| Term | Definition |
|------|------------|
| **Sonarr** | TV series management and download automation |
| **Radarr** | Movie management and download automation |
| **Radarr4k** | Separate Radarr instance for 4K content only |
| **Plex** | Media server for streaming content |
| **Sabnzbd** | Usenet download client |
| **Overseerr** | Media request management and user requests |
| **TMDB** | The Movie Database - primary source for movie metadata |
| **\*arr apps** | Collective term for Sonarr, Radarr, Lidarr, etc. |

## MCP (Model Context Protocol)

| Term | Definition |
|------|------------|
| **MCP** | Model Context Protocol - standard for AI tool integration |
| **MCP Server** | Service that exposes tools to Claude |
| **MCP Client** | Claude Desktop, Claude Code, or other consumers |
| **Tool** | A function Claude can invoke via MCP |
| **stdio transport** | Communication via stdin/stdout JSON-RPC |

## Media Management

| Term | Definition |
|------|------------|
| **TVDB** | TheTVDB - database of TV series metadata |
| **TMDB API** | The Movie Database API - used for collection and recommendation data |
| **Quality Profile** | Settings defining acceptable video quality |
| **Root Folder** | Base directory for storing media |
| **Queue** | Current download queue |
| **Calendar** | Upcoming episodes/releases |
| **Indexer** | Source for finding download links |

## Infrastructure

| Term | Definition |
|------|------------|
| **QNAP** | NAS (Network Attached Storage) device |
| **Container Station** | QNAP's container management (Docker-like) |
| **Port Forwarding** | Exposing internal services to internet |

## This Project

| Term | Definition |
|------|------------|
| **Semantic Tools** | User-facing tools with intuitive names (`tv_search`, `movie_add`) |
| **Service-Specific Tools** | Admin/troubleshooting tools with service prefix (`sonarr_queue`) |
| **quality parameter** | `'hd'` (default) or `'4k'` to route movie requests to Radarr or Radarr4k |
| **Service Module** | Self-contained directory for each service under `src/services/` |
| **Collection** | TMDB concept - a franchise or series of related movies (e.g., Star Wars Collection) |
| **Collection Completion** | Finding movies you're missing from a franchise you've started collecting |
