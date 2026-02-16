# Netrunner v2 — AI Operations Command Center

## Build Spec for Codex

### Overview
Transform the existing `netrunner.html` task manager into a full AI operations terminal/dashboard. Single HTML file, no dependencies, dark terminal aesthetic. Runs locally from `file://` with live data fetched from APIs where available.

### Design Language
- **Theme:** Dark terminal / cyberpunk ops center. Think Bloomberg terminal meets hacker aesthetic.
- **Colors:** Dark background (#0a0a0f), teal accent (#00a19a), green for healthy (#059669), red for critical (#dc2626), amber for warnings (#d97706), purple for special (#7c3aed)
- **Font:** Monospace for data (JetBrains Mono or Fira Code via Google Fonts, fallback to system monospace). Sans-serif (Arial/Helvetica) for labels.
- **Layout:** CSS Grid, responsive. Card-based modules that feel like terminal panes.
- **Animations:** Subtle — pulse on live indicators, fade on data refresh. Nothing heavy.
- **Header:** "NETRUNNER" in stylized text, subtle glow effect. Timestamp showing current time (live clock).

### Modules / Panes

#### 1. Agent Status Panel
Three agent cards side by side:
- **Desktop Rook** (♜) — status indicator (online/offline), current model, context usage bar (% of 200k), last active timestamp
- **Cloud Rook** (♜) — same layout, VPS IP (138.197.64.68), uptime if available
- **Scout** (♟️) — sub-agent, shows last run time, next scheduled run, last report summary

Each card has a colored status dot: green = active, amber = idle, red = down/error

#### 2. Session Monitor
Table/list showing active sessions:
- Session name/label
- Type (DM, Group, Cron, Sub-agent)
- Model in use
- Context tokens used / max (with progress bar)
- Cost estimate (if available)
- Last message timestamp
- Channel (Telegram, etc.)

Sort by context usage descending (fullest first). Color-code rows: red if >90% context, amber if >70%.

#### 3. Cron Dashboard
List of all scheduled jobs:
- Job name
- Schedule (human readable — "Daily 8 AM CST", "Wed 4:45 PM CST")
- Status (enabled/disabled toggle visual)
- Last run timestamp + result (success/fail)
- Next fire time

#### 4. Project Tracker
Migrate existing Netrunner task sections:
- Active Projects (with status badges: 🟢 Live, 🔵 In Progress, 🟡 Testing, ⚪ Queued)
- High Priority items
- Needs Planning
- Back Burner
- Completed (collapsible)

Each project card shows: name, status, one-line description, last updated date.

#### 5. Memory Vault
File browser for memory system:
- List of memory files (MEMORY.md, memory/*.md)
- File size, last modified date
- Click to expand and view contents (rendered markdown)
- Search bar to search across all memory files

#### 6. System Health
Status indicators for infrastructure:
- Desktop Rook gateway status
- Cloud Rook VPS status  
- gcloud auth status (valid/expired)
- GitHub auth status
- API key health (Anthropic)
- Last deployment timestamp

### Data Architecture
This is a LOCAL HTML file. It won't have direct access to OpenClaw internals. Two approaches:

**Static/Manual Mode (v1):**
- Project data, cron schedules, agent info embedded as JS objects in the file
- Rook (the AI) updates these objects directly when changes happen
- Memory vault reads from embedded snapshots

**Live Mode (v2, future):**
- OpenClaw exposes a local REST API on port 18789
- Netrunner fetches session data, cron status, etc. from `http://localhost:18789/api/...`
- Requires gateway auth token
- Falls back to static data if API unavailable

**For this build: Start with Static/Manual Mode.** Structure the code so live API integration can be added later without rewriting.

### Data Structures (embed in JS)

```javascript
const AGENTS = [
  { id: 'desktop-rook', name: 'Desktop Rook', icon: '♜', status: 'online', model: 'claude-sonnet-4-20250514', contextUsed: 180000, contextMax: 200000, lastActive: '2026-02-13T15:45:00', host: 'WIN-79G2697J611 (WSL2)' },
  { id: 'cloud-rook', name: 'Cloud Rook', icon: '♜', status: 'online', model: 'claude-sonnet-4-20250514', contextUsed: 42000, contextMax: 200000, lastActive: '2026-02-13T15:30:00', host: 'rook-247 (138.197.64.68)' },
  { id: 'scout', name: 'Scout', icon: '♟️', status: 'idle', model: 'claude-sonnet-4-20250514', lastRun: '2026-02-13T08:00:00', nextRun: '2026-02-14T08:00:00' }
];

const CRON_JOBS = [
  { name: 'Scout — Daily Trading Analysis', schedule: 'Daily 8:00 AM CST', enabled: true, lastRun: '2026-02-13T08:00:00', lastResult: 'success', nextFire: '2026-02-14T08:00:00' },
  { name: 'Black Hole Voting Reminder', schedule: 'Wed 4:45 PM CST', enabled: true, lastRun: '2026-02-12T16:45:00', lastResult: 'success', nextFire: '2026-02-19T16:45:00' },
  { name: 'Morning Briefing', schedule: 'Daily 7:00 AM CST', enabled: false, lastRun: '2026-02-12T07:00:00', lastResult: 'success', nextFire: null }
];

const PROJECTS = {
  active: [
    { name: 'Signal & Scalp Trading Bots', status: 'live', desc: 'Live trading system, iterating on v2 changes', updated: '2026-02-13' },
    { name: 'Louisville Replenishment Dashboard', status: 'in-progress', desc: 'Ontario→Louisville transfer management tool', updated: '2026-02-13' },
    // ... etc
  ],
  highPriority: [...],
  needsPlanning: [...],
  queued: [...],
  backBurner: [...],
  completed: [...]
};

const SYSTEM_HEALTH = {
  desktopGateway: { status: 'running', port: 18789 },
  cloudVPS: { status: 'online', ip: '138.197.64.68' },
  gcloudAuth: { status: 'expired', account: 'brandon@portorindustry.com' },
  githubAuth: { status: 'active', method: 'credential-manager' },
  anthropicAPI: { status: 'active' }
};
```

### Interactions
- **Collapsible sections** — click to expand/collapse any module
- **Live clock** in header
- **Refresh button** — for future live mode
- **Theme toggle** — dark (default) / lights-on mode (for daytime)
- **Keyboard shortcuts** — `1-6` to jump to modules, `r` to refresh, `t` to toggle theme

### File Location
Output: `/mnt/c/Users/owner/Desktop/netrunner.html` (replaces existing)

### Technical Constraints
- Single HTML file, all CSS and JS inline
- No external dependencies except Google Fonts (optional, graceful fallback)
- Must work from `file://` protocol
- Mobile-responsive (but desktop-first)
- No build step, no framework
- Clean, well-commented code
- Modular JS — each pane has its own render function for easy updates

### Quality Bar
This is a personal ops dashboard for a power user. It should feel premium — not a bootcamp project. Think:
- Smooth transitions
- Consistent spacing and alignment
- Data-dense but not cluttered
- The kind of dashboard you'd leave open on a second monitor
