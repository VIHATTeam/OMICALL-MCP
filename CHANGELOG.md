# Changelog

All notable changes to OmiCall MCP Server are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versioning follows [Semantic Versioning](https://semver.org/).

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
