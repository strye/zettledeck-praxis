# zettledeck-praxis

Daily practice and operational management module for ZettleDeck. Provides diary workflows, email triage, correspondence drafting, ruthless priority tracking, and weekly reshaping tools.

---

## What's Included

### Skills

| Skill | Modes | Description |
|-------|-------|-------------|
| `diary` | `daily`, `weekly`, `meeting-summary`, `close`, `compile` | Generate and maintain daily/weekly diary files from calendar, compile weekly learnings |
| `email` | `triage`, `flagged`, `cleanup`, `draft` | Four-mode email workflow with recommend-first principle |
| `comms` | `process`, `new` | Draft and iterate on professional correspondence using file-based workspace |
| `plan` | `reshape-week`, `review`, `quarterly` | Planning and priority management — weekly restructuring, RP review, quarterly strategic review |

### Scaffolding

```
scaffolding/
├── Praxis/
│   ├── diary/              # Diary directory scaffold (empty, git-tracked)
│   ├── actions/
│   │   └── actions.md      # Task dashboard with Obsidian Tasks queries
│   └── ruthless-priorities/
│       └── ruthless-priorities.md  # RP tracking template
└── Scriptorium/
    └── README.md           # Correspondence workspace
```

---

## Installation

### Via zd (recommended)

```bash
zd install praxis
```

Then run `/zettledeck.init praxis` to configure your vault paths.

### Manual

1. Copy `skills/` into your `.shared/skills/` directory
2. Copy `scaffolding/Praxis/` into your vault root
3. Copy `scaffolding/Scriptorium/` into your vault root
4. Configure paths marked `<!-- CUSTOMIZE -->` in the template files

---

## Configuration

After installation, run:

```
/zettledeck.init praxis
```

This walks you through:
- **Diary path** — where your daily files live (e.g., `Praxis/diary`)
- **Actions path** — where your task dashboard lives (e.g., `Praxis/actions`)
- **Ruthless Priorities path** — where your RP file lives (e.g., `Praxis/ruthless-priorities`)
- **Comms path** — where correspondence files live (e.g., `Scriptorium`)

---

## MCP Dependencies

The `diary` and `email` skills use MCP servers for calendar and email access:

| Server | Used by | Purpose |
|--------|---------|---------|
| `aws-outlook-mcp` | `diary daily`, `diary meeting-summary`, `email.*` | Outlook calendar and email |

These are optional — skills degrade gracefully when MCP servers are unavailable, but full functionality requires them.

---

## Quick Start

```
/diary daily          — Generate today's diary from calendar
/diary weekly         — Consolidate week-at-a-glance
/diary meeting-summary — Import Zoom summaries into diary
/diary close          — End-of-day processing ritual
/diary compile        — Synthesize weekly learnings for team

/email triage         — Classify and action inbox
/email flagged        — Review flagged messages
/email draft          — Draft a reply

/comms new            — Create a new correspondence file
/comms                — Process an existing correspondence file

/plan reshape-week    — Restructure week around what matters
/plan review          — Weekly RP progress check
/plan quarterly       — Quarterly RP review and refresh
```

---

## Part of ZettleDeck

This module is part of the [ZettleDeck](https://github.com/your-org/zettledeck-core) ecosystem. Core functionality (vault conventions, task format, init system) requires `zettledeck-core`.

Other modules: [zettledeck-nexus](../zettledeck-nexus/) · [zettledeck-foundry](../zettledeck-foundry/)
