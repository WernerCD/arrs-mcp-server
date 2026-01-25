# arrs-mcp-server Roadmap

> MCP server for managing Sonarr, Radarr, Radarr4k, Plex, and Sabnzbd through Claude.

## Overview

| Phase | Name | Core Tools | Status |
|-------|------|------------|--------|
| 1 | Foundation + Sonarr | 6 core + 3 cross-service + 8 extended | Planned |
| 2 | Radarr | 6 core + 5 extended | Planned |
| 3 | Plex | 3 core + 5 extended | Planned |
| 4 | Sabnzbd | 7 core + 4 extended | Planned |
| 5 | Polish & Extended | Remaining extended tools, refinements | Future |

---

## Phase 1: Foundation + Sonarr

**Goal**: Working MCP server with Sonarr core tools and cross-service utilities.

### Deliverables

1. **Project Setup**
   - TypeScript project with pnpm
   - MCP SDK integration
   - Configuration loading (JSON file + env vars)
   - Error handling patterns

2. **Cross-Service Tools** (Core)
   - `downloads_status` - Unified download status across all services
   - `system_health` - Health check across all services
   - `media_help` - System overview + tool catalog for Claude

3. **TV Show Tools (Sonarr)**
   - API client for Sonarr v3 API
   - Semantic tools (user-facing):
     - `tv_search` - Search for series by name
     - `tv_add` - Add series (with monitor/quality/folder options)
     - `tv_list` - List all series (simple names + status)
     - `tv_episodes` - Episode status (missing/available/downloaded)
     - `tv_search_missing` - Trigger search for missing episodes
   - Service-specific tools (admin):
     - `sonarr_queue` - Queue with progress, ETA, errors, stuck imports
     - `sonarr_details` - Detailed info for one series
     - `sonarr_delete` - Remove series
     - `sonarr_profiles` - Available quality profiles
     - `sonarr_folders` - Available root folders (kids, tv)
     - `sonarr_stuck` - Items stuck importing
     - `sonarr_import` - Trigger manual import
     - `sonarr_blacklist` - Blacklist + re-search
     - `sonarr_calendar` - Upcoming episodes

4. **Documentation**
   - README with setup instructions
   - Config example file
   - Claude Desktop/Code configuration examples

### Verification Gate

- [ ] MCP server starts without errors
- [ ] Works with both Claude Desktop and Claude Code
- [ ] Can search, add, and list series
- [ ] Can check queue with progress/ETA/errors
- [ ] Can see and fix stuck imports
- [ ] Can blacklist bad releases and re-search
- [ ] Health check reports issues correctly

---

## Phase 2: Radarr

**Goal**: Full Radarr integration with Radarr4k support.

### Deliverables

1. **Movie Tools (Radarr)**
   - API client for Radarr v3 API (shared base with different configs)
   - Semantic tools (user-facing):
     - `movie_search` - Search for movies by name
     - `movie_add` - Add movie (with monitor/quality options)
     - `movie_list` - Browse collection
     - `movie_upgrade` - Re-search for better quality
     - `movie_delete` - Remove movie (cleanup space)
   - Service-specific tools (admin):
     - `radarr_queue` - Queue with progress, ETA, errors
     - `radarr_details` - Detailed info
     - `radarr_profiles` - Available profiles
     - `radarr_stuck` - Stuck items
     - `radarr_import` - Trigger import
     - `radarr_blacklist` - Blacklist + re-search

2. **Radarr4k Support**
   - `quality` parameter on ALL movie tools: `'hd'` (default) or `'4k'`
   - Default: `'hd'` (regular Radarr) - SAFE
   - Only `'4k'` when user explicitly says "4K", "4k", "UHD", "2160p"
   - Separate configuration for Radarr4k instance

### Verification Gate

- [ ] All 5 movie semantic tools work
- [ ] Regular Radarr is default (no accidental 4K)
- [ ] `quality: '4k'` routes to Radarr4k correctly
- [ ] Can search for quality upgrades
- [ ] Delete works safely
- [ ] Blacklist + re-search workflow works

---

## Phase 3: Plex

**Goal**: Plex library browsing, search, and watch status.

### Deliverables

1. **Library Tools (Plex)**
   - Plex API client with token auth
   - Semantic tools (user-facing):
     - `library_list` - List all libraries (Movies, TV, Kids, etc.)
     - `library_search` - Search across libraries ("Do I have...?")
     - `library_watched` - Watched/unwatched status
   - Service-specific tools (admin):
     - `plex_delete` - Delete from Plex (with care!)
     - `plex_unwatched` - Content unwatched for N days
     - `plex_watched_old` - Watched content older than N days
     - `plex_recent` - Recently added
     - `plex_refresh` - Trigger scan (rarely needed)

### Verification Gate

- [ ] Can list all Plex libraries
- [ ] Can search across libraries
- [ ] Watch status reporting works
- [ ] Delete works with confirmation
- [ ] Stale content queries work

---

## Phase 4: Sabnzbd

**Goal**: Full download queue management and troubleshooting.

### Deliverables

1. **Download Tools (Sabnzbd)**
   - Sabnzbd API client
   - Semantic tools (user-facing):
     - `downloads_queue` - Queue with full details
     - `downloads_history` - History with full details (status, size, source, errors)
     - `downloads_pause` - Pause all downloads
     - `downloads_resume` - Resume all downloads
     - `downloads_speed` - Set speed limit
   - Service-specific tools (admin):
     - `sabnzbd_delete` - Remove from queue
     - `sabnzbd_failed` - Failed downloads for investigation
     - `sabnzbd_retry` - Retry failed download
     - `sabnzbd_priority` - Change priority
     - `sabnzbd_categories` - List categories
     - `sabnzbd_pause_item` - Pause specific item
     - `sabnzbd_resume_item` - Resume specific item

### Verification Gate

- [ ] Can view queue with full details
- [ ] Can view history with errors/sources
- [ ] Can pause/resume all downloads
- [ ] Can adjust speed limits
- [ ] Can delete items from queue
- [ ] Can view and retry failed downloads

---

## Phase 5: Polish & Future Enhancements

**Goal**: Refinements and nice-to-have features.

### Potential Deliverables

1. **Library Cleanup Workflows**
   - Suggest deleting watched episodes after N days
   - Suggest deleting unwatched downloads after N days
   - Cross-reference with "keep list" (personal/family favorites)

2. **Troubleshooting Enhancements**
   - Deeper diagnostics for common issues
   - Guided troubleshooting workflows

3. **Future Integrations**
   - Overseerr (request management)
   - Streaming service availability check (if feasible)
   - Better curation suggestions (certified fresh, Oscar lists)

4. **Bulk Operations**
   - Add multiple shows/movies at once
   - Bulk delete operations

---

## Implementation Notes

### Per-Phase Pattern

Each phase follows this pattern:

1. Create service directory: `src/services/{service}/`
2. Implement API client: `client.ts`
3. Define types: `types.ts`
4. Register MCP tools: `tools.ts`
5. Export from index: `index.ts`
6. Test manually with Claude
7. Update README

### Configuration

Services are configured in `config.json` or environment variables:

```json
{
  "sonarr": { "url": "...", "apiKey": "..." },
  "radarr": { "url": "...", "apiKey": "..." },
  "radarr4k": { "url": "...", "apiKey": "..." },
  "plex": { "url": "...", "token": "..." },
  "sabnzbd": { "url": "...", "apiKey": "..." }
}
```

### Success Criteria

The project is complete when:

1. All core tools for all 4 services work
2. Natural language requests are handled smoothly
3. Error messages are helpful
4. Configuration is straightforward
5. Works with both Claude Desktop and Claude Code
6. Top workflows work seamlessly:
   - "Add Breaking Bad" → searches, confirms, adds, reports search started
   - "What's downloading?" → unified view with issues highlighted
   - "Something is stuck" → health check + fix workflow
   - "Get Inception in 4K" → correctly routes to Radarr4k
