# Testing Strategy

> **Agents**: Reference this for testing approaches and verification methods.

**Last Updated**: 2026-01-24

## Testing Approach

This project uses **manual testing** against production services. There are no automated test suites.

## Testing Environment

| Aspect | Detail |
|--------|--------|
| Services | Production instances on QNAP Container Station |
| Access | Internet-accessible via port-forwarded URLs |
| Risk Level | Medium - be careful with write operations |

## Testing Phases

### Phase 1: Read Operations First

Before testing any write operations, verify reads work correctly:

1. `tv_list` - Confirm API connection and response parsing
2. `sonarr_queue` - Verify queue data structure
3. `tv_search` - Test search functionality

### Phase 2: Safe Write Operations

Test non-destructive writes:

1. `tv_add` - Add a show you actually want
2. `movie_add` - Add a movie you actually want
3. `downloads_pause` / `downloads_resume` - Safe to toggle

### Phase 3: Destructive Operations

Only test when confident in the implementation:

1. Delete operations - Use items you want to remove anyway
2. Bulk operations - Start with small sets

## Verification Checklist

### Per-Tool Verification

- [ ] Tool registers correctly (shows in Claude's tool list)
- [ ] Input validation works (zod schemas)
- [ ] API call succeeds with valid credentials
- [ ] Response is formatted correctly for Claude
- [ ] Error messages are user-friendly
- [ ] Timeout handling works

### Per-Service Verification

- [ ] Client connects with configured credentials
- [ ] All core tools functional
- [ ] Extended tools accessible via catalog

### Integration Verification

- [ ] Works with Claude Desktop
- [ ] Works with Claude Code
- [ ] Configuration loads correctly from file/env
- [ ] Server starts without errors
- [ ] Server shuts down cleanly

## Testing Commands

```bash
# Build and run
pnpm build && node build/index.js

# Test with Claude Desktop
# 1. Update claude_desktop_config.json
# 2. Restart Claude Desktop
# 3. Try: "What series do I have in Sonarr?"

# Test with Claude Code
# 1. Update .claude/settings.json
# 2. Restart Claude Code
# 3. Try: "List my Sonarr series"
```

## Known Testing Risks

| Risk | Mitigation |
|------|-----------|
| Accidental 4K downloads | `quality` defaults to `'hd'`, only `'4k'` when explicit |
| Deleting wanted content | Test deletes only on items you want to remove |
| API rate limiting | Unlikely with occasional use pattern |
| Credentials in logs | Never log API keys, only stderr for debug |

## Debug Logging

Enable verbose logging for troubleshooting:

```typescript
// In development only
console.error("[DEBUG] Sonarr search:", query);
console.error("[DEBUG] Response:", JSON.stringify(result, null, 2));
```

Remember: stdout is reserved for MCP protocol, use stderr for all logging.
