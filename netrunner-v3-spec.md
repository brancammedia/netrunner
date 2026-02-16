# Netrunner v3 — AI Operations Command Center

## Build Spec

### Overview
Multi-page single-file HTML application. A strategic operations dashboard that maps the entire Rook ecosystem — agents, workflows, cron jobs, skills, memory, and config — organized around **projects** as the primary mental model. Everything connects to a project. If it doesn't connect to a project, it shows up in system-level views.

This is not a system admin tool. It's a command center for a human who manages multiple AI agents across multiple projects and needs to see the whole picture without context-switching.

### Architecture
- Single HTML file with tab-based navigation (SPA feel)
- Connects to OpenClaw WebSocket API (`ws://localhost:18789`) for live data where possible
- Falls back to embedded static data when API is unavailable
- Editable — inline editing for project status, task management, notes
- Mobile-responsive (desktop-first, but usable on phone)
- Works from `file://` protocol

### Design Language
- Dark terminal aesthetic (#0a0a0f background)
- Teal accent (#00a19a) — matches Portor brand, feels like ops center
- Green (#059669) healthy, Red (#dc2626) critical, Amber (#d97706) warning, Purple (#7c3aed) special
- Data font: JetBrains Mono (Google Fonts, monospace fallback)
- Label font: Outfit (Google Fonts, sans-serif fallback)
- Terminal pane feel — cards with subtle borders, slight depth
- Subtle animations: pulse on live indicators, fade on data refresh
- Header: "NETRUNNER" stylized, live clock, connection status dot

### Navigation
Top bar with tabs:
1. **Overview** — Home dashboard, at-a-glance everything
2. **Projects** — Project-centric view with full depth
3. **Agents** — All agents, their config, status, capabilities
4. **Workflows** — Timeline visualization of all automated workflows
5. **Skills** — Installed/available skills browser
6. **Memory** — Memory vault file browser
7. **Config** — View SOUL.md, AGENTS.md, IDENTITY.md, USER.md, TOOLS.md

Keyboard shortcuts: 1-7 for tabs, R to refresh data, T to toggle theme (dark/light)

---

## Tab Specifications

### 1. OVERVIEW
The home screen. Everything important at a glance.

**Top Stats Bar:**
- Active Agents (count + status dots)
- Cron Jobs Running (enabled count)
- Active Projects (count)
- Memory Files (count)
- OpenClaw Version
- Uptime

**Agent Status Row:**
Three cards side by side:
- Desktop Rook ♜ — status, model, host, version, context usage bar
- Cloud Rook ♜ — status, model, host, version, context usage bar
- Scout ♟️ — status, last run, next run, last report summary

**Today's Timeline:**
Compact horizontal timeline showing what has fired today and what's coming:
```
✅ 7:00 AM Crypto Intel → ⏳ 8:00 AM Scout → ✅ 8:30 AM Pull Report → ⏳ 4:00 PM Afternoon Intel
```
Color-coded: green = completed, amber = pending, red = failed

**Active Projects Quick View:**
Cards for each active project showing:
- Project name + status badge (🟢🔵🟡)
- One-line description
- Agent(s) assigned
- Next action

**Recent Activity Feed:**
Last 10 actions/events:
- "7:00 AM — Crypto Intel ran, sent Telegram summary"
- "8:30 AM — Scout report pulled, 3 recs pending"
- "10:55 AM — RSI Divergence confidence bumped 66→69"
- etc.

**System Health:**
Row of status indicators with colored dots

---

### 2. PROJECTS
The strategic view. Organized by **Domain** (top-level grouping) → **Projects** (individual work streams).

**Domain Structure:**

```
TRADING & CRYPTO
├── Signal Engine — 🟢 Live
│   Agents: Desktop Rook, Cloud Rook, Scout
│   Cron: Scout Analysis (8 AM), Pull Report (8:30 AM)
│   Recent: RSI Div boosted, Bollinger killed
│   Next: Regime detection (Codex review)
│   
├── Crypto Intel — 🟢 Live
│   Agents: Desktop Rook
│   Cron: Morning Intel (7 AM), Afternoon Intel (4 PM)
│   Recent: xAI sentiment API issues
│   Next: Fix sentiment reliability
│
├── BLACK/NOVA Strategy — 📋 Planning
│   Agents: Desktop Rook
│   Cron: Black Hole Voting (Wed 4:45 PM), Elevate/UFO Check (Mon 10 AM)
│   Recent: LP Study Guide built, strategy doc updated
│   Next: Wait for NOVA launch details
│
└── LP Education — 🔵 Built
    Agents: Desktop Rook
    Recent: Study guide completed
    Next: Brandon review

PORTOR LIGHTING
├── Homepage Redesign — 🔵 Concept
│   Agents: Desktop Rook
│   Recent: Teal/black concept built
│   Next: Brandon review
│
├── Inventory Management — 🟡 High Priority
│   Agents: Desktop Rook
│   Next: Build tool to flag low stock
│
├── Product Cross Tool — ⚪ Needs Planning
│   Next: Ground-up re-plan with AI backend
│
├── Power Track Tool — 🧊 Back Burner
│   Most complex build, parked
│
└── Rep Hubs — 🧊 Back Burner
    Index file built, paused

PHOTOGRAPHY
└── Camarie Photography — ⚪ Queued
    Next: Email automation + SMS campaigns

ROOK OPERATIONS
├── Netrunner — 🔵 Building
│   The dashboard you're looking at right now
│
├── LLM Cost Optimization — 🟡 High Priority
│   Research cheaper models for routine tasks
│
└── Memory/Embedding Setup — 🧊 Back Burner
    Configure embedding API key for memory search
```

**Each Project Card (expanded view) shows:**
- Status badge (editable — click to change)
- Domain it belongs to
- Description (editable)
- Assigned agents
- Related cron jobs (linked — click to see job details)
- Related workflows
- Task list (add/complete/delete tasks)
- Notes (editable free-text)
- Last updated timestamp
- History log of changes

**Interactions:**
- Click domain header to collapse/expand
- Click project to expand full detail view
- Drag projects between domains (stretch goal)
- Add new project button per domain
- Status filter: show all / active only / by status

---

### 3. AGENTS
Full profile for each agent.

**Agent Cards (full width, one per agent):**

**Desktop Rook ♜**
- Status: online/offline (live from API)
- Model: claude-opus-4-6
- Host: WIN-79G2697J611 (WSL2)
- OpenClaw Version: 2026.2.14
- Context: usage bar (current tokens / max)
- Active Sessions: list with token counts
- Assigned Projects: linked list
- Assigned Cron Jobs: linked list
- Skills: eligible skills list
- Soul: rendered SOUL.md content
- Identity: rendered IDENTITY.md content
- Config: key settings from AGENTS.md

**Cloud Rook ♜**
- Same layout
- Additional: VPS IP, SSH status, last SFTP connection
- Note: needs OpenClaw update

**Scout ♟️**
- Sub-agent profile
- Schedule: Daily 8 AM CST
- Last Report: summary + link
- Recommendations: pending approvals list
- Performance: cumulative stats from engine

**Interactions:**
- Click "View Soul" to see full SOUL.md
- Click project links to jump to Projects tab
- Click cron links to jump to Workflows tab

---

### 4. WORKFLOWS
The timeline visualization — the centerpiece.

**Daily Workflow Timeline:**
Vertical timeline with time on the left, events on the right:

```
06:00 ─────────────────────────────────
       
07:00 ─── ● Morning Crypto Intel ──────
       │   Agent: Desktop Rook
       │   Output: Telegram summary
       │   Project: Crypto Intel
       ▼
08:00 ─── ● Scout Analysis ────────────
       │   Agent: Cloud Rook (VPS)
       │   Output: Trade report → /root/trading-analyst/
       │   Project: Signal Engine
       ▼
08:30 ─── ● Pull Scout Report ─────────
       │   Agent: Desktop Rook
       │   Input: VPS trade report
       │   Output: Local copy + Telegram summary
       │   Project: Signal Engine
       │   Depends on: Scout Analysis
       ▼
       
... gap ...

16:00 ─── ● Afternoon Crypto Intel ────
       │   Agent: Desktop Rook
       │   Output: Telegram summary (with AM comparison)
       │   Project: Crypto Intel
       ▼
17:00 ─────────────────────────────────
```

**Weekly View:**
7-column grid (Mon-Sun), each day shows its scheduled events:

```
MON         TUE         WED         THU         FRI         SAT         SUN
7 AM Intel  7 AM Intel  7 AM Intel  7 AM Intel  7 AM Intel  7 AM Intel  7 AM Intel
8 AM Scout  8 AM Scout  8 AM Scout  8 AM Scout  8 AM Scout  8 AM Scout  8 AM Scout
8:30 Pull   8:30 Pull   8:30 Pull   8:30 Pull   8:30 Pull   8:30 Pull   8:30 Pull
10 AM UFO                4:45 PM
Check                    BH Vote
4 PM Intel  4 PM Intel  4 PM Intel  4 PM Intel  4 PM Intel  4 PM Intel  4 PM Intel
```

**Dependency Arrows:**
Visual lines connecting dependent workflows:
- Scout Analysis → Pull Scout Report (data dependency)
- Morning Intel → Afternoon Intel (comparison data)

**Each workflow node shows:**
- Time
- Job name
- Agent responsible (color-coded)
- Project it belongs to (linked)
- Status (completed/pending/failed today)
- Last run result
- Click to expand: full job config, history, logs

**Color coding by agent:**
- Desktop Rook: teal
- Cloud Rook: purple
- Scout: green

---

### 5. SKILLS
Browser for all skills.

**Three sections:**
- **Active** — currently eligible and enabled
- **Available** — blocked but installable (show what's missing)
- **All** — full list with search/filter

**Each skill card:**
- Icon + name
- Status badge (eligible/blocked/disabled)
- Description (one line)
- Missing requirements (if blocked)
- Install button (if installable)
- Enable/Disable toggle

**Data source:** OpenClaw API `skills.status`

---

### 6. MEMORY
File browser for the memory system.

**File List:**
- MEMORY.md (main file)
- memory/*.md (individual memory files)
- session-summaries/*.md (session summaries)

**Each file shows:**
- Filename
- Size
- Last modified
- Preview (first 3 lines)

**Click to expand:**
- Full rendered markdown content
- Edit button (stretch goal — inline editing)

**Search bar:**
- Full-text search across all memory files
- Results highlighted with file + line number

---

### 7. CONFIG
View and reference the workspace config files.

**Files displayed:**
- SOUL.md — Who Rook is
- AGENTS.md — How Rook operates  
- IDENTITY.md — Name, creature, vibe
- USER.md — Who Brandon is
- TOOLS.md — Environment-specific notes
- HEARTBEAT.md — Heartbeat tasks

**Each file:**
- Rendered markdown
- "Last modified" timestamp
- Edit button (stretch goal)

---

## Data Architecture

### Live Data (from OpenClaw WebSocket API)
Connect to `ws://localhost:18789` with gateway token.

Fetch on load + poll/subscribe:
- `sessions.list` → agent sessions, context usage
- `cron.list` → all cron jobs, schedules, last run
- `skills.status` → skill availability
- `config.get` → current config
- `status` → gateway health, version

### Static Data (embedded JS objects)
For data the API doesn't have:
- Project definitions, domains, descriptions, status
- Agent-to-project mappings
- Cron-to-project mappings
- Workflow dependencies
- Activity feed entries
- Task lists per project

Structure so Rook can update these objects directly via file edits.

### Hybrid Approach
```javascript
// Static project data (Rook updates this)
const PROJECTS = { ... };

// Live data (fetched from API)
let liveData = {
  sessions: [],    // from sessions.list
  cronJobs: [],    // from cron.list  
  skills: [],      // from skills.status
  config: {},      // from config.get
  health: {}       // from status
};

// Merge: project cards pull cron job status from liveData
// so project views show real-time cron health
```

---

## Embedded Project Data Structure

```javascript
const DOMAINS = [
  {
    id: 'trading-crypto',
    name: 'Trading & Crypto',
    icon: '📈',
    projects: [
      {
        id: 'signal-engine',
        name: 'Signal Engine',
        status: 'live',           // live | in-progress | built | planning | queued | paused
        description: 'Headless BTC trading signal system on VPS',
        agents: ['desktop-rook', 'cloud-rook', 'scout'],
        cronJobs: ['Pull Scout Report'],  // matched by name to live cron data
        tasks: [
          { text: 'Build regime detection', done: false, priority: 'high' },
          { text: 'RSI Div confidence bump', done: true },
          { text: 'Bollinger Squeeze removal', done: true },
        ],
        notes: 'Queued for Codex review. Need regime-aware confidence.',
        lastUpdated: '2026-02-15',
      },
      // ... more projects
    ]
  },
  // ... more domains
];

const AGENTS = [
  {
    id: 'desktop-rook',
    name: 'Desktop Rook',
    icon: '♜',
    model: 'claude-opus-4-6',
    host: 'WIN-79G2697J611 (WSL2)',
    version: '2026.2.14',
    status: 'online',
  },
  {
    id: 'cloud-rook', 
    name: 'Cloud Rook',
    icon: '♜',
    model: 'claude-sonnet-4-20250514',
    host: '138.197.64.68 (DigitalOcean)',
    version: 'unknown',
    status: 'online',
  },
  {
    id: 'scout',
    name: 'Scout', 
    icon: '♟️',
    model: 'claude-sonnet-4-20250514',
    host: 'VPS sub-agent',
    schedule: 'Daily 8 AM CST',
    status: 'idle',
  }
];

const WORKFLOW_DEPENDENCIES = [
  { from: 'Scout Analysis', to: 'Pull Scout Report', type: 'data' },
  { from: 'Morning Crypto Intel', to: 'Afternoon Crypto Intel', type: 'comparison' },
];
```

---

## Technical Requirements
- Single HTML file, all CSS and JS inline
- Google Fonts (JetBrains Mono, Outfit) with graceful fallback
- Works from `file://` protocol
- WebSocket connection to OpenClaw API (optional — degrades gracefully)
- No external dependencies or build step
- Clean, modular JS — each tab has its own render function
- LocalStorage for: connection settings, collapsed states, theme preference
- Mobile-responsive grid (single column on mobile)

## Build Priority
1. Overview + Projects (core value)
2. Workflows (timeline visualization)
3. Agents
4. Config + Memory (simpler views)
5. Skills (mostly API-driven)
6. Editing capabilities
7. Live API integration

## File Location
Output: `/mnt/c/Users/owner/Desktop/Netrunner/netrunner.html`
