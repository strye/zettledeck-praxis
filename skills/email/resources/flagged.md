# Email — Flagged Review Mode

Review flagged emails, assess staleness, and recommend next steps.

## Steps

**1. Retrieve flagged emails**

Call `email_search` with query `flag:flagged` or retrieve inbox emails and filter for those with active flags. If no flagged emails, report "No flagged emails found" and stop.

**2. Read each flagged email**

Call `email_read` for full content and thread context.

**3. Load RPs**

Read `{rp-path}/ruthless-priorities.md` for RP alignment.

**4. Assess each flagged email:**

- Calculate age since flag date (or delivery date if flag date unavailable)
- Classify staleness: current (< 7 days), aging (7-14 days), stale (> 14 days)
- Recommend next step based on content and age:

| Signal | Recommendation |
|--------|---------------|
| Unanswered question to user | Respond |
| Describes task not yet tracked | Create action item |
| Waiting on someone else | Create waiting-for item |
| Informational and fully read | Unflag & archive |
| Stale (> 14 days) with no clear action | Unflag & archive |
| Involves leadership or cross-org visibility | Escalate |

**5. Present recommendations:**

```
## 🚩 Flagged Email Review — {date}
{count} flagged emails reviewed.

### Stale (> 14 days) ({count})

**1. {subject}** — from {sender}, flagged {n} days ago
   Summary: {1-2 sentence summary}
   🎯 RP: {RP name, if applicable}
   💡 Recommendation: {respond / create action item / delegate / unflag & archive / escalate}
   Reason: {brief rationale}

### Aging (7-14 days) ({count})

**1. {subject}** — from {sender}, flagged {n} days ago
   Summary: {1-2 sentence summary}
   💡 Recommendation: {next step}

### Current (< 7 days) ({count})

**1. {subject}** — from {sender}, flagged {n} days ago
   Summary: {1-2 sentence summary}
   💡 Recommendation: {next step}

---
**Recommendations:**
- Create {n} action items?
- Create {n} waiting-for items?
- Unflag & archive {n} emails?
- Draft a reply to any? (specify which)
- Any adjustments?
```

**6. On approval, execute:**

- **Create action item:** Write to diary file per task-management rules
- **Create waiting-for:** Write to diary file with `@person:` prefix
- **Unflag & archive:** Call `email_update` to set flag status to `NotFlagged`, then `email_move` to archive
- **Draft reply:** Switch to Draft Response mode for that email
