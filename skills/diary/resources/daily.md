# Daily Diary — Meeting Log

Generate or update a daily diary file with non-all-day meetings from the user's calendar.

## Input

- **Date** (optional): A date in `MM-DD-YYYY` format. If not provided, use the current date.
- **`help`**: If the user's input is the word "help" (case-insensitive), skip all steps and respond with the quick-reference below instead.

## Help

When the user passes `help`, respond with:

```
Daily Diary — Quick Reference

Generates a daily diary file from your calendar.

Usage:
  /diary daily                → today's diary
  /diary daily MM-DD-YYYY     → diary for that date (e.g. 03-09-2026)
  /diary daily help           → this message

What it does:
  1. Fetches non-all-day meetings from your calendar
  2. Creates/updates {diary-path}/{YYYY}/{Month}/{YYYY}-{MM}-{DD}.md
  3. Populates meeting blocks with attendees, tags, and placeholders for notes

Notes:
  - Existing meeting notes and action items are preserved on re-run
  - Cancelled meetings are removed automatically
  - Meetings tagged "hide" are excluded
```

Then stop — do not proceed to the steps below.

## Requirements

**Providers**:
- **calendar** — list and read meetings → `.zettledeck/providers/{calendar-provider}.md`
- **contacts** — attendee name resolution → `.zettledeck/providers/{contacts-provider}.md`

**Tools needed**: Read, Write, Glob

## Steps

1. **Parse the date.** Use the supplied date or default to today's date. Derive the following values:
   - `{YYYY}` — four-digit year (e.g. `2026`)
   - `{MM}` — two-digit month (e.g. `03`)
   - `{DD}` — two-digit day (e.g. `05`)
   - `{MMM}` — full month name (e.g. `March`)
   - `{DayName}` — full day name (e.g. `Thursday`)
   - `{Dth}` — day of month with ordinal suffix (e.g. `5th`, `1st`, `22nd`)
   - `{WW}` — two-digit ISO week number (e.g. `10`)
   - `{timestamp}` — compact timestamp in `YYYYMMDDHHmmss` format using current time

2. **Fetch meetings.** Using the **calendar** provider, retrieve all meetings for the target date. Follow the provider's instructions for listing meetings by date.

3. **Filter meetings.** Exclude any meeting where:
   - `isAllDay` is `true` (all-day meetings)
   - The meeting has a category tag of `"hide"` (case-insensitive). Check the meeting's `categories` array for this value.

4. **Fetch meeting details.** For each non-all-day meeting, use the **calendar** provider's read meeting details operation to retrieve full details including attendees. Follow the provider's instructions for required parameters.

5. **Check for existing diary file.** Look for a file at:
   ```
   {diary-path}/{YYYY}/{MMM}/{YYYY}-{MM}-{DD}.md
   ```
   For example: `{diary-path}/2026/March/2026-03-05.md`

6. **Create or update the diary file.**

   - If the file **does not exist**, create it (and any necessary parent directories) with the format below.
   - If the file **already exists**, update the meetings section while preserving any existing content outside the meetings section (e.g. user notes, action items). Preserve all existing frontmatter fields; only add missing standard fields.

## Diary File Format

```markdown
---
aliases:
  - "Daily {YYYY}-{MM}-{DD}"
creationDate: "{DayName} {Dth} {MMM} {YYYY}"
docType: diary
status: active
subType: daily
tags:
  - filofax
  - daily
timestamp: "{timestamp}"
title: "Daily Diary — {MMM} {DD}, {YYYY}"
---
# Daily Diary — {MMM} {DD}, {YYYY}

> [[{YYYY}.{WW}]]

## Action Items

<!-- Tasks use Obsidian Tasks format per task-management rules -->

## Waiting For

<!-- Waiting-for items use @person: prefix per task-management rules -->

## Meetings

### {Subject} ({Status} | {Response})

- **Time**: {start time} – {end time}
- **Organizer**: {Organizer}          <- omit if solo organizer (see rules)
- **Tags**: {tags}                    <- comma-delimited, each prefixed with #; omit if no categories
- **External Domains**: {domains}     <- only if non-amazon.com domains present (comma-delimited)

#### Attendees                         <- omit if solo organizer (see rules)

- [{Name}]({corporate-directory-url}/{login}) ({email})    <- internal attendees
- {Name} ({email})                                          <- external attendees

#### Meeting Notes

#### Auto Summary                      <- omit if no conference bridge in location

---

(repeat for each meeting, separated by `---`)

## Notes

<!-- Add your notes for the day here -->
```

### Field formatting rules:
- **Weekly cross-link**: The `> [[{YYYY}.{WW}]]` line links to the weekly view file for the ISO week containing this date. `{WW}` is the two-digit ISO week number. This is an Obsidian wikilink.
- **Time**: Display in `h:mm AM` – `h:mm PM` format, derived from the meeting start/end times.
- **Response**: Use the `response` field from the calendar data (e.g. `Accept`, `Tentative`, `Organizer`, `NoResponseReceived`). Display `NoResponseReceived` as `No Response`.
- **Status**: Use the `status` field (e.g. `Busy`, `Free`, `Tentative`).
- **Attendees**: List all attendees from the meeting details (required + optional). To resolve display names, use the **contacts** provider to look up each attendee by their email address. Follow the provider's instructions for lookup and fallback behavior. For internal attendees (matching the corporate email domain), format as a markdown link to the corporate directory: `[{Name}]({corporate-directory-url}/{login}) ({email})` where `{login}` is the email local part. For external attendees, format without a link: `{Name} ({email})`. If no attendees are available, write `- None listed`.
- **Tags**: Display the meeting's categories as a comma-delimited list, each prefixed with `#`. Exclude the `hide` tag. Convert each tag to PascalCase with no spaces for Obsidian hashtag compatibility (e.g. `Acct Team` -> `#AcctTeam`, `!Customer` -> `#!Customer`). Omit this line entirely if the meeting has no categories (or only `hide`).
- **Meeting Notes**: Leave blank — this is for the user to fill in manually.
- **Auto Summary**: Leave blank — this is a placeholder for future automated meeting summary imports.

### Conditional section rules:

1. **Omit Organizer & Attendees when solo organizer.** If the user ({user-email}, configured via email.management rules) is the organizer AND there are no other attendees (the attendee list is empty or contains only the user), omit both the `- **Organizer**: ...` line and the entire `#### Attendees` section for that meeting.

2. **Omit Auto Summary when no conference bridge.** If the meeting location does not contain a conference bridge URL or reference (e.g. Zoom, Teams, Chime, Webex, Google Meet), omit the `#### Auto Summary` section for that meeting. Look for keywords like `zoom`, `teams`, `chime`, `webex`, `meet.google` in the location field (case-insensitive).

3. **External Domains.** For any meeting where an organizer or attendee has an email address with a domain other than the corporate domain, include an `- **External Domains**:` bullet immediately after the `- **Organizer**:` line, with a comma-delimited list of the unique external domains. Format:
   ```
   - **External Domains**: nike.com, idc.com
   ```
   Only include this line if external domains are present.

### Update rules:
- When updating an existing file, replace only the `## Meetings` section content (all meeting blocks between `## Meetings` and `## Notes`). Do not remove or modify the `## Action Items`, `## Waiting For`, `## Notes` sections or any content the user has added within those sections.
- Preserve any user-written content in individual meeting `#### Meeting Notes` sections when updating. Match meetings by subject and time to identify existing entries.
- If a meeting was previously listed but is no longer in the calendar (cancelled), remove its block.
- If new meetings have appeared, add them in chronological order.
- Meetings should be ordered chronologically by start time.

### Task format rules:
- When writing action items to `## Action Items` or waiting-for items to `## Waiting For`, follow standard Obsidian Tasks format.
- Every task must include at minimum a created date signifier (`+` YYYY-MM-DD).
- Waiting-for items use an `@{person}:` prefix in the description.
- Include attribution: `*(from: {meeting subject}, {DayName})*` after the description, before signifiers.

## Output

After creating or updating the file, confirm the file path and summarize how many meetings were logged.
