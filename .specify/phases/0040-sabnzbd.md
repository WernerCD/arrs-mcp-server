# Phase 0040: Sabnzbd (Downloads)

**Status**: Not Started
**Branch**: `0040-sabnzbd`
**Estimated Scope**: Medium (simpler API, focus on queue management)

---

## Goals

1. Build Sabnzbd API client with query-parameter authentication
2. Implement download semantic tools (queue, history, pause, resume, speed)
3. Implement admin tools (delete, failed, retry, priority, categories)
4. Enhance downloads_status with full Sabnzbd integration
5. Enable cross-service download tracking (what's in Sabnzbd for which *arr)
6. Complete the unified download management experience

---

## Scope

### In Scope

- `src/services/sabnzbd/` module (client.ts, types.ts, tools.ts, index.ts)
- All download tools from api-standards.md
- Queue management (pause, resume, priority, delete)
- History and retry workflows
- Speed limiting
- Integration with *arr tracking

### Out of Scope

- NZB file uploads (use *arr for adding content)
- Sabnzbd configuration/settings
- Server administration
- RSS feed management

---

## Deliverables

1. **Sabnzbd API Client**
   - `src/services/sabnzbd/client.ts` - API client
   - `src/services/sabnzbd/types.ts` - TypeScript types
   - `src/services/sabnzbd/tools.ts` - MCP tool registrations
   - `src/services/sabnzbd/index.ts` - Exports

2. **Semantic Tools (5)**
   - `downloads_queue` - Current download queue with full details
   - `downloads_history` - Download history with status/size/time/errors
   - `downloads_pause` - Pause all downloads
   - `downloads_resume` - Resume all downloads
   - `downloads_speed` - Set speed limit

3. **Admin Tools (7)**
   - `sabnzbd_delete` - Remove item from queue
   - `sabnzbd_failed` - Get failed downloads for investigation
   - `sabnzbd_retry` - Retry a failed download
   - `sabnzbd_priority` - Change queue priority
   - `sabnzbd_categories` - List download categories
   - `sabnzbd_pause_item` - Pause specific item
   - `sabnzbd_resume_item` - Resume specific item

4. **Cross-Service Updates**
   - Full integration of `downloads_status` with Sabnzbd queue
   - Cross-reference downloads with Sonarr/Radarr (which *arr is this for?)

---

## Design Decisions (Inherited from Phase 0010)

### Technical Patterns

| Decision | Value | Rationale |
|----------|-------|-----------|
| HTTP Client | Native fetch | Consistent with other services |
| Output Format | Simple text | Readable, token-efficient |
| Parameter Types | z.coerce.number() | MCP passes numbers as strings |
| Module Pattern | client/types/tools per service | Clean separation |

### Sabnzbd-Specific Considerations

| Decision | Value | Rationale |
|----------|-------|-----------|
| Authentication | apikey query parameter | Sabnzbd API standard |
| Output Format | JSON | Request `output=json` |
| Cross-Reference | Match by title/nzo_id | Link to *arr sources |

---

## Sabnzbd API Reference

### Authentication

```typescript
// API key via query parameter
const url = `${baseUrl}/api?mode=${mode}&apikey=${apiKey}&output=json`;
```

### Key Modes (Endpoints)

| Mode | Description |
|------|-------------|
| `queue` | Get download queue |
| `history` | Get download history |
| `pause` | Pause all downloads |
| `resume` | Resume all downloads |
| `config` | Get speed limit and other settings |
| `speedlimit` | Set speed limit |
| `queue&name=delete&value=` | Delete from queue |
| `retry&value=` | Retry failed download |
| `queue&name=priority&value=&value2=` | Change priority |

### Key Types

```typescript
interface SabnzbdQueue {
  status: 'Downloading' | 'Paused' | 'Idle';
  speed: string;           // "25.5 M" (MB/s)
  timeleft: string;        // "2:30:45"
  mb: string;              // Total MB remaining
  mbleft: string;          // MB left for current item
  slots: SabnzbdQueueSlot[];
}

interface SabnzbdQueueSlot {
  nzo_id: string;          // Unique ID
  filename: string;        // Display name
  status: 'Downloading' | 'Queued' | 'Paused' | 'Verifying' | 'Extracting';
  mb: string;              // Total size
  mbleft: string;          // Remaining
  percentage: string;      // "45"
  timeleft: string;        // ETA
  cat: string;             // Category (tv, movies, etc.)
  priority: string;        // Priority level
}

interface SabnzbdHistorySlot {
  nzo_id: string;
  name: string;
  status: 'Completed' | 'Failed' | 'Queued';
  fail_message?: string;
  bytes: number;
  download_time: number;   // seconds
  completed: number;       // timestamp
  category: string;
  storage: string;         // Final path
}
```

---

## downloads_status Enhancement

This tool becomes the unified download view:

```text
Downloads Status

Speed: 25.5 MB/s | Queue: 3 items | ETA: 2:30:45

Queue:
  1. Show.S01E05.720p.WEB-DL [Sonarr] - 45% (1.2 GB / 2.7 GB) - ETA: 15m
  2. Movie.2024.1080p.BluRay [Radarr] - Queued (8.5 GB)
  3. Show.S01E06.720p.WEB-DL [Sonarr] - Queued (2.8 GB)

Issues:
  - Radarr: 2 stuck imports
  - Sonarr: 1 failed download

Recent Completions:
  - Movie.2023.1080p.BluRay - Completed 15m ago (12.3 GB)
```

### Cross-Reference Logic

To determine which *arr a download belongs to:

1. Check Sabnzbd category (often `tv`, `movies`, `movies-4k`)
2. Query Sonarr/Radarr queues for matching download IDs
3. Match by title if ID not found

```typescript
interface EnhancedDownload {
  nzo_id: string;
  title: string;
  progress: number;
  eta: string;
  status: string;
  size: number;
  source: 'sonarr' | 'radarr' | 'radarr4k' | 'unknown';
  issues?: string[];
}
```

---

## Queue Management Workflows

### "Pause downloads"

```typescript
downloads_pause()
// Response: "Downloads paused. 3 items in queue."
```

### "Resume downloads"

```typescript
downloads_resume()
// Response: "Downloads resumed. Speed: 25.5 MB/s"
```

### "Slow down downloads" / "Limit speed"

```typescript
downloads_speed({ speed: 10 })  // 10 MB/s
// Response: "Speed limit set to 10 MB/s"

downloads_speed({ speed: 'unlimited' })
// Response: "Speed limit removed"
```

### "Move X to top of queue"

```typescript
sabnzbd_priority({ nzo_id: 'abc123', position: 'top' })
// Response: "Moved 'Show.S01E05' to top of queue"
```

---

## Failed Download Handling

### Investigation

```typescript
sabnzbd_failed()
// Response:
// 2 failed downloads:
//
// 1. Show.S01E03.720p - Failed: "Incomplete download"
//    Category: tv | Size: 2.1 GB | Completed: 2024-01-15 10:30
//
// 2. Movie.2024.1080p - Failed: "Unpacking failed"
//    Category: movies | Size: 8.5 GB | Completed: 2024-01-15 09:15
```

### Retry

```typescript
sabnzbd_retry({ nzo_id: 'abc123' })
// Response: "Retrying 'Show.S01E03.720p'. Added to queue."
```

---

## Verification Gate

Download tools work correctly:

- [ ] `downloads_queue` shows current queue with details
- [ ] `downloads_history` shows history with status
- [ ] `downloads_pause` pauses all downloads
- [ ] `downloads_resume` resumes downloads
- [ ] `downloads_speed` sets speed limit correctly
- [ ] `sabnzbd_delete` removes items from queue
- [ ] `sabnzbd_failed` lists failed downloads
- [ ] `sabnzbd_retry` retries failed downloads
- [ ] `sabnzbd_priority` changes queue order
- [ ] `downloads_status` shows unified view with *arr cross-references
- [ ] `system_health` includes Sabnzbd status

---

## Dependencies

- Phase 0010 (Sonarr) must be complete
- Phase 0020 (Radarr) should be complete for full cross-reference

---

## Technical Notes

### Speed Parsing

Sabnzbd returns speed as string like "25.5 M". Parse for display:

```typescript
function formatSpeed(speed: string): string {
  // "25.5 M" -> "25.5 MB/s"
  return speed.replace(' M', ' MB/s').replace(' K', ' KB/s');
}
```

### Category Mapping

Common category-to-source mapping:

| Category | Likely Source |
|----------|---------------|
| `tv`, `sonarr` | Sonarr |
| `movies`, `radarr` | Radarr |
| `movies-4k`, `radarr4k` | Radarr4K |
| `audio`, `lidarr` | Lidarr (not supported) |

### NZO ID

The `nzo_id` is Sabnzbd's unique identifier for each download. Use it for:
- Delete operations
- Retry operations
- Priority changes
- Pause/resume individual items
