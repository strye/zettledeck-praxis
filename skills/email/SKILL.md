---
name: email
description: Email workflows — triage, flagged review, cleanup sweep, and draft assistance with recommend-first principle. Includes shared email management rules for classification, sensitivity, and thread handling.
---

# Email — Consolidated Skill

Unified email skill with four modes: triage, flagged review, cleanup, and draft assistance. All follow the recommend-first principle.

## Invocation

| User says | Mode |
|-----------|------|
| `/email triage`, "triage my inbox", "check emails", "process inbox" | Triage |
| `/email flagged`, "review flagged emails", "check flags" | Flagged Review |
| `/email cleanup`, "clean up inbox", "archive old emails", "sweep inbox" | Cleanup Sweep |
| `/email draft`, "draft a reply to...", "respond to..." | Draft Response |
| `/email` (no argument) | Show available modes |

If the user invokes `/email` with no argument or the mode is unclear, respond with:

```
Available email modes:
- **triage** — Classify unread emails, summarize, extract actions
- **flagged** — Review flagged emails, assess staleness, recommend next steps
- **cleanup** — Batch archive or delete old processed emails
- **draft** — Draft an email reply for review and approval

Usage: `/email <mode>`
```

## Mode Routing

Each mode is defined in a resource file:

| Mode | Resource |
|------|----------|
| `triage` | `resources/triage.md` |
| `flagged` | `resources/flagged.md` |
| `cleanup` | `resources/cleanup.md` |
| `draft` | `resources/draft.md` |

Load the appropriate resource file based on detected mode, then follow its workflow exactly.

## Requirements

**Providers**: **email** — email access → `.zettledeck/providers/{email-provider}.md`

**Tools needed**: Read, Write

**Context files**:
- `.shared/skills/markdown/task-format.md` — Task format rules
- `{rp-path}/ruthless-priorities.md` — RP context

## Core Principle: Recommend-First

Every action follows this pattern:
```
Assistant recommends → User reviews → User approves/adjusts → Assistant executes
```

**Never send, move, delete, or archive email without explicit user approval. Never create tasks without explicit user approval.**

---

## Shared Context: Email Management Rules

The following rules apply to ALL email modes. Individual mode resources handle workflow-specific logic; this section defines the conventions they share.

### User Identity

When matching emails to the user (e.g. extracting action items, detecting direct mentions), match any of:
- `{user-email}` (configured per workspace)
- Username portion of `{user-email}`
- User's first name, last name, and full name

### Classification Categories

When classifying emails, use exactly one of these three categories:

| Category | Label | Use When |
|----------|-------|----------|
| 🔴 Action Required | Needs user response, decision, or task | Direct question to user; request for deliverable; mentions user by name with a request; meeting action item; sensitive keywords |
| 🟡 Read & Review | Worth reading, no immediate action | Status update from key stakeholder; shared document or report; team announcement |
| 🟢 FYI | Low priority, batch-processable | Newsletter; automated notification; calendar confirmation; marketing; CC-only with no direct mention of user (unless from leadership) |

When classification is ambiguous, default to 🟡 Read & Review and flag for user decision.

### Sensitivity Rules

Emails containing any of these keywords are always classified as 🔴 Action Required regardless of other signals:

`legal`, `HR`, `confidential`, `PIP`, `investigation`, `compliance`, `attorney`, `escalation`

These emails are never auto-archived or auto-categorized as FYI.

### Email Attribution

When creating tasks from email content, use this attribution pattern (per `.shared/skills/markdown/task-format.md`):

```
*(from: email — {email subject}, {YYYY-MM-DD})*
```

The date is the email's delivery date, not the date the task was created.

### Thread Handling

When an email conversation has 3 or more messages, generate a thread summary:

```
📧 Thread Summary: {subject} ({n} messages)
Overview: {one paragraph}
Decisions: {bulleted list or "None yet"}
Open Questions: {bulleted list or "None"}
Latest: {most recent substantive message summary}
```

Distinguish substantive messages from automated replies, forwarding notes, or acknowledgments.

### Archive vs Delete

- **Archive by default.** Archive is searchable and recoverable.
- **Delete only:** newsletters the user will never reference, automated notifications older than 7 days, marketing/promotional emails.
- When in doubt, archive — never delete.
- All archive and delete operations require user approval.

---

## Error Handling

| Scenario | Behavior |
|----------|----------|
| No unread emails (triage) | Report "Inbox is clear — nothing to triage" and stop |
| No flagged emails (flagged review) | Report "No flagged emails found" and stop |
| No cleanup candidates (sweep) | Report "Inbox is clean — nothing to sweep" and stop |
| Email read fails | Skip that email, note in output, continue |
| Diary file write fails | Report failure, suggest manual entry, continue |
| Email send fails | Report failure, offer to save draft instead |
| Large inbox (50+ emails) | Process in batches of 20, ask to continue after each |
| Ambiguous classification | Default to 🟡 Read & Review, flag for user decision |

## Boundaries

- Never send, move, delete, or archive email without explicit approval
- Never create tasks without explicit approval
- Always present recommendations before executing
- Always preserve user's ability to adjust classifications
- Never fabricate email content or actions not present in original message
- Never skip sensitivity keyword check
- Always batch large operations and ask to continue
- Never auto-archive emails flagged or matching RPs without explicit approval
