# Discovery Decisions

Decisions captured during SpecFlow discovery interview.

---

#### D-1: Project Purpose
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: Initial project definition
- **Decision**: MCP server providing Claude/AI full control over personal *arr media stack (Sonarr, Radarr, Radarr4k, Plex, Sabnzbd) running on QNAP Container Station
- **Alternatives**: REST API wrapper, CLI tool - rejected as MCP is the target integration
- **Consequences**: Enables AI-driven media management, Requires MCP SDK knowledge, Requires API integration with 5 services
- **Memory Doc Impact**: constitution.md, tech-stack.md

#### D-2: Software Type
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: Architecture classification
- **Decision**: MCP Server (Model Context Protocol)
- **Alternatives**: REST API, CLI, Library
- **Consequences**: Requires MCP SDK, Enables Claude Desktop/Code integration
- **Memory Doc Impact**: tech-stack.md

#### D-3: Target Audience
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: User persona definition
- **Decision**: Developers using Claude + Home server admins
- **Alternatives**: General automation enthusiasts
- **Consequences**: Focus on Claude integration quality, Secondary focus on admin-friendly configuration
- **Memory Doc Impact**: constitution.md

#### D-4: Technology Stack
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: Language/runtime selection
- **Decision**: TypeScript + Node.js - aligns with MCP SDK ecosystem and existing projects (clawdbot, ai-assist)
- **Alternatives**: Python MCP SDK - rejected for consistency with existing work
- **Consequences**: Enables code reuse patterns from existing projects, Requires TypeScript tooling
- **Memory Doc Impact**: tech-stack.md, coding-standards.md

#### D-5: Team Structure
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: Maintenance model
- **Decision**: Solo project - single maintainer
- **Alternatives**: Open source, team project
- **Consequences**: Can move fast, No PR review overhead, Documentation for self is sufficient
- **Memory Doc Impact**: constitution.md

#### D-6: Criticality Level
- **Phase**: 0 - Discovery
- **Status**: Decided
- **Confidence**: High
- **Context**: Reliability requirements
- **Decision**: Home Production - regularly used, should be reliable but not mission-critical
- **Alternatives**: Hobby (too casual), Production (overkill)
- **Consequences**: Needs good error handling, Logging important, Some downtime acceptable, No SLA requirements
- **Memory Doc Impact**: constitution.md, testing-strategy.md

#### D-7: Core Value Proposition
- **Phase**: 1 - Problem & Vision
- **Status**: Decided
- **Confidence**: High
- **Context**: Why build this
- **Decision**: Unified natural language control + AI automation across 5 media services. Single conversation interface instead of 5 separate web UIs.
- **Alternatives**: Individual app usage, custom scripts
- **Consequences**: MCP tools must be intuitive and well-named, Error messages must be helpful
- **Memory Doc Impact**: constitution.md

#### D-8: V1.0 Success Criteria
- **Phase**: 1 - Problem & Vision
- **Status**: Decided
- **Confidence**: High
- **Context**: Definition of done for v1.0
- **Decision**: Full management capability - everything the web UIs can do should be possible through Claude
- **Alternatives**: Basic CRUD only, Smart workflows
- **Consequences**: Complete API coverage required for each service, More tools to implement
- **Memory Doc Impact**: constitution.md, roadmap

#### D-9: Service Priority Order
- **Phase**: 1 - Problem & Vision
- **Status**: Decided
- **Confidence**: High
- **Context**: Implementation phasing
- **Decision**: Sonarr -> Radarr -> Plex -> Sabnzbd (then Radarr4k uses same code as Radarr)
- **Alternatives**: All at once, Plex first
- **Consequences**: Can validate MCP patterns early with Sonarr, Radarr4k is mostly config, Learn from each integration
- **Memory Doc Impact**: roadmap

#### D-10: Use Case Priority
- **Phase**: 2 - Users & Personas
- **Status**: Decided
- **Confidence**: High
- **Context**: Common request types
- **Decision**: All four use case types are high priority: Add new content, Check status, Search/browse, Troubleshoot
- **Alternatives**: Focus on subset
- **Consequences**: Tools must cover CRUD + status + diagnostics, Need comprehensive API coverage
- **Memory Doc Impact**: roadmap, spec templates

#### D-11: MCP SDK Pattern
- **Phase**: 2 - Users & Personas
- **Status**: Decided
- **Confidence**: High
- **Context**: Implementation pattern from MCP docs research
- **Decision**: Use TypeScript MCP SDK with McpServer class, zod for input schemas, StdioServerTransport
- **Alternatives**: FastMCP (Python only)
- **Consequences**: Follow @modelcontextprotocol/sdk patterns, Use server.registerTool() pattern
- **Memory Doc Impact**: tech-stack.md, coding-standards.md

#### D-12: Tool Coverage Strategy
- **Phase**: 3 - Core Features
- **Status**: Decided
- **Confidence**: High
- **Context**: API coverage approach
- **Decision**: Two-tier tool system: Core tools (frequently used, prominent) + Extended tools (advanced, discoverable). Claude can query extended tool catalog when needed.
- **Alternatives**: All tools equal, Curated only
- **Consequences**: Need to categorize each API endpoint, Extended tools can be listed/described dynamically
- **Memory Doc Impact**: api-standards.md, spec templates

#### D-13: Tool Design Pattern
- **Phase**: 3 - Core Features
- **Status**: Decided
- **Confidence**: High
- **Context**: Granularity of tool design
- **Decision**: Both granular primitives AND convenience wrappers. Primitives for flexibility, wrappers for common workflows.
- **Alternatives**: Granular only, Compound only
- **Consequences**: More tools to maintain, Better UX for common tasks
- **Memory Doc Impact**: coding-standards.md

#### D-14: Radarr4k Architecture
- **Phase**: 3 - Core Features
- **Status**: Decided
- **Confidence**: High
- **Context**: Multi-instance Radarr handling
- **Decision**: Same tool set with explicit 'radarr4k' or 'use_4k: true' flag. DEFAULT IS REGULAR RADARR. 4k only when explicitly requested. Separate movie lists, separate configs - treated as distinct but sharing code.
- **Alternatives**: Separate tool prefixes, Auto-routing by quality
- **Consequences**: Safe default (no accidental 4k downloads), Single codebase, Config needs both instances
- **Memory Doc Impact**: tech-stack.md, security-checklist.md

#### D-15: Credential Management
- **Phase**: 4 - Technical Architecture
- **Status**: Decided
- **Confidence**: High
- **Context**: How to handle API keys for Sonarr, Radarr, etc.
- **Decision**: API keys stored in config file (JSON or env vars). Config file excluded from git.
- **Alternatives**: Keychain (overkill for home use), Runtime input (inconvenient)
- **Consequences**: Need .gitignore for config, Need config schema, Document setup process
- **Memory Doc Impact**: tech-stack.md, security-checklist.md

#### D-16: MCP Transport
- **Phase**: 4 - Technical Architecture
- **Status**: Decided
- **Confidence**: High
- **Context**: How MCP server communicates with clients
- **Decision**: stdio transport (stdin/stdout JSON-RPC)
- **Alternatives**: SSE/HTTP, WebSocket
- **Consequences**: Simple deployment, Works with both Claude Desktop and Code, No port management
- **Memory Doc Impact**: tech-stack.md

#### D-17: Target Clients
- **Phase**: 4 - Technical Architecture
- **Status**: Decided
- **Confidence**: High
- **Context**: Which Claude clients to support
- **Decision**: Both Claude Desktop and Claude Code
- **Alternatives**: Single client
- **Consequences**: Must test with both, stdio works for both, Config examples for both
- **Memory Doc Impact**: constitution.md

#### D-18: State Management
- **Phase**: 5 - Data & Storage
- **Status**: Decided
- **Confidence**: High
- **Context**: Whether MCP server maintains its own state
- **Decision**: Stateless - all state lives in the *arr applications. MCP server is a pure pass-through.
- **Alternatives**: Caching, Action history
- **Consequences**: Simpler architecture, No database needed, Each request is independent
- **Memory Doc Impact**: tech-stack.md

#### D-19: Response Formatting
- **Phase**: 5 - Data & Storage
- **Status**: Decided
- **Confidence**: High
- **Context**: How tool responses are structured
- **Decision**: Hybrid - human-readable text for simple queries (status, search results), structured JSON for complex data (full series info, queue details)
- **Alternatives**: Always text, Always JSON
- **Consequences**: Need formatting helpers, Claude can parse both, Better UX for common cases
- **Memory Doc Impact**: coding-standards.md, api-standards.md

#### D-20: Network Exposure
- **Phase**: 6 - Security & Compliance
- **Status**: Decided
- **Confidence**: High
- **Context**: How services are accessed
- **Decision**: Internet accessible via port-forwarded URLs
- **Alternatives**: VPN only, Local only
- **Consequences**: API keys are security-critical, HTTPS recommended, Config must not be committed to git
- **Memory Doc Impact**: security-checklist.md

#### D-21: Operation Confirmation
- **Phase**: 6 - Security & Compliance
- **Status**: Decided
- **Confidence**: High
- **Context**: Whether to require user confirmation for operations
- **Decision**: No confirmation required - trust Claude to execute all operations
- **Alternatives**: Confirm destructive, Confirm all writes
- **Consequences**: Faster workflow, Relies on Claude's judgment, Radarr4k flag becomes the safety mechanism for 4K
- **Memory Doc Impact**: constitution.md

#### D-22: Usage Pattern
- **Phase**: 7 - Performance & Scale
- **Status**: Decided
- **Confidence**: High
- **Context**: How frequently the MCP will be used
- **Decision**: Occasional use - few requests per day when thinking of media to add
- **Alternatives**: Regular monitoring, Automation heavy
- **Consequences**: No caching needed, No rate limiting concerns, Simple architecture sufficient
- **Memory Doc Impact**: tech-stack.md

#### D-23: API Timeouts
- **Phase**: 7 - Performance & Scale
- **Status**: Decided
- **Confidence**: High
- **Context**: HTTP timeout configuration
- **Decision**: Standard 30-second timeout for all operations
- **Alternatives**: Extended, Configurable per-service
- **Consequences**: Simple timeout handling, May need adjustment if search is slow
- **Memory Doc Impact**: coding-standards.md

#### D-24: Testing Approach
- **Phase**: 8 - Testing Strategy
- **Status**: Decided
- **Confidence**: High
- **Context**: How to verify the MCP works correctly
- **Decision**: Manual testing - test by actually using with Claude against production
- **Alternatives**: Unit tests with mocks, Integration tests
- **Consequences**: Faster development, Less test infrastructure, Need to be careful with destructive operations during testing
- **Memory Doc Impact**: testing-strategy.md

#### D-25: Test Environment
- **Phase**: 8 - Testing Strategy
- **Status**: Decided
- **Confidence**: High
- **Context**: Where to test
- **Decision**: Production only - test carefully against real services on QNAP
- **Alternatives**: Separate test instances, Docker containers
- **Consequences**: Be careful during development, Use read operations first when testing
- **Memory Doc Impact**: testing-strategy.md

#### D-26: Deployment Environment
- **Phase**: 9 - Deployment & Operations
- **Status**: Decided
- **Confidence**: High
- **Context**: Where the MCP server process runs
- **Decision**: Local Mac - runs alongside Claude Code/Desktop. MCP server connects to remote *arr APIs over the network.
- **Alternatives**: QNAP container, Either
- **Consequences**: Simple deployment via npm/node, No Docker needed for MCP itself, Network latency to QNAP APIs
- **Memory Doc Impact**: tech-stack.md

#### D-27: Error Handling
- **Phase**: 9 - Deployment & Operations
- **Status**: Decided
- **Confidence**: High
- **Context**: How to communicate errors to Claude/user
- **Decision**: Graceful user-friendly error messages that Claude can interpret and explain
- **Alternatives**: Technical details, Both levels
- **Consequences**: Good UX, May need verbose logging to stderr for debugging
- **Memory Doc Impact**: coding-standards.md

#### D-28: Future Services
- **Phase**: 10 - Future & Extensibility
- **Status**: Decided
- **Confidence**: Medium
- **Context**: What services might be added later
- **Decision**: Overseerr is installed and a candidate. Lidarr/Readarr tried but didn't stick. Architecture should support these additions.
- **Alternatives**: Hard-coded service list
- **Consequences**: Design for extensibility, Overseerr could be Phase 5 or future version
- **Memory Doc Impact**: roadmap, tech-stack.md

#### D-29: Extensibility Architecture
- **Phase**: 10 - Future & Extensibility
- **Status**: Decided
- **Confidence**: High
- **Context**: How easily can new services be added
- **Decision**: Plugin-like architecture - each service is a self-contained module with its own tools, client, and types
- **Alternatives**: Moderate separation, Minimal
- **Consequences**: More upfront structure, Easier to add services, Common interface pattern across modules
- **Memory Doc Impact**: coding-standards.md, tech-stack.md

#### D-30: Sonarr Core Tools
- **Phase**: 11 - Memory Bootstrap
- **Status**: Decided
- **Confidence**: High
- **Context**: Which Sonarr operations are core vs extended
- **Decision**: CORE: search_series, add_series, list_series, get_queue. EXTENDED: delete_series, edit_series, get_calendar, manage_quality_profiles, etc.
- **Alternatives**: All equal priority
- **Consequences**: Core tools are prominently documented, Extended tools discoverable
- **Memory Doc Impact**: api-standards.md

#### D-31: Radarr Core Tools
- **Phase**: 11 - Memory Bootstrap
- **Status**: Decided
- **Confidence**: High
- **Context**: Which Radarr operations are core vs extended
- **Decision**: CORE: search_movie, add_movie, list_movies, get_queue. EXTENDED: delete_movie, edit_movie, manage_quality_profiles, etc.
- **Alternatives**: All equal priority
- **Consequences**: Mirrors Sonarr pattern for consistency
- **Memory Doc Impact**: api-standards.md

#### D-32: Plex Core Tools
- **Phase**: 11 - Memory Bootstrap
- **Status**: Decided
- **Confidence**: High
- **Context**: Which Plex operations are core vs extended
- **Decision**: CORE: list_libraries, search_library. EXTENDED: refresh_library, now_playing, get_metadata, etc.
- **Alternatives**: More ops in core
- **Consequences**: Plex is more read-focused, less critical than *arr apps
- **Memory Doc Impact**: api-standards.md

#### D-33: Sabnzbd Core Tools
- **Phase**: 11 - Memory Bootstrap
- **Status**: Decided
- **Confidence**: High
- **Context**: Which Sabnzbd operations are core vs extended
- **Decision**: CORE: get_queue, get_history, pause_resume, set_speed, delete_item, get_failed, retry_item. EXTENDED: move_item, get_categories, pause/resume single items.
- **Alternatives**: Simpler core
- **Consequences**: Full control in core due to frequent troubleshooting needs
- **Memory Doc Impact**: api-standards.md

---

## Deep Dive Decisions (Detailed Interview)

#### D-34: Sonarr Add Workflow
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: How series are added
- **Decision**: Need monitor options (all/future/missing/none), quality profile selection, root folder selection (kids vs tv). Always search by name, never by ID.
- **Memory Doc Impact**: api-standards.md

#### D-35: Sonarr Episode Management
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Episode-level operations
- **Decision**: Episode status checking is frequent (missing/available). Search missing episodes is used sometimes. Calendar rarely used.
- **Memory Doc Impact**: api-standards.md

#### D-36: Queue Management Needs
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: What's needed when checking downloads
- **Decision**: Need progress, ETA, errors, stuck imports. Common workflow: see stuck → investigate → blacklist + re-search. Manual import visibility important.
- **Memory Doc Impact**: api-standards.md

#### D-37: Radarr Quality Upgrades
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Re-searching for better quality
- **Decision**: Search for quality upgrade happens ~10% of time (fairly often). Need explicit tool for this. Also delete movies is core (cleanup space).
- **Memory Doc Impact**: api-standards.md

#### D-38: Plex Usage Patterns
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: What Plex operations matter
- **Decision**: Core: Check what I have, watch status. Extended: Delete (with care), find stale content. Library scan not needed (auto-detection). Future: Streaming service cross-reference.
- **Memory Doc Impact**: api-standards.md

#### D-39: Sabnzbd Full Control
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Level of SAB control needed
- **Decision**: Full control: delete, retry, investigate failures. History with full details important. Speed limiting sometimes used. Categories are in use.
- **Memory Doc Impact**: api-standards.md

#### D-40: Unified Status View
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Cross-service queue view
- **Decision**: Unified "what's downloading" tool that checks Sabnzbd (source of truth) + shows which items are tracked in Sonarr/Radarr. Highlight orphaned items. Smart deduplication.
- **Memory Doc Impact**: api-standards.md

#### D-41: Health Check Tool
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Problem detection across services
- **Decision**: Health check tool that reports connection issues, stuck imports, failed downloads across all services. Troubleshooting capability important.
- **Memory Doc Impact**: api-standards.md

#### D-42: Tool Discovery
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: How to discover extended tools
- **Decision**: Dedicated `arr_list_capabilities` tool that lists all available tools by service and category.
- **Memory Doc Impact**: api-standards.md

#### D-43: Top Priority Workflows
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: What matters most
- **Decision**: 1. Add content (most frequent), 2. Fix problems (stuck imports, blacklist), 3. Check status (when anxious or broken), 4. Library cleanup (nice-to-have future).
- **Memory Doc Impact**: roadmap, api-standards.md

#### D-44: Search Result Handling
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Multiple search results
- **Decision**: Smart pick - if one result is obviously correct, use it; otherwise ask user to choose.
- **Memory Doc Impact**: coding-standards.md

#### D-45: Default Monitor Behavior
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: What to monitor when adding
- **Decision**: TV shows: monitor ALL episodes by default. Movies: monitor + search immediately. Unless user specifies otherwise.
- **Memory Doc Impact**: api-standards.md

#### D-46: Default Root Folders
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Which folder to use by default
- **Decision**: Sonarr: /tv (default), /kids (when specified). Radarr: /Movies. Radarr4K: /share/movies.
- **Memory Doc Impact**: tech-stack.md, api-standards.md

#### D-47: Duplicate Handling
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: What to do when item already exists
- **Decision**: Inform user it exists and offer options (check status, search for upgrade, etc.)
- **Memory Doc Impact**: api-standards.md

#### D-48: Plex Library Display
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: How to show Plex results
- **Decision**: Always show which library (Movies, TV Shows, Kids Shows, Movies (4K)) each result is from.
- **Memory Doc Impact**: api-standards.md

#### D-49: Semantic Tool Naming
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: Tool names for Claude understanding
- **Decision**: Use semantic names for user-facing tools (tv_search, movie_add, library_search, downloads_status). Keep service-specific names for admin/troubleshooting (sonarr_queue, sabnzbd_retry). Claude doesn't know what "Sonarr" is, but understands "tv_search".
- **Memory Doc Impact**: api-standards.md, coding-standards.md

#### D-50: Help/Info Tool
- **Phase**: Deep Dive
- **Status**: Decided
- **Confidence**: High
- **Context**: How Claude learns the system
- **Decision**: Create `media_help` tool that provides: 1) All available tools with descriptions, 2) System architecture explanation (Sonarr=TV, Radarr=Movies, etc.), 3) Which services are connected. Claude can auto-call when unsure.
- **Memory Doc Impact**: api-standards.md

---

