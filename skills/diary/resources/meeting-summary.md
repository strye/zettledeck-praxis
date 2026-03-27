# Meeting Summaries — Import from Email

Import meeting summaries from email (e.g. Zoom meeting summary emails) into the corresponding daily diary file's `#### Auto Summary` sections, then extract action items, waiting-for items, and callouts.

## Invocation

Call this mode when the user requests:
- "Import meeting summaries for today"
- "Import Zoom summaries into diary"
- "Update meeting summaries for [date]"
- `/diary meeting-summary [optional: MM-DD-YYYY or file path]`

## Requirements

**MCP Server**: `aws-outlook-mcp` with email search and read access

**Tools needed**: Read, Write

**Context files**: `markdown/task-format.md`, `email.management` rules

## Input Parameters

**Date or file path** (optional):
- Date format: `MM-DD-YYYY`
- File path: `{diary-path}/2026/March/2026-03-06.md`
- Omitted: defaults to current date

**`help`** (optional):
- If user's input is the word "help" (case-insensitive), skip all steps and respond with quick-reference below instead

## Help

When the user passes `help`, respond with:

```
Meeting Summaries — Quick Reference

Imports meeting summary emails (e.g. Zoom) into your daily diary's Auto Summary sections.

Usage:
  /diary meeting-summary                              → import summaries for today
  /diary meeting-summary MM-DD-YYYY                   → import for that date
  /diary meeting-summary {diary-path}/.../YYYY-MM-DD.md → import for that specific file
  /diary meeting-summary help                          → this message

What it does:
  1. Reads the diary file and finds meetings with empty Auto Summary sections
  2. Searches email for matching "Meeting summary: {subject}" messages
  3. Inserts the summary content into the diary
  4. Extracts action items, waiting-for items, and callouts to diary sections

Notes:
  - Idempotent — re-running won't duplicate already-imported summaries
  - Creates the diary file first if it doesn't exist
  - Only fills empty Auto Summary sections; existing content is never overwritten
```

Then stop — do not proceed to the steps below.

## Workflow

### Step 1: Resolve Diary File

**If file path provided**: Use directly

**If date provided (or defaulting to today)**: Derive file path:
```
{diary-path}/{YYYY}/{MMM}/{YYYY}-{MM}-{DD}.md
```
Where:
- `{YYYY}` - Four-digit year
- `{MMM}` - Full month name (e.g. `March`)
- `{MM}` - Two-digit month
- `{DD}` - Two-digit day

### Step 2: Ensure Diary File Exists

If file does NOT exist, invoke the `daily` mode for the resolved date to create it.

### Step 3: Parse Meetings from Diary File

Read diary file and identify meeting blocks:
- Meeting blocks delimited by `### {Subject}` headings and `---` separators
- For each meeting, check if it has `#### Auto Summary` section
- **Skip meetings without** `#### Auto Summary` section
- **Skip meetings with populated** `#### Auto Summary` (already has content)

This ensures **idempotency** - re-running only fills empty summaries, never overwrites existing ones.

### Step 4: Search for Meeting Summary Emails

For each meeting with empty `#### Auto Summary`, search for summary emails using two strategies:

**Strategy A — Internal auto-summaries (Amazon Meetings Summary / Zoom)**:

Use `email_search` from aws-outlook-mcp to search for email with subject:
```
Meeting summary: {meeting subject}
```

Where `{meeting subject}` is the meeting title from diary (text in `###` heading before any parenthetical).

**Search parameters**:
- Date range: diary date +/- 2 days (accounts for timezone, delays, weekends)

**Strategy B — External organizer recaps**:

For meetings organized by someone outside the corporate domain (identified by **External Domains** in the diary entry or a non-corporate-domain organizer email), also search for recap emails sent by the meeting organizer with the meeting subject in the email subject line.

These are manual recaps sent by the external organizer (e.g. a customer) after the meeting. They typically contain a summary, next steps, and action items — and are a valid summary source even though they don't follow the "Meeting summary:" subject pattern.

**Search parameters**:
- Query: meeting subject
- Date range: diary date +/- 1 day
- From: meeting organizer email (if external domain)

**Matching validation — CRITICAL**:

When a candidate summary email is found, **read the full email** and verify that the meeting date and time stated in the email body match the diary entry's date and time.

**Why this matters**:
- Many recurring meetings share the same subject line
- A summary email whose body says "Mon, March 9 | 03:30 PM" does NOT match a diary entry for Tuesday March 10 at 3:30 PM, even if subject lines are identical

**Validation steps**:
1. Read full email body
2. Look for date/time header (e.g. from Amazon Meetings Summary or Zoom invite block)
3. Compare against diary entry's `**Time**` field and diary file's date
4. Both the date and approximate time must match
5. If email body has no explicit date/time, use email delivery timestamp as proxy (should fall on or shortly after diary date)

**Never match a summary to a meeting based on subject alone**. Subject + date/time confirmation is required.

### Step 5: Extract Meeting Summary

**If matching email found**:

Use `email_read` from aws-outlook-mcp with `format: "markdown"` to retrieve email body.

**Extract only summary content**:

For Zoom meeting summaries:
- Include: Summary text, action items, key discussion points
- Exclude: Email headers, footers, signatures, boilerplate
- Remove: "This is an AI-generated summary", Zoom branding, unsubscribe links

For external organizer recaps:
- Include: Recap/summary section and next steps
- Exclude: Email signatures, CAUTION banners, Zoom meeting invite blocks

### Step 6: Update Diary File

For each meeting where summary was found, insert extracted content into `#### Auto Summary` section.

**CRITICAL: Safe Update Method**

Only modify the specific `#### Auto Summary` section for the meeting being updated.

1. **Identify exact meeting block** by its `### {Subject}` heading
2. **Within that block only**, locate empty `#### Auto Summary` section
3. **Insert summary content** on blank lines immediately after `#### Auto Summary` heading
4. **Insertion point**: The whitespace between `#### Auto Summary` and whatever comes next
5. **Do NOT match, consume, or modify** anything beyond the empty lines

**What comes after could be**:
- `---` separator
- Another heading (`###`, `####`)
- `## Notes` section
- Any user-added content

**Treat everything after blank lines as untouchable**. Only replace the empty/whitespace gap - never the surrounding structure.

**Never delete or modify**:
- Other meeting blocks
- Headings
- Attendees lists
- Meeting notes
- Any content outside specific `#### Auto Summary` being updated

**If no summary email found**: Leave `#### Auto Summary` section completely untouched.

### Step 7: Extract Action Items, Waiting-For, and Callouts

After importing summaries, review ALL `#### Auto Summary` sections (newly imported AND previously existing).

Extract ALL action items - not just user's items.

**Classify by owner**:

**User action items**: Items assigned to or involving the user
- Match user identity per email.management rules ({user-email}, configured via email.management rules)
- Add to `## Action Items` section
- Format: `- [ ] {action item} *(from: {meeting subject})*`
- Follow task format from markdown/task-format.md rules

**Waiting For items**: Items assigned to someone else
- These are commitments the user needs to track
- Add to `## Waiting For` section (create after `## Action Items` if doesn't exist)
- Format: `- [ ] @{person first name}: {action item} *(from: {meeting subject})*`

**Callouts**: User mentioned in non-actionable way
- Examples: "Will presented the findings", "Strye's team will be impacted"
- Add as bullet under `## Notes` section
- Format: `- {callout} *(from: {meeting subject})*`

**Idempotency Check**:
- Before adding item, check if semantically similar item already exists in target section
- Do NOT add duplicates
- Compare meaning, not exact strings
- Example: "Nazlee to finish deck by Monday" = "Nazlee to complete first pass on deck Monday morning"

**Preserve existing content**:
- Append new items after any existing user-written content
- Do not remove or modify existing items

**Strip placeholder comments**:
- If `## Action Items` contains only `- [ ] <!-- Add action items here -->`, remove placeholder before appending

### Step 8: Mark Summary Emails as Read

After successfully importing summaries into the diary, mark all matched summary emails as read:
- Use `email_read` with `markAs: "read"` for each email's `conversationId`
- This applies to both internal auto-summaries (Strategy A) and external organizer recaps (Strategy B)
- Only mark emails that were actually used to populate an `#### Auto Summary` section
- Do NOT mark emails that were found but failed date/time validation

## Output Confirmation

After processing, confirm:
- Which meetings had summaries imported (newly filled this run)
- Which meetings were skipped (already had summary)
- Which meetings had no matching email found (still empty)
- How many user action items extracted
- How many waiting-for items extracted
- How many callouts extracted
- Diary file path updated

## Example Before/After

### Before (Empty Auto Summary)
```markdown
### Team Planning Session

- **Time:** 2:00 PM - 3:00 PM
- **Organizer:** Sarah Johnson
- **Attendees:**
  - [Will Strye]({corporate-directory-url}/stryew)
  - [Alex Chen]({corporate-directory-url}/alexchen)

**Meeting Notes:**

Team planning for Q2 initiatives.

#### Auto Summary

---
```

### After (Imported Summary)
```markdown
### Team Planning Session

- **Time:** 2:00 PM - 3:00 PM
- **Organizer:** Sarah Johnson
- **Attendees:**
  - [Will Strye]({corporate-directory-url}/stryew)
  - [Alex Chen]({corporate-directory-url}/alexchen)

**Meeting Notes:**

Team planning for Q2 initiatives.

#### Auto Summary

**Key Discussion Points:**
- Reviewed Q1 outcomes and lessons learned
- Prioritized top 3 initiatives for Q2
- Discussed resource constraints and mitigation strategies

**Decisions Made:**
- Will to lead customer migration platform initiative
- Alex to focus on operational excellence improvements
- Weekly checkpoint meetings every Friday at 10 AM

**Action Items:**
- Will: Draft technical design doc for migration platform by March 15
- Alex: Create operational metrics dashboard by March 20
- Sarah: Schedule stakeholder review meeting for March 22

**Next Steps:**
- Reconvene in 2 weeks to review progress
- Share draft documents in team Slack channel

---
```

### Extracted to Action Items Section
```markdown
## Action Items

- [ ] Draft technical design doc for migration platform ⏫ ➕ 2026-03-08 📅 2026-03-15 *(from: Team Planning Session)*
```

### Extracted to Waiting For Section
```markdown
## Waiting For

- [ ] @Alex: Create operational metrics dashboard ➕ 2026-03-08 📅 2026-03-20 *(from: Team Planning Session)*
- [ ] @Sarah: Schedule stakeholder review meeting ➕ 2026-03-08 📅 2026-03-22 *(from: Team Planning Session)*
```

### Extracted to Notes Section
```markdown
## Notes

- Will to lead customer migration platform initiative *(from: Team Planning Session)*
```

## Error Handling

**If aws-outlook-mcp not available**: Inform user that MCP server must be configured.

**If diary file doesn't exist**: Invoke the `daily` mode to create it first.

**If date format invalid**: Ask user for date in `MM-DD-YYYY` format or valid file path.

**If email search fails**: Report which meetings couldn't be searched, continue with others.

**If email body empty or malformed**: Skip that meeting's summary import, log issue, continue.

**If meeting subject ambiguous**: Use exact match from diary heading. Log if multiple emails match.

## Boundaries

- Never overwrite existing Auto Summary content (idempotency)
- Never modify meeting blocks beyond Auto Summary section
- Never delete user-added content in Action Items, Waiting For, or Notes
- Always preserve existing task formatting and emoji signifiers
- Only add action items that don't already exist (semantic matching)
- Never modify meeting structure (headings, separators, attendees)
- Never remove placeholder comments from sections with other content

## Implementation Notes

**Idempotency is critical**: This skill must be safe to run multiple times on the same diary file without creating duplicates or overwriting user edits.

**Surgical precision**: When updating Auto Summary sections, use targeted string replacements that match the exact empty block context. Never use broad replacements that could affect adjacent meetings.

**Semantic deduplication**: When extracting action items, compare meaning rather than exact strings to avoid duplicate entries across multiple summary imports.

**Attribution preservation**: Always include `*(from: {meeting subject})*` attribution so users can trace items back to their source.
