---
module: praxis
order: 20
description: Configure diary paths, actions dashboard location, ruthless priorities file path, and comms workspace
---

## Diary Path

**What:** The directory where your daily diary files are stored, organized by year and month.
**File:** `skills/diary/SKILL.md`
**Marker:** `{diary-path}`
**Default:** `Praxis/diary`

### Questions to ask the user

1. Where should your daily diary files be stored?
   - `Praxis/diary` (default — follows the standard Praxis structure)
   - Enter a custom path (e.g., `journal/daily`, `notes/diary`)

### How to apply

Replace `{diary-path}` in `skills/diary/SKILL.md` and all four resource files under `skills/diary/resources/` with the user's chosen path. The diary skill uses this path to locate and create files at `{diary-path}/YYYY/MMM/YYYY-MM-DD_{Day}.md`.

---

## Actions Dashboard Path

**What:** The directory containing your task dashboard (`actions.md`) and archive (`actions-archive.md`).
**File:** `skills/diary/resources/close.md`, `scaffolding/Praxis/actions/actions.md`
**Marker:** `{actions-path}`
**Default:** `Praxis/actions`

### Questions to ask the user

1. Where should your actions dashboard live?
   - `Praxis/actions` (default)
   - Enter a custom path

### How to apply

Replace `{actions-path}` in the diary close resource and actions template with the user's chosen path.

---

## Ruthless Priorities Path

**What:** The file path for your ruthless priorities tracking document.
**File:** `skills/plan/references/rp-framework.md`, `skills/plan/SKILL.md`
**Marker:** `{rp-path}`
**Default:** `Praxis/ruthless-priorities`

### Questions to ask the user

1. Where should your ruthless priorities file live?
   - `Praxis/ruthless-priorities` (default)
   - Enter a custom path

### How to apply

Replace `{rp-path}` in plan/references/rp-framework.md and plan/SKILL.md. The full file path will be `{rp-path}/ruthless-priorities.md`.

---

## Comms Workspace Path

**What:** The directory where draft correspondence files are created and tracked. The comms skill uses file-based workspaces for iterative drafting.
**File:** `skills/comms/SKILL.md`
**Marker:** `{comms-path}`
**Default:** `Scriptorium`

### Questions to ask the user

1. Where should draft correspondence files be stored?
   - `Scriptorium` (default — dedicated correspondence workspace)
   - Enter a custom path

### How to apply

Replace `{comms-path}` in `skills/comms/SKILL.md`. Each draft creates a file at `{comms-path}/{slug}.md` with frontmatter tracking status, channel, and conversation history.

---

## MCP Server Configuration

**What:** The Outlook MCP server name used by diary and email skills.
**Files:** `skills/diary/SKILL.md`, `skills/email/SKILL.md`
**Marker:** `{outlook-mcp}`
**Default:** `aws-outlook-mcp`

### Questions to ask the user

1. What is your Outlook MCP server name?
   - `aws-outlook-mcp` (default — standard ZettleDeck setup)
   - Enter your server name if different

### How to apply

Replace `{outlook-mcp}` in the diary and email SKILL.md files. If the user doesn't have Outlook MCP, the skills will still work in degraded mode — note this for them.
