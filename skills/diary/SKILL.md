---
name: diary
description: Daily diary management — create daily/weekly views, import meeting summaries, close-of-day processing, compile weekly learnings, and promote raw ideas from diary entries into permanent vault notes
---

# Diary

Unified skill for daily diary management. Routes to mode-specific logic based on the requested operation.

## Modes

| Mode | Description | Resource |
|------|-------------|----------|
| `daily` | Generate or update daily diary file from calendar | `resources/daily.md` |
| `weekly` | Consolidate daily files into week-at-a-glance dashboard | `resources/weekly.md` |
| `meeting-summary` | Import meeting summary emails into diary Auto Summary sections | `resources/meeting-summary.md` |
| `close` | End-of-day ritual for processing and filing daily notes | `resources/close.md` |
| `compile` | Synthesize week's work into structured team communications | `resources/compile.md` |
| `promote` | Graduate raw ideas from diary entries into permanent vault notes | `resources/promote.md` |

## Invocation

```
/diary daily                      → today's diary from calendar
/diary daily 03-22-2026           → diary for specific date
/diary weekly                     → this week's view
/diary weekly 2026-W12            → specific ISO week
/diary weekly 03-22-2026          → week containing that date
/diary weekly quick               → skip daily file refresh
/diary meeting-summary            → import summaries for today
/diary meeting-summary 03-22-2026 → import for specific date
/diary close                      → end-of-day processing ritual
/diary compile                    → synthesize weekly learnings for team
/diary promote                    → graduate diary ideas into permanent vault notes
/diary help                       → show this mode table
```

## Shared Dependencies

**Providers** (required by `daily`, `weekly`, `meeting-summary`):
- **calendar** — calendar access → `.zettledeck/providers/{calendar-provider}.md`
- **email** — email search and read → `.zettledeck/providers/{email-provider}.md`
- **contacts** — attendee name resolution → `.zettledeck/providers/{contacts-provider}.md`

**Reference Skills**:
- `markdown` — task format rules (emoji signifiers, attribution, field order)
- `plan` — task operations (where tasks live, archival, RP alignment)
- `email.management` — email classification, user identity ({user-email}, configured via email.management rules)

**Configurable Paths** (set by consuming project):
- `{diary-path}/` — root for diary files (daily and weekly)
- `{rp-path}/` — ruthless priorities registry and individual RP files
- `{actions-path}/` — task dashboard and archive

**Tools needed**: Read, Write, Glob

## Mode Routing

1. If user invokes `/diary` with no argument, display the mode table above as help.
2. Otherwise, identify the requested mode from the first argument.
3. Load the resource file for the requested mode from the `resources/` directory.
4. Pass any remaining arguments (date, week identifier, flags) to the mode logic.

## Help

If user says `/diary` or `/diary help`, respond with:

```
Diary — Available Modes

  daily              Generate/update daily diary from calendar
  weekly             Consolidate week-at-a-glance from daily files
  meeting-summary    Import meeting summary emails into diary
  close              End-of-day processing and filing ritual
  compile            Synthesize weekly learnings for team communications
  promote            Graduate diary ideas into permanent vault notes

Usage:
  /diary <mode> [arguments]

Examples:
  /diary daily
  /diary daily 03-22-2026
  /diary weekly 2026-W12
  /diary weekly quick
  /diary meeting-summary
  /diary close
  /diary compile
  /diary promote

Run /diary <mode> help for mode-specific help.
```
