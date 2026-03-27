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
  "rpPath": "Praxis/ruthless-priorities",
  "commsPath": "Scriptorium",
  "outlookMcp": "aws-outlook-mcp"
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
2. Replace `{diary-path}` in `skills/diary/SKILL.md` and all resource files under `skills/diary/resources/`
3. Also replace in any nexus skills that share this placeholder (`skills/insight/`, `skills/reflection/`, `skills/ideas/`)

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
2. Replace `{actions-path}` in diary, email, and insight skill files

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
2. Replace `{rp-path}` in plan, email, insight, and ideas skill files
3. The full file path will be `{rpPath}/ruthless-priorities.md`

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
2. Replace `{comms-path}` in `skills/comms/SKILL.md`

---

## MCP Server Configuration

**What:** The Outlook MCP server name used by diary and email skills.
**Config key:** `outlookMcp`
**Default:** `aws-outlook-mcp`

### Questions to ask the user

1. What is your Outlook MCP server name?
   - `aws-outlook-mcp` (default — standard ZettleDeck setup)
   - Enter your server name if different

### How to apply

1. Write the user's choice to `outlookMcp` in `.zettledeck/zettledeck-praxis/config.json`
2. Replace `{outlook-mcp}` in the diary and email SKILL.md files (if placeholder exists)
3. If the user doesn't have Outlook MCP, note that skills will work in degraded mode
