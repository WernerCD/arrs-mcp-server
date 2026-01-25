# Phase 0050: Polish & Extended Features

**Status**: Not Started
**Branch**: `0050-polish-extended`
**Estimated Scope**: Medium (refinement and edge cases)

---

## Goals

1. Implement all Extended tier tools from api-standards.md
2. Add unified cleanup workflows across services
3. Polish error messages and edge case handling
4. Add comprehensive media_help with tool catalog
5. Performance optimization for large libraries
6. Documentation updates and final testing

---

## Scope

### In Scope

- Extended tools not implemented in previous phases
- Cross-service cleanup workflows
- Enhanced error messages
- Large library pagination/limits
- README polish and examples
- End-to-end workflow testing

### Out of Scope

- New services (Overseerr, Tautulli, etc.)
- Web UI
- Automated testing infrastructure
- CI/CD pipeline

---

## Deliverables

### 1. Extended Tools

**Sonarr Extended:**
- `sonarr_rename` - Rename episode files
- `sonarr_refresh` - Refresh series metadata
- `sonarr_upcoming` - More detailed upcoming view

**Radarr Extended:**
- `radarr_rename` - Rename movie files
- `radarr_refresh` - Refresh movie metadata
- `radarr_discover` - Discovery/recommendations

**Plex Extended:**
- `plex_collections` - List/manage collections
- `plex_duplicates` - Find duplicate files
- `plex_optimize` - Optimize database

**Sabnzbd Extended:**
- `sabnzbd_quota` - Quota status
- `sabnzbd_warnings` - System warnings

### 2. Unified Workflows

**Cleanup Assistant:**

```typescript
// Comprehensive cleanup analysis
cleanup_analysis()

// Response:
// Cleanup Analysis
//
// Unwatched Movies (365+ days): 15 items, 156.2 GB
// Watched Movies (180+ days): 8 items, 89.3 GB
// Ended Series (no recent episodes): 5 series, 234.5 GB
// Duplicate Files: 3 items, 45.2 GB
// Failed Downloads: 2 items (retry or blacklist)
//
// Total potential savings: 525.2 GB
//
// Use specific tools to investigate each category.
```

**Health Dashboard:**

```typescript
// Enhanced system_health
system_health({ verbose: true })

// Response:
// System Health: WARNING
//
// Sonarr: OK
//   Version: 4.0.13.2932
//   Shows: 160 | Episodes: 12,345
//   Queue: Empty | Stuck: 0
//
// Radarr: OK
//   Version: 5.18.4.9674
//   Movies: 450 | Downloaded: 423
//   Queue: 2 items | Stuck: 0
//
// Radarr4K: OK
//   Version: 5.18.4.9674
//   Movies: 85 | Downloaded: 82
//   Queue: 1 item | Stuck: 0
//
// Plex: OK
//   Libraries: 4
//   Movies: 508 | TV: 160 series
//   Recently Added: 12 items (7 days)
//
// Sabnzbd: WARNING
//   Queue: 3 items (25.5 MB/s)
//   Warnings: 1 (disk space low)
//
// Issues:
//   - Sabnzbd: Low disk space warning
```

### 3. Error Message Polish

Review and improve all error messages:

| Scenario | Before | After |
|----------|--------|-------|
| Invalid API key | "401 Unauthorized" | "Invalid Sonarr API key. Check your configuration." |
| Service down | "ECONNREFUSED" | "Cannot connect to Sonarr at http://... Is the service running?" |
| Movie not found | "Movie with ID 999 not found" | "No movie found with ID 999. Use movie_list to find valid IDs." |
| 4K not configured | "radarr4k.url is undefined" | "Radarr4K is not configured. Add radarr4k settings or use quality: 'hd'" |

### 4. Large Library Handling

For libraries with thousands of items:

**Pagination:**
```typescript
tv_list({ limit: 50, offset: 100 })
movie_list({ limit: 50, offset: 100 })
```

**Summary Mode:**
```typescript
tv_list({ summary: true })
// Response: "160 TV series (89 continuing, 71 ended). Use filters to narrow down."
```

**Smart Defaults:**
- Default limit of 100 for list operations
- Warn when results exceed 500
- Suggest filters for large result sets

### 5. media_help Enhancement

Comprehensive help system:

```typescript
media_help()
// Response:
// Media Management Server
//
// Connected Services:
//   - Sonarr (TV Shows) ✓
//   - Radarr (Movies) ✓
//   - Radarr4K (4K Movies) ✓
//   - Plex (Library) ✓
//   - Sabnzbd (Downloads) ✓
//
// Quick Start:
//   "Add Breaking Bad" → Search and add TV show
//   "Get Inception in 4K" → Search and add 4K movie
//   "What's downloading?" → Check download status
//   "Do I have The Office?" → Search Plex library
//
// For detailed help: media_help({ topic: 'tv' | 'movies' | 'library' | 'downloads' | 'cleanup' })

media_help({ topic: 'cleanup' })
// Response:
// Cleanup Tools
//
// Find Candidates:
//   plex_unwatched({ days: 365 }) - Movies not watched in a year
//   plex_watched_old({ days: 180 }) - Watched movies ready to delete
//   plex_duplicates() - Duplicate files wasting space
//
// Analysis:
//   cleanup_analysis() - Overview of all cleanup opportunities
//
// Delete (with confirmation):
//   plex_delete({ rating_key: 12345, confirm: true })
//   movie_delete({ movie_id: 123, delete_files: true })
//   sonarr_delete({ series_id: 456, delete_files: true })
```

---

## Design Decisions (Inherited)

### From Phase 0010

| Decision | Value |
|----------|-------|
| HTTP Client | Native fetch |
| Output Format | Simple text |
| Parameter Types | z.coerce.number() |
| List Tools | Filtering + sorting + display options |

### New Decisions

| Decision | Value | Rationale |
|----------|-------|-----------|
| Default Limit | 100 items | Prevent token overflow |
| Summary Mode | Available for all list tools | Quick counts without full data |
| Verbose Health | Optional detailed view | Balance between quick check and full status |

---

## Verification Gate

**USER GATE** - Complete end-to-end verification:

- [ ] All core workflows work smoothly:
  - [ ] "Add Breaking Bad" → searches, adds, reports
  - [ ] "Get Inception in 4K" → routes to Radarr4K correctly
  - [ ] "What's downloading?" → unified view with sources
  - [ ] "Something is stuck" → health check + fix workflow
  - [ ] "Do I have The Office?" → library search with context
- [ ] Large libraries handled gracefully (no timeouts, no token overflow)
- [ ] Error messages are actionable
- [ ] 4K routing is safe (never accidental)
- [ ] All extended tools work
- [ ] README is complete and accurate
- [ ] Works with Claude Desktop
- [ ] Works with Claude Code

---

## Dependencies

- Phase 0010 (Sonarr) must be complete
- Phase 0020 (Radarr) must be complete
- Phase 0030 (Plex) must be complete
- Phase 0040 (Sabnzbd) must be complete

---

## Technical Notes

### Performance Considerations

1. **Parallel API calls** where possible:
   ```typescript
   const [sonarr, radarr, plex, sabnzbd] = await Promise.all([
     sonarrClient.health(),
     radarrClient.health(),
     plexClient.health(),
     sabnzbdClient.health(),
   ]);
   ```

2. **Early termination** for large results:
   ```typescript
   if (results.length > MAX_RESULTS) {
     return {
       content: [{
         type: "text",
         text: `Found ${results.length} items. Showing first ${MAX_RESULTS}.\n` +
               `Use filters to narrow down: status, network, genre, etc.`
       }]
     };
   }
   ```

3. **Lazy loading** for cross-references:
   ```typescript
   // Don't fetch *arr queue data unless needed
   if (includeSource) {
     await loadArrReferences(downloads);
   }
   ```

### Testing Checklist

Before marking complete:

- [ ] Test each tool individually
- [ ] Test common workflows end-to-end
- [ ] Test with empty libraries
- [ ] Test with large libraries (100+ items)
- [ ] Test error scenarios (service down, bad config)
- [ ] Test 4K safety (verify no accidental 4K)
- [ ] Test with Claude Desktop
- [ ] Test with Claude Code
- [ ] Review all error messages
- [ ] Verify README accuracy
