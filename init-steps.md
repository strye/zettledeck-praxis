---
module: praxis
order: 20
description: Configure diary paths, actions dashboard location, ruthless priorities file path, and comms workspace
---

## Configuration Convention

All praxis configuration is stored in `.zettledeck/zettledeck-praxis/config.json`. This file is the single source of truth for runtime paths used by diary, plan, email, comms, and related skills.

After walking through each section below, write the final config file with all resolved values.

### Config file format

**File:** `.zettledeck/zettledeck-praxis/config.json`

```json
{
  "diaryPath": "Praxis/diary",
  "actionsPath": "Praxis/actions",
  "taskArchiveThreshold": 14,
  "rpPath": "Praxis/ruthless-priorities",
  "commsPath": "Scriptorium",
  "providers": {
    "calendar": "aws-outlook-calendar",
    "email": "aws-outlook-email",
    "contacts": "aws-outlook-contacts"
  }
}
```

---

## Diary Path

**What:** The directory where your daily diary files are stored, organized by year and month.
**Config key:** `diaryPath`
**Default:** `Praxis/diary`

### Questions to ask the user

1. Where should your daily diary files be stored?
   - `Praxis/diary` (default — follows the standard Praxis structure)
   - Enter a custom path (e.g., `journal/daily`, `notes/diary`)

### How to apply

1. Write the user's choice to `diaryPath` in `.zettledeck/zettledeck-praxis/config.json`

---

## Actions Dashboard Path

**What:** The directory containing your task dashboard (`actions.md`) and archive (`actions-archive.md`).
**Config key:** `actionsPath`
**Default:** `Praxis/actions`

### Questions to ask the user

1. Where should your actions dashboard live?
   - `Praxis/actions` (default)
   - Enter a custom path

### How to apply

1. Write the user's choice to `actionsPath` in `.zettledeck/zettledeck-praxis/config.json`

---

## Task Archive Threshold

**What:** How many days after completion a task must be before it's moved to the archive file.
**Config key:** `taskArchiveThreshold`
**Default:** `14`

### Questions to ask the user

1. How many days after completion should tasks be archived?
   - `14` days (default)
   - Enter a custom number if preferred

### How to apply

1. Write the user's choice to `taskArchiveThreshold` in `.zettledeck/zettledeck-praxis/config.json`

---

## Ruthless Priorities Path

**What:** The file path for your ruthless priorities tracking document.
**Config key:** `rpPath`
**Default:** `Praxis/ruthless-priorities`

### Questions to ask the user

1. Where should your ruthless priorities file live?
   - `Praxis/ruthless-priorities` (default)
   - Enter a custom path

### How to apply

1. Write the user's choice to `rpPath` in `.zettledeck/zettledeck-praxis/config.json`
2. The full file path will be `{rpPath}/ruthless-priorities.md`

---

## Comms Workspace Path

**What:** The directory where draft correspondence files are created and tracked.
**Config key:** `commsPath`
**Default:** `Scriptorium`

### Questions to ask the user

1. Where should draft correspondence files be stored?
   - `Scriptorium` (default — dedicated correspondence workspace)
   - Enter a custom path

### How to apply

1. Write the user's choice to `commsPath` in `.zettledeck/zettledeck-praxis/config.json`

---

## Providers

**What:** External service providers for calendar, email, and contacts used by diary, email, and comms skills. Providers are instruction files in `.zettledeck/providers/` that tell skills how to interact with specific MCP servers.

**Config key:** `providers`

### Questions to ask the user

For each capability, scan `.zettledeck/providers/` for files with matching `capability` frontmatter and present available options:

1. **Calendar provider** — Which calendar provider should diary skills use?
   - List available providers with `capability: calendar` (e.g., `aws-outlook-calendar`, `gmail-calendar`)
   - If only one exists, confirm it as the default
   - If none exist, note that calendar-dependent features will run in degraded mode

2. **Email provider** — Which email provider should email skills use?
   - List available providers with `capability: email` (e.g., `aws-outlook-email`, `gmail-email`)
   - Same selection logic as calendar

3. **Contacts provider** — Which contacts provider should diary skills use for attendee lookup?
   - List available providers with `capability: contacts` (e.g., `aws-outlook-contacts`, `gmail-contacts`)
   - Same selection logic as calendar

### How to apply

1. Write selections to `providers` object in `.zettledeck/zettledeck-praxis/config.json`:
   ```json
   "providers": {
     "calendar": "aws-outlook-calendar",
     "email": "aws-outlook-email",
     "contacts": "aws-outlook-contacts"
   }
   ```
2. If user has no providers for a capability, note which skills will run in degraded mode
