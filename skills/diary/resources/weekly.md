# Weekly View — Praxis Week at a Glance

Generate or update a weekly view file that consolidates daily diary files into a single filo-fax-style week-at-a-glance surface.

## Invocation

Call this mode when the user requests:
- "Generate this week's view"
- "Update my weekly view for [week/date]"
- "Create week-at-a-glance for [week]"
- `/diary weekly [optional: 2026-W10 or MM-DD-YYYY]`
- `/diary weekly quick` (skip daily file refresh)

## Requirements

**Providers**: **calendar** provider (if refreshing daily files) → `.zettledeck/providers/{calendar-provider}.md`

**Tools needed**: Read, Write, Glob

**Context files**:
- `markdown/task-format.md` - Task format rules
- `{rp-path}/ruthless-priorities.md` - RP registry

## Input Parameters

**Week identifier** (optional):
- ISO week format: `2026-W10`
- Date format: `MM-DD-YYYY` (uses the week containing this date)
- Omitted: defaults to current week

**Mode** (optional):
- `quick` or `weekly-only`: Skip daily file refresh, build from existing files only
- Default: Refresh all weekday daily files before building weekly view

## Workflow

### Step 1: Resolve Target Week

Determine ISO week from input:
- If ISO week provided (`2026-W10`), use directly
- If date provided, determine which ISO week contains that date
- If no input, use current date's ISO week

Derive these values:
- `{YYYY}` - Year of the week's Monday
- `{WW}` - Two-digit ISO week number (e.g. `10`)
- Monday through Sunday dates for the week
- `{MM}` - Numeric month of Monday (no leading zero for single-digit: `3` not `03`)
- `{MMM}` - Full month name for each day (used for daily file paths, may span two months)
- `{DayName}` - Full day name of creation/update date (e.g. `Saturday`)
- `{Dth}` - Day of month with ordinal suffix (e.g. `7th`)
- `{timestamp}` - Compact timestamp in `YYYYMMDDHHmmss` format using current time

### Step 2: Check for Existing Weekly File

Look for file at: `{diary-path}/{YYYY}.{WW}.md`

Example: `{diary-path}/2026.10.md`

**If file exists**, read and extract user content to preserve:

**Frontmatter**: Preserve all existing fields. If using old `alias` (string) format, convert to `aliases` (array). Preserve `creationDate` if exists; only set on first creation.

**Ruthless Priorities section**: Preserve any user-added notes within RP subsections (notes without `*(from: ...)*` attribution)

**Scratchpad section**: Preserve verbatim, always

**Action Items section**: Note manually added items (items without `*(from: ...)*` attribution)

**Waiting For section**: Note manually added items (items without `*(from: ...)*` attribution)

### Step 3: Ensure Daily Files Exist

**Skip this entire step if user specified `quick` or `weekly-only` mode.**

For each **weekday** (Monday-Friday):
- Check if daily diary file exists at `{diary-path}/{YYYY}/{MMM}/{YYYY}-{MM}-{DD}.md`
- If file does NOT exist, invoke the `daily` mode with that date to create it
- If daily creation fails, log error, skip that day, continue with remaining days

For each **weekend day** (Saturday-Sunday):
- Check if daily diary file exists
- If exists, include it
- If not, skip it — **do NOT create weekend daily files automatically**

### Step 4: Extract Data from Daily Files

For each daily file that exists, read and extract:

**Meetings** (from `## Meetings` section):
- Subject - from `### {Subject}` heading (text before any parenthetical)
- Time - from `- **Time**: ...` line
- Tags - from `- **Tags**: ...` line (if present)

**Action Items** (from `## Action Items` section):
- All unchecked items: `- [ ] ...`
- Skip placeholder comments: `<!-- Add action items here -->`

**Waiting For** (from `## Waiting For` section):
- All unchecked items: `- [ ] ...`
- Skip placeholder comments

**Ruthless Priorities**:
- Read `{rp-path}/ruthless-priorities.md` (registry)
- Extract each active RP's title and status
- Read each active RP's individual file from `{rp-path}/`
- Get `Current projects` field values for RP-alignment matching

### Step 5: Build Weekly File

Create or overwrite file at: `{diary-path}/{YYYY}.{WW}.md`

Merge extracted data with preserved user content.

## Weekly File Format

### Frontmatter
```yaml
---
aliases:
  - "weekly {YYYY}.{WW}"
creationDate: "{DayName} {Dth} {Month} {YYYY}"
cssclass:
  - dashboard
  - two-column-list
docType: diary
fullDate: "{YYYY}-{WW}"
month: {MM}
status: active
subType: weekly
tags:
  - filofax
  - weekly
timestamp: "{timestamp}"
title: "Week {WW}: {MON_YYYY}-{MON_MM}-{MON_DD} thru {SUN_YYYY}-{SUN_MM}-{SUN_DD}"
week: {WW}
year: {YYYY}
---
```

**Field Notes**:
- `month`: Numeric month of Monday, no leading zero (e.g. `3` not `03`)
- `week`: ISO week number as integer (e.g. `10`)
- `year`: Four-digit year of Monday
- `fullDate`: Format `{YYYY}-{WW}` (e.g. `2026-10`)

### Navigation
```markdown
<< [[{PREV_YYYY}.{PREV_WW}]] | [[{NEXT_YYYY}.{NEXT_WW}]] >>
```

**Navigation Rules**:
- Use Obsidian wikilinks
- Handle year boundaries: W01 links to last ISO week of prior year; last week links to W01 of next year
- Week numbers always two digits (zero-padded): `[[2026.01]]` not `[[2026.1]]`

### Title
```markdown
# Week {WW}: {MON_YYYY}-{MON_MM}-{MON_DD} thru {SUN_YYYY}-{SUN_MM}-{SUN_DD}
```

### At a Glance Table
```markdown
## At a Glance

| | |
|---|---|
| **Monday** [[{YYYY}-{MM}-{DD}]]<br>{meetings} | **Thursday** [[{YYYY}-{MM}-{DD}]]<br>{meetings} |
| **Tuesday** [[{YYYY}-{MM}-{DD}]]<br>{meetings} | **Friday** [[{YYYY}-{MM}-{DD}]]<br>{meetings} |
| **Wednesday** [[{YYYY}-{MM}-{DD}]]<br>{meetings} | **Sat** [[{YYYY}-{MM}-{DD}]] **· Sun** [[{YYYY}-{MM}-{DD}]]<br>{meetings or "No meetings"} |
```

**Table Rules**:
- Two columns, three rows
- Left column: Mon, Tue, Wed
- Right column: Thu, Fri, Sat+Sun
- When daily file exists: `**Monday** [[{YYYY}-{MM}-{DD}]]` (date is in link)
- When daily file doesn't exist: `**Tuesday {MM}/{DD}**` (include numeric date)
- Meeting times: `h:mm AM` format (start time only for compactness)
- No meetings: Show `No meetings` instead of bullet list
- Saturday/Sunday cell combines both days:
  - With files: `**Sat** [[{YYYY}-{MM}-{DD}]] **· Sun** [[{YYYY}-{MM}-{DD}]]`
  - Without files: `**Sat {MM}/{DD} · Sun {MM}/{DD}**`
  - No meetings: `No meetings`
- Each wikilink uses daily filename without path: `[[{YYYY}-{MM}-{DD}]]`

### Ruthless Priorities Section
```markdown
## Ruthless Priorities

### 1. {RP Title} — {Status}
> [[ruthless-priorities]]
- RP-aligned this week:
  - [ ] {action item} *(from: {meeting}, {Day})*
  - [ ] {action item} *(from: {meeting}, {Day})*

### 2. {RP Title} — {Status}
> [[ruthless-priorities]]
- RP-aligned this week:
  - {items or "No progress this week"}

### 3. {RP Title} — {Status}
> [[ruthless-priorities]]
- RP-aligned this week:
  - {items or "No progress this week"}
```

**RP Section Rules**:
- On initial creation: Auto-populate from registry if exists, otherwise 3 blank slots
- On update: Regenerate RP titles, statuses, and auto-matched items; preserve user notes
- Read registry for active RP titles and statuses
- Read each RP's individual file for current project names (from `Current projects` field)
- Each RP gets subsection with title, status, wikilink to source
- List RP-aligned action items (see alignment rules below)
- No progress: Show `- No progress this week` instead of list
- Only include active RPs (from `## Active` section); skip Parked/Rejected

**How RP-Alignment is Determined**:
1. For each action/waiting item rolled up from daily files
2. Check if item's source meeting subject or item text contains keywords matching:
   - Active RP title
   - Current project names from RP's `Current projects` field
3. If match found, list under that RP in weekly view's RP section
4. Items without matches remain in general Action Items/Waiting For sections
5. Matched items appear in BOTH places (RP section shows alignment, general section is complete list)
6. Keyword-based matching (not semantic) - avoids false positives but may miss connections

### Daily Detail Sections
```markdown
## Monday {MM}/{DD}

> [[{YYYY}-{MM}-{DD}]]

| Time | Meeting | Tags |
|------|---------|------|
| {start} – {end} | {Subject} | {tags} |

---

## Tuesday {MM}/{DD}

> [[{YYYY}-{MM}-{DD}]]

| Time | Meeting | Tags |
|------|---------|------|
| {start} – {end} | {Subject} | {tags} |

---

(repeat for each day that has a daily file)
```

**Daily Section Rules**:
- One section per day that has a daily file
- Heading: `## {DayName} {MM}/{DD}`
- Immediately below: `> [[{YYYY}-{MM}-{DD}]]` (wikilink to daily file)
- Meeting table columns: Time, Meeting, Tags
  - Time: `{start} – {end}` in `h:mm AM – h:mm PM` format
  - Meeting: Subject text
  - Tags: From daily file's `- **Tags**: ...` line (empty if none)
- No meetings: Show `No meetings scheduled` instead of table
- Separate sections with `---` horizontal rules
- Chronological order: Monday through Sunday
- Only include weekend days if daily files exist

### Rolled-Up Action Items
```markdown
## Action Items

- [ ] {action item} *(from: {meeting subject}, {DayName})*
```

**Rollup Rules**:
- Aggregate all unchecked (`- [ ]`) items from daily files' Action Items sections
- Deduplicate semantically - same action phrased differently appears once
- Keep earliest occurrence's attribution
- Preserve full task line including Obsidian Tasks emoji signifiers (+, due date, priority, etc.)
- Do NOT strip or reformat signifiers during rollup
- New tasks follow format from markdown/task-format.md rules
- Legacy tasks (plain checkboxes without signifiers) preserved as-is
- Manually added items (no `*(from: ...)*` attribution) preserved at top

### Rolled-Up Waiting For
```markdown
## Waiting For

- [ ] @{person}: {action item} *(from: {meeting subject}, {DayName})*
```

**Rollup Rules**:
- Aggregate all unchecked (`- [ ]`) items from daily files' Waiting For sections
- Deduplicate semantically, keep earliest attribution
- Preserve full task line including emoji signifiers
- Do NOT strip or reformat signifiers
- New tasks follow task-management format, legacy preserved as-is
- Manually added items preserved at top

### Scratchpad
```markdown
## Scratchpad

```

**Scratchpad Rules**:
- On initial creation: Leave empty
- On update: Preserve all existing content verbatim
- Never modify, append to, or remove scratchpad content

## Update Preservation Summary

| Section | On Update |
|---|---|
| Frontmatter | Regenerate (preserve creationDate) |
| Navigation | Regenerate |
| At a Glance table | Regenerate |
| Ruthless Priorities | Regenerate from source + preserve user notes |
| Daily detail sections | Regenerate |
| Action Items | Merge: preserve manual items, refresh from daily files |
| Waiting For | Merge: preserve manual items, refresh from daily files |
| Scratchpad | Preserve verbatim |

## Output Confirmation

After creating or updating file, confirm:
- Weekly file path
- How many daily files were processed (read)
- How many daily files were created (via daily mode)
- How many action items rolled up
- How many waiting-for items rolled up
- Any days skipped due to errors

## Example Output

```markdown
---
aliases:
  - "weekly 2026.10"
creationDate: "Saturday 7th March 2026"
cssclass:
  - dashboard
  - two-column-list
docType: diary
fullDate: "2026-10"
month: 3
status: active
subType: weekly
tags:
  - filofax
  - weekly
timestamp: "20260307143000"
title: "Week 10: 2026-03-02 thru 2026-03-08"
week: 10
year: 2026
---
<< [[2026.09]] | [[2026.11]] >>
# Week 10: 2026-03-02 thru 2026-03-08

## At a Glance

| | |
|---|---|
| **Monday** [[2026-03-02]]<br>* 10:00 AM Team Sync<br>* 2:00 PM Customer Call | **Thursday** [[2026-03-05]]<br>* 9:00 AM Architecture Review |
| **Tuesday** [[2026-03-03]]<br>* 1:00 PM 1:1 with Manager | **Friday** [[2026-03-06]]<br>* 3:00 PM Sprint Retro |
| **Wednesday** [[2026-03-04]]<br>No meetings | **Sat** [[2026-03-07]] **· Sun** [[2026-03-08]]<br>No meetings |

## Ruthless Priorities

### 1. Customer Migration Platform — On Track
> [[ruthless-priorities]]
- RP-aligned this week:
  - [ ] Review migration architecture with team ⏫ ➕ 2026-03-02 📅 2026-03-05 *(from: Architecture Review, Thursday)*
  - [ ] Complete technical design doc ➕ 2026-03-02 📅 2026-03-06 *(from: Team Sync, Monday)*

### 2. Team Capability Building — At Risk
> [[ruthless-priorities]]
- RP-aligned this week:
  - No progress this week

### 3. Operational Excellence — On Track
> [[ruthless-priorities]]
- RP-aligned this week:
  - [ ] Document sprint retrospective outcomes ➕ 2026-03-06 *(from: Sprint Retro, Friday)*

## Monday 03/02

> [[2026-03-02]]

| Time | Meeting | Tags |
|------|---------|------|
| 10:00 AM – 10:30 AM | Team Sync | team, weekly |
| 2:00 PM – 3:00 PM | Customer Call - Acme Corp | customer, external |

---

## Tuesday 03/03

> [[2026-03-03]]

| Time | Meeting | Tags |
|------|---------|------|
| 1:00 PM – 1:30 PM | 1:1 with Manager | |

---

## Wednesday 03/04

> [[2026-03-04]]

No meetings scheduled

---

## Thursday 03/05

> [[2026-03-05]]

| Time | Meeting | Tags |
|------|---------|------|
| 9:00 AM – 10:00 AM | Architecture Review | architecture, technical |

---

## Friday 03/06

> [[2026-03-06]]

| Time | Meeting | Tags |
|------|---------|------|
| 3:00 PM – 4:00 PM | Sprint Retro | agile, retrospective |

---

## Action Items

- [ ] Review migration architecture with team ⏫ ➕ 2026-03-02 📅 2026-03-05 *(from: Architecture Review, Thursday)*
- [ ] Complete technical design doc ➕ 2026-03-02 📅 2026-03-06 *(from: Team Sync, Monday)*
- [ ] Send follow-up email to Acme with action items ➕ 2026-03-02 📅 2026-03-03 *(from: Customer Call - Acme Corp, Monday)*
- [ ] Document sprint retrospective outcomes ➕ 2026-03-06 *(from: Sprint Retro, Friday)*

## Waiting For

- [ ] @Sarah: Feedback on architecture proposal ➕ 2026-03-05 *(from: Architecture Review, Thursday)*
- [ ] @Manager: Approval for training budget ➕ 2026-03-03 *(from: 1:1 with Manager, Tuesday)*

## Scratchpad

Remember to prepare Q2 planning materials before end of month.
```

## Error Handling

**If calendar provider not available** (and not in quick mode): Inform user that a calendar provider must be configured for daily file refresh.

**If week identifier invalid**: Ask user to provide ISO week (`2026-W10`) or date (`MM-DD-YYYY`) format.

**If daily file creation fails**: Log which day failed, skip that day, continue with remaining days. Report in output confirmation.

**If RP registry doesn't exist**: Create weekly file with 3 blank numbered RP slots.

**If daily files don't exist** (quick mode): Build weekly view from whatever daily files are present. Report missing days in output.

## Boundaries

- Never delete manually added action items or waiting-for items
- Never modify scratchpad content
- Always preserve Obsidian Tasks emoji signifiers during rollup
- Always replace entire daily detail sections (don't merge meetings)
- Preserve user notes within RP subsections (notes without attribution)
- Only auto-create weekday daily files; never auto-create weekend files
