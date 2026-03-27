# Email — Triage Mode

Classify unread emails, summarize, extract actions, and present recommendations.

## Phase 1 — Retrieve & Classify

**1. Load Ruthless Priorities**

Read `{rp-path}/ruthless-priorities.md` and extract active RP titles and linked file names. If file doesn't exist or no active RPs, skip RP flagging.

**2. Fetch unread emails**

Call `email_inbox` with `unreadOnly: true`. If no unread emails, report "Inbox is clear — nothing to triage" and stop.

**3. Handle large inboxes**

If more than 20 unread emails, process in batches of 20. After presenting each batch, ask user if they want to continue.

**4. Read full threads where needed**

For emails where inbox preview is truncated or unclear, call `email_read` with `conversationId` to get full thread.

**5. Classify each email**

Into exactly one category per the Shared Context in SKILL.md:

| Category | Signal |
|----------|--------|
| 🔴 **Action Required** | Direct question to user; request for deliverable; mentions user by name with request; meeting action item assigned to user; sensitivity keywords |
| 🟡 **Read & Review** | Status update from key stakeholder; shared document/report/announcement; informational but worth reading |
| 🟢 **FYI** | Newsletter; automated notification; calendar confirmation; marketing/promotional; CC-only with no direct mention (unless from leadership) |

**User identity matching** (from Shared Context):
- `{user-email}`, username portion of `{user-email}`, user's first name, last name, and full name

**Sensitivity keywords** (always 🔴 Action Required):
- legal, HR, confidential, PIP, investigation, compliance, attorney, escalation

**6. RP flagging**

If email's subject, sender, or body matches active RP title or related keywords/account names, flag it with RP name.

**7. Ambiguity default**

If classification unclear, default to 🟡 Read & Review and flag for user decision.

## Phase 2 — Summarize & Extract

**1. Summarize each email**

Generate one-to-two sentence summary capturing key point and any action implied.

**2. Thread summarization**

For emails with 3+ messages in thread (determined by reading full conversation via `email_read`), generate thread summary per format in Shared Context.

**3. Extract actions from 🔴 emails**

For each Action Required email, identify:
- **Action items** owned by user: what needs to be done, any deadline mentioned
- **Waiting-for items** owned by someone else: what user is waiting on, from whom

## Phase 3 — Present & Act

**1. Present the triage:**

```
## 📬 Email Triage — {date}
{count} unread emails classified.

### 🔴 Action Required ({count})

**1. {subject}** — from {sender}, {relative time}
   Summary: {1-2 sentence summary}
   🎯 RP: {RP name, if applicable}
   📋 Actions:
     - {action description} (owner: you, deadline: {date or "none stated"})
     - ⏳ {waiting-for description} (owner: {person})
   ✉️ Want me to draft a reply?

### 🟡 Read & Review ({count})

**1. {subject}** — from {sender}, {relative time}
   Summary: {1-2 sentence summary}

### 🟢 FYI ({count})

**1. {subject}** — from {sender}, {relative time}
   Summary: {1-2 sentence summary}

---
**Recommendations:**
- Write {n} action items to today's diary?
- Write {n} waiting-for items to today's diary?
- Archive {n} FYI emails?
- Any reclassifications?
```

**2. Wait for user review**

Accept any of:
- Approval ("looks good", "go ahead", etc.)
- Reclassification ("move #3 to Action Required", etc.)
- Partial approval ("create the action items but don't archive yet")
- Draft request ("draft a reply to #2") — switch to Draft Response mode
- Rejection ("skip this one", "I'll handle it manually")

**3. On approval, execute:**

**Action items → diary file**

Write approved action items to today's diary file under `## Action Items` using format from task-management rules:
```
- [ ] {description} *(from: email — {email subject}, {YYYY-MM-DD})* {priority} ➕ {today} {📅 deadline if stated}
```

If today's diary file doesn't exist, write to `{actions-path}/actions.md` under `## Inbox` instead.

**Waiting-for items → diary file**

Write approved waiting-for items under `## Waiting For`:
```
- [ ] @{person}: {description} *(from: email — {email subject}, {YYYY-MM-DD})* ➕ {today} {📅 deadline if stated}
```

**Archive FYI emails**

Call `email_move` with `targetFolder: "archive"` for each approved FYI email's `conversationId`.

**Mark triaged emails as read**

Call `email_read` with `markAs: "read"` for all triaged emails.

## Priority Assignment for Extracted Tasks

| Signal | Priority |
|--------|----------|
| Explicit deadline within 2 days, or sender is leadership | `⏫` High |
| Deadline within a week, or from key stakeholder | `🔼` Medium |
| No deadline, general request | (none) Normal |
| Low-urgency, nice-to-have | `🔽` Low |

## Example Triage Output

```
## 📬 Email Triage — March 8, 2026
12 unread emails classified.

### 🔴 Action Required (3)

**1. Q2 Planning Materials Needed** — from Sarah Johnson (Manager), 2 hours ago
   Summary: Sarah is requesting draft Q2 planning materials and timeline for migration platform initiative by EOD Friday March 15.
   🎯 RP: Customer Migration Platform
   📋 Actions:
     - Draft Q2 planning materials for migration platform (owner: you, deadline: March 15)
   ✉️ Want me to draft a reply?

**2. Customer escalation - Acme Corp billing** — from Jane Smith (CSM), 4 hours ago
   Summary: Acme Corp (XXL) escalated billing issue affecting $2.2M contract. Need technical review and response by Monday.
   🎯 RP: Customer Migration Platform
   📋 Actions:
     - Review Acme Corp billing issue and provide technical response (owner: you, deadline: March 11)
     - ⏳ Jane to schedule call with Acme billing team (owner: Jane)
   ✉️ Want me to draft a reply?

**3. [CONFIDENTIAL] HR Performance Review** — from HR Partner, 1 day ago
   Summary: Request to schedule performance review discussion for Q1.
   📋 Actions:
     - Schedule Q1 performance review with HR (owner: you, deadline: none stated)
   ✉️ Want me to draft a reply?

### 🟡 Read & Review (5)

**1. Architecture Review Outcomes** — from Alex Chen, 5 hours ago
   Summary: Alex shared outcomes from yesterday's architecture review including 3 technical decisions and next steps.

**2. Weekly Team Newsletter** — from Team Lead, 1 day ago
   Summary: Team updates, shoutouts, and upcoming events for the week of March 9.

**3. Doc Share: Sprint Retrospective Notes** — from Jordan Lee, 1 day ago
   Summary: Jordan shared retrospective notes from Friday's sprint retro with action items for next sprint.

**4. AWS Service Announcement** — from AWS Announcements, 2 days ago
   Summary: New feature launch for EC2 that may be relevant for customer migrations.

**5. Shared Presentation - Customer Success Metrics** — from Leadership, 3 days ago
   Summary: Quarterly customer success metrics presentation shared by VP.

### 🟢 FYI (4)

**1. Zoom Meeting Summary: Team Sync** — from Zoom, 3 hours ago
   Summary: Auto-generated summary from this morning's team sync meeting.

**2. Calendar: Accepted - Architecture Review** — from Outlook, 1 day ago
   Summary: Confirmation of acceptance for Thursday's architecture review meeting.

**3. AWS Newsletter: March Edition** — from AWS Marketing, 2 days ago
   Summary: Monthly newsletter with AWS updates, case studies, and events.

**4. LinkedIn: You appeared in 12 searches** — from LinkedIn, 3 days ago
   Summary: Weekly LinkedIn activity notification.

---
**Recommendations:**
- Write 4 action items to today's diary?
- Write 1 waiting-for item to today's diary?
- Archive 4 FYI emails?
- Any reclassifications?
```
