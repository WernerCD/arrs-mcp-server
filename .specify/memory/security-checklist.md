# Security Checklist

> **Agents**: Verify these security requirements are met before any release.

**Last Updated**: 2026-01-24

## Credential Protection

### MUST

- [ ] `config.json` is in `.gitignore`
- [ ] `.env` is in `.gitignore`
- [ ] No API keys in source code
- [ ] No API keys in commit history

### SHOULD

- [ ] Document credential setup in README
- [ ] Provide example config template (without real values)
- [ ] Validate config exists at startup

## Network Security

### Configuration

- Services are accessed via HTTPS URLs (port-forwarded)
- API keys provide authentication
- MCP server runs locally, not exposed to network

### Considerations

| Aspect | Status | Notes |
|--------|--------|-------|
| HTTPS | User-configured | Depends on user's port forwarding setup |
| API Key Strength | Service-dependent | Use strong, unique keys per service |
| Local Only | Yes | MCP server not exposed to network |

## Code Security

### Input Validation

- [ ] All tool inputs validated via zod schemas
- [ ] No dynamic code execution
- [ ] No shell command injection possible

### Output Handling

- [ ] Error messages don't leak credentials
- [ ] API responses sanitized before returning
- [ ] Logs don't contain sensitive data

## Configuration Template

Create `config.example.json`:

```json
{
  "sonarr": {
    "url": "https://your-sonarr-domain.com",
    "apiKey": "your-api-key-here"
  },
  "radarr": {
    "url": "https://your-radarr-domain.com",
    "apiKey": "your-api-key-here"
  },
  "radarr4k": {
    "url": "https://your-radarr4k-domain.com",
    "apiKey": "your-api-key-here"
  },
  "plex": {
    "url": "https://your-plex-domain.com",
    "token": "your-plex-token-here"
  },
  "sabnzbd": {
    "url": "https://your-sabnzbd-domain.com",
    "apiKey": "your-api-key-here"
  }
}
```

## Gitignore Requirements

```gitignore
# Credentials
config.json
.env
.env.*

# IDE
.idea/
.vscode/

# Build
build/
dist/
node_modules/

# Workflow state
.specflow/
```

## Pre-Commit Checks

Before committing:

1. `git diff --cached` - Review all staged changes
2. Search for API key patterns in staged files
3. Verify config files are not staged

## Incident Response

If credentials are accidentally committed:

1. Immediately rotate all exposed API keys
2. Remove from git history: `git filter-branch` or BFG Repo-Cleaner
3. Force push to remote (if applicable)
4. Verify services still work with new keys
