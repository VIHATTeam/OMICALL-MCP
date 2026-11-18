# Changelog

All notable changes to OmiCall MCP Server are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versioning follows [Semantic Versioning](https://semver.org/).

## [1.4.1] - 2026-11-18

### Fixed
- The first tool call now logs in automatically when there is no saved session. Previously it could fail with `http_500` until `login_omicall_mcp` was called explicitly.
- Several MCP servers for different accounts or tenants on the same machine no longer overwrite each other's session: sessions are now stored per account. A session belonging to another account is never reused.
- Clear error messages when automatic login isn't possible (no credentials configured, or the account has several tenants and none is selected).

### Added
- `get_omibot` and `get_callbot_script` accept `full: true` to return the whole system prompt / SmartAgent instruction instead of the first 3,000 characters.

## [1.4.0] - 2026-11-17

### Added
- AI configuration tools: view AI chatbots (OmiBot) and their settings, available AI models, knowledge documents and FAQ sets, AI conversation assistants, and voice callbot scripts including SmartAgent (AI voice agent); turn the AI bot on/off for a conversation; enable/disable conversation assistants; pause callbot campaigns.
- OmiFlow tools: list flows, see flows with errors, view a flow's steps, performance reports and run history, and pause or resume a flow.
- New toolset names `aiconfig` and `omiflow` for `OMICALL_TOOLSETS`.

### Fixed
- Contact search/count filters now work in MCP clients that don't support JSON Schema references (e.g. OpenAI Codex).

### Documentation
- Setup instructions for OpenAI Codex.
- Rewritten README: example prompts, full configuration table, every tool with its impact level, toolset sizes, and a Safety section. Recommended permissions now auto-approve read-only tools only.
- `OMICALL_TOOLSETS` can now be set from the MCP Registry and Smithery configuration.

### Security
- Hardened the new AI and OmiFlow tools: permission checks matching the web app, rate limits on on/off switches, safer handling of third-party text, and redaction of credentials in AI prompts.
- Stricter input validation across all tools (length limits on every text and list parameter, clear errors for invalid dates).

### Fixed
- Creating and updating tickets, changing ticket status, and adding/editing ticket notes now work reliably. Notes added through the MCP are internal (never shown to the customer). Tickets with pinned items must be edited in the web app.
- Permission checks now load the signed-in user's roles correctly.

### Fixed
- Many call-center, ticket, agent and messaging tools that failed with "not found" now work: hotlines, ring groups, IVR menus, call scripts, audio files, extension lookup, call details and evaluation, ticket statistics and evaluation, contact lookup by phone, employee details, conversation details, and message history for Zalo, Facebook, LiveTalk and more.
- `search_channel_messages` now only needs the conversation ID.
- `list_agents` returns a compact list instead of a very large response.
- Clearer "not found" errors.

### Removed (temporarily)
- Creating ring groups / IVR menus / call scripts / TTS greetings, editing group members, registering webhooks, and bulk ticket/conversation transfer — please use the OmiCall web app for these for now.

### Fixed
- Contact search now correctly filters by created date (the date range was previously ignored).
- Date filters ("today", "on the 19th") now follow Vietnam time; previously ranges were shifted by 7 hours, and the last day of a call/conversation range could be missed.
- Contact search and creation now always use the tenant's default customer list.

### Added
- Filter contacts by person in charge, creator, tag, customer group, business type and customer status — on the server, without loading every contact.
- `count_contacts` — count matching contacts instead of paging through all of them.
- `get_contact_filter_options` — lists the available tags, groups, business types and statuses with their IDs.
- Contact search results now show owner, creator, created date, tags, groups and status directly, so there is no need to open each contact.

### Changed
- Clearer error messages: failed requests now show the server's reason and a hint instead of a generic "Request failed".
- New `OMICALL_TOOLSETS` option to load only the tool groups you need (smaller context, faster responses). Tools for modules not in your service package are hidden automatically after login.
- `OMICALL_ENV` no longer needs to be set.

### Security
- Stronger protection against prompt injection: content written by customers (chat messages, tickets, contacts) is now clearly marked as untrusted data, and the assistant is instructed never to act on instructions found inside it.
- Built-in safety limits for actions with real-world effects: outbound messages (30/min), outbound calls (100 per hour, counted per destination number) and deletions (30/min). Bulk jobs should use the OmiCall web app.
- High-impact tools (auto-call, extension password change, webhook registration, agent invitation, ticket/conversation transfer) are now flagged so MCP clients ask for confirmation before running them.
- Tighter input limits (e.g. max 50 numbers per auto-call request, max 3 retries, page size ≤ 50).
- Broader masking of credentials in tool responses.
- Permission checks now apply consistently for every session, including restored sessions and after switching tenant.
- Saved sessions are tied to the configured account and environment.
- Hardened network handling and updated dependencies to their latest secure versions.
- Published packages now include npm provenance.

## [1.3.1] - 2026-07-13

### Added
- **`get_version` tool** — ask the assistant for the running server version at any time. Useful because `npx` caches builds locally, so the running version can differ from the latest published one.
- Listed on the MCP Registry for easier discovery.
- README: added a **Requirements** section (Node.js 18+) and a **Troubleshooting** section for common install issues.

## [1.3.0] - 2026-07-13

### Fixed
- **Intermittent "Server disconnected" on startup** — on some setups the server could crash right after `npx` install on older Node.js runtimes. The published build now targets a broader runtime baseline so it loads cleanly. **Node.js 18+ is required.**
- **Intermittent disconnect under slow network** — authentication requests had no timeout, so a slow or unresponsive backend could stall the server and make the session appear dropped. All auth calls now time out after 15s.

### Added
- Clear startup error when running on an unsupported Node.js version (instead of a cryptic crash).
- Resilience against unexpected async errors — the server now logs and keeps the session alive rather than exiting silently.
- `get_version` tool — ask the assistant for the running server version at any time.

### Security
- Hardened request routing so tool parameters can no longer be used to reach unintended API paths.
- Ensured error logs and tool responses never expose authentication tokens or other sensitive fields.
- Tightened permissions on the local session file directory.

### Changed
- `list_groups` / `list_ivr` / `list_scripts` / `list_audio` now return clean, formatted summaries instead of raw JSON.

## [1.2.3] - 2026-03-26

### Fixed
- **Session lost on process restart** — auth state is now persisted locally and auto-restored on startup, so you stay logged in across restarts.

## [1.2.1] - 2026-03-20

### Changed
- Renamed the `login` tool to `login_omicall_mcp` for clearer tool discovery.
- Documented auto-approve permissions setup in the README.

## [1.2.0] - 2026-03-20

### Added
- Human-readable formatting for all search/list tools — no more raw JSON parsing.
- Semantic tool annotations (read-only / mutating / destructive) for safer auto-approve.

### Fixed
- Corrected pagination totals and various internal consistency fixes.

## [1.1.2] - 2026-03-20

### Fixed
- `search_calls`: the `sip_numbers` filter no longer overrides `phone_numbers` — both are now applied.

## [1.1.0] - 2026-03-19

### Added
- Tool annotations enabling auto-approve across all 84 tools.
- Role-based access control (RBAC).

## [1.0.0] - 2026-03-15

### Added
- Initial release: 84 tools across 9 groups.
- **Auth**: login (email/password + API key), tenant selection, logout.
- **Call Center**: search/detail/update calls, extensions, hotlines, groups, IVR, scripts, audio.
- **Tickets**: CRUD, notes, evaluation, categories, statistics, transfer.
- **Multi-Channel**: conversations and messaging across 6 channels (Zalo, Facebook, Telegram, LiveTalk).
- **Contacts**: search, CRUD, source categories.
- **Agents**: list, detail, invite, PBX info, notifications.
- **Webhooks**: list, register, destroy.
- **Auto Call**: by phone, by extension.
- **AI**: text-to-speech, speech-to-text webhook registration.
