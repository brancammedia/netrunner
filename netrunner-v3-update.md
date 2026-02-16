# Netrunner v3 Update — Project Hierarchy & Workflow Embedding

## Key Change: Three-Tier Hierarchy
Domain → Project → Sub-project

Every sub-project is a persistent entity — never "done." Even fully built tools may need maintenance, updates, automation, or new features.

## Updated Domain Structure

```javascript
const DOMAINS = [
  {
    id: 'portor',
    name: 'Portor Lighting',
    icon: '💡',
    projects: [
      // === SALES TOOLS (Live, built pre-Rook) ===
      { id: 'portor-homepage', name: 'Main HomePage', status: 'live', description: 'Portal landing page', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'pricing-main', name: 'Pricing Main with Inventory', status: 'live', description: 'Primary pricing portal with live inventory', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'pricing-dist', name: 'Pricing - Distribution', status: 'live', description: 'Distribution-specific pricing portal', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'pole-pricing-main', name: 'Pole Pricing Main', status: 'live', description: 'Pole product pricing portal', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'pole-pricing-dist', name: 'Pole Pricing - Distribution', status: 'live', description: 'Distribution pole pricing', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'quick-hub', name: 'Quick Hub', status: 'live', description: 'Quick reference product hub', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'quick-hub-dist', name: 'Quick Hub Distribution', status: 'live', description: 'Distribution quick hub', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'inventory', name: 'Inventory Dashboard', status: 'live', description: 'Live inventory viewer across warehouses', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'clearance', name: 'Clearance Stock', status: 'live', description: 'Clearance and overstock management', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'incoming-orders', name: 'Incoming Orders & Shipments', status: 'live', description: 'Track incoming POs and shipments', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'louisville', name: 'Louisville Replenishment', status: 'live', description: 'Ontario→Louisville transfer management', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'support', name: 'Support', status: 'live', description: 'Support resources portal', agents: [], cronJobs: [], workflows: [], tasks: [], notes: '' },
      { id: 'rep-hubs', name: 'Rep Hubs', status: 'paused', description: 'Rep agency index and hubs', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [], notes: 'Index file built, paused' },
      { id: 'cloud-functions', name: 'Cloud Functions (Backend)', status: 'live', description: 'GCP Cloud Functions powering all tools', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [], notes: 'Never push without Brandon approval' },
      
      // === ACTIVE BUILDS ===
      { id: 'crossing-tool', name: 'Crossing Tool', status: 'building', description: 'Product cross-reference tool — ground-up rebuild', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [{ text: 'Data mining phase planning', done: false, priority: 'high' }, { text: 'AI backend architecture', done: false, priority: 'high' }], notes: 'Needs ground-up re-plan with AI backend' },
      { id: 'gen-guide', name: 'Generation Guide', status: 'testing', description: 'Product generation/lineup guide tool', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [{ text: 'Waiting on Brandon feedback', done: false }], notes: 'In testing' },
      { id: 'homepage-redesign', name: 'Homepage Redesign', status: 'concept', description: 'Modern redesign of portorlighting.com', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [{ text: 'Brandon review concept', done: false }], notes: 'Teal/black concept built, pending review' },
      { id: 'inventory-mgmt', name: 'Inventory Management Tool', status: 'planned', description: 'Flag low stock, prioritize by market importance', agents: ['desktop-rook'], cronJobs: [], workflows: [], tasks: [{ text: 'Build low-stock flagging', done: false, priority: 'high' }], notes: 'High priority' },
      { id: 'power-track', name: 'Power Track Tool', status: 'paused', description: 'Most complex build — power consumption tracking', agents: [], cronJobs: [], workflows: [], tasks: [], notes: 'Parked — most complex build in the portfolio' },
    ]
  },
  {
    id: 'trading-crypto',
    name: 'Trading & Crypto',
    icon: '📈',
    projects: [
      {
        id: 'signal-engine',
        name: 'Signal Engine',
        status: 'live',
        description: 'Headless BTC trading signal system on VPS — 14 strategies, 4 timeframes',
        agents: ['desktop-rook', 'cloud-rook', 'scout'],
        cronJobs: ['Pull Scout Report'],
        workflows: [
          { name: 'Scout Daily Analysis', schedule: 'Daily 8:00 AM', agent: 'cloud-rook', output: 'Trade report + recommendations' },
          { name: 'Pull Scout Report', schedule: 'Daily 8:30 AM', agent: 'desktop-rook', output: 'Local copy + Telegram summary', dependsOn: 'Scout Daily Analysis' },
        ],
        tasks: [
          { text: 'Build regime detection (ADX + EMA slope)', done: false, priority: 'high' },
          { text: 'RSI Divergence confidence bump 66→69', done: true },
          { text: 'Bollinger Squeeze removal', done: true },
        ],
        notes: 'Regime detection queued for Codex review. Scout auto-approval workflow active — Rook reviews, implements, notifies Brandon.'
      },
      {
        id: 'crypto-intel',
        name: 'Crypto Intel System',
        status: 'live',
        description: 'Portfolio price tracking + sentiment analysis for 20 tokens',
        agents: ['desktop-rook'],
        cronJobs: ['Morning Crypto Intel', 'Afternoon Crypto Intel'],
        workflows: [
          { name: 'Morning Intel', schedule: 'Daily 7:00 AM', agent: 'desktop-rook', output: 'Price report + Telegram summary' },
          { name: 'Afternoon Intel', schedule: 'Daily 4:00 PM', agent: 'desktop-rook', output: 'Price report + AM comparison', dependsOn: 'Morning Intel' },
        ],
        tasks: [
          { text: 'Fix xAI sentiment API reliability', done: false, priority: 'med' },
          { text: 'NT CoinGecko ID returning N/A', done: false, priority: 'low' },
        ],
        notes: 'xAI sentiment hanging — running prices-only as fallback'
      },
      {
        id: 'black-nova',
        name: 'BLACK/NOVA Strategy',
        status: 'active',
        description: '3M veBLACK + SuperNova ETH expansion + 40 Acres lending strategy',
        agents: ['desktop-rook'],
        cronJobs: ['Black Hole Voting', 'Elevate & UFO Check'],
        workflows: [
          { name: 'Black Hole Voting', schedule: 'Wed 4:45 PM', agent: 'desktop-rook', output: 'Telegram reminder' },
          { name: 'Elevate/UFO Check', schedule: 'Mon 10:00 AM', agent: 'desktop-rook', output: 'Project launch updates' },
        ],
        tasks: [
          { text: 'Wait for NOVA distribution details', done: false, priority: 'high' },
          { text: 'Monitor 40 Acres NOVA support', done: false },
        ],
        notes: 'Existing loan: ~$30K on 1M veBLACK. $10-12K available on remaining 2M. Amended strategy: borrow against NOVA → LP → DCA into blue chips.'
      },
      {
        id: 'lp-education',
        name: 'LP Education',
        status: 'built',
        description: 'Liquidity provisioning study guide for Blackhole/SuperNova',
        agents: ['desktop-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [{ text: 'Brandon review guide', done: false }],
        notes: 'Study guide at Crypto Home/lp-study-guide.md'
      }
    ]
  },
  {
    id: 'photography',
    name: 'Photography',
    icon: '📸',
    projects: [
      {
        id: 'camarie',
        name: 'Camarie Photography',
        status: 'queued',
        description: 'Email automation + SMS campaigns',
        agents: [],
        cronJobs: [],
        workflows: [],
        tasks: [{ text: 'Plan email automation', done: false }, { text: 'Plan SMS campaigns', done: false }],
        notes: 'Queued — waiting for bandwidth'
      }
    ]
  },
  {
    id: 'rook-ops',
    name: 'Rook Operations',
    icon: '♜',
    projects: [
      {
        id: 'netrunner',
        name: 'Netrunner',
        status: 'building',
        description: 'AI Operations Command Center dashboard',
        agents: ['desktop-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [
          { text: 'Workflows inside project cards', done: false, priority: 'high' },
          { text: 'Live API integration', done: false },
          { text: 'Full Portor tool inventory', done: false },
        ],
        notes: 'v3 building now'
      },
      {
        id: 'llm-cost',
        name: 'LLM Cost Optimization',
        status: 'planned',
        description: 'Research cheaper models for routine tasks',
        agents: ['desktop-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [{ text: 'Benchmark Sonnet vs Opus for sub-agent tasks', done: false }],
        notes: 'High priority — burning credits on Opus'
      },
      {
        id: 'memory-embedding',
        name: 'Memory/Embedding Setup',
        status: 'paused',
        description: 'Configure embedding API key for semantic memory search',
        agents: ['desktop-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [{ text: 'Add OpenAI/Google/Voyage key to auth profiles', done: false }],
        notes: 'Memory search currently disabled — no embedding key'
      },
      {
        id: 'wake-on-lan',
        name: 'Wake-on-LAN Setup',
        status: 'queued',
        description: 'Enable remote PC wake from phone',
        agents: ['desktop-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [
          { text: 'Enable WoL in BIOS', done: false },
          { text: 'Configure Windows network adapter', done: false },
          { text: 'Install phone app', done: false },
        ],
        notes: 'Quick 5-min setup next time at the machine'
      },
      {
        id: 'cloud-rook-maintenance',
        name: 'Cloud Rook Maintenance',
        status: 'active',
        description: 'VPS management, updates, SSH access',
        agents: ['desktop-rook', 'cloud-rook'],
        cronJobs: [],
        workflows: [],
        tasks: [
          { text: 'Update OpenClaw on VPS', done: false, priority: 'high' },
          { text: 'Fix SSH shell access from WSL', done: false },
        ],
        notes: 'SFTP works, shell exec hangs on bash -l. Need to fix or set up SSH keys.'
      }
    ]
  }
];
```

## Updated Status Options
- `live` — 🟢 Built and operational
- `active` — 🔵 Actively being worked on
- `building` — 🔨 Under construction
- `testing` — 🧪 In testing/review
- `concept` — 💡 Concept/design phase
- `planned` — 📋 Planned, not started
- `queued` — ⏳ Waiting for bandwidth
- `paused` — ⏸️ On hold
- `maintenance` — 🔧 Needs maintenance work

## Workflow Display Inside Project Cards

Each project card's workflow section should show:
```
WORKFLOWS
├── ⏰ Scout Daily Analysis
│   Schedule: Daily 8:00 AM CST
│   Agent: Cloud Rook ♜
│   Output: Trade report + recommendations
│   Status: ✅ Last ran Feb 15 8:00 AM
│
├── ⏰ Pull Scout Report
│   Schedule: Daily 8:30 AM CST  
│   Agent: Desktop Rook ♜
│   Output: Local copy + Telegram summary
│   Depends on: Scout Daily Analysis
│   Status: ✅ Last ran Feb 15 8:30 AM
│
└── + Add Workflow
```

If a project has no workflows: show "No workflows configured" with an "+ Add Workflow" button (stretch goal).

## Cross-Cutting Workflows Tab Still Exists
The Workflows tab shows ALL workflows across ALL projects in one unified timeline. But now each workflow is tagged with its project, so you can trace back.
