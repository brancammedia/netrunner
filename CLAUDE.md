# Netrunner — AI Operations Command Center

## What This Is
Netrunner is a local HTML dashboard for managing a multi-agent AI system (OpenClaw). It serves as the human-facing command center for monitoring agents, sessions, scheduled jobs, projects, and system health.

## Build Instructions
Read `netrunner-v2-spec.md` for the full specification. That is the source of truth.

## Key Files
- `netrunner.html` — Current v1 (simple task manager). Will be replaced by v2.
- `netrunner-v2-spec.md` — Full build spec for v2 command center.

## Output
Build the v2 dashboard as a single `index.html` file in this directory. Keep `netrunner.html` as a backup until v2 is confirmed working.

## Constraints
- Single HTML file, all CSS/JS inline
- No external dependencies (Google Fonts optional with fallback)
- Must work from `file://` protocol
- Dark terminal aesthetic — this should look premium
- Well-commented, modular code
- Desktop-first but mobile-responsive

## Who Uses This
Brandon ("BRizzle") — manages multiple AI agents (Desktop Rook, Cloud Rook, Scout) via OpenClaw. This dashboard gives him visibility into everything without needing to use CLI commands.

## Context
- OpenClaw is the AI agent framework running on WSL2 (Desktop) and DigitalOcean VPS (Cloud)
- Agents communicate via Telegram
- Data starts as static JS objects (Rook updates them); future version will pull live from OpenClaw's local API
