# Email — Cleanup Sweep Mode

Batch archive or delete old processed emails from inbox.

## Steps

**1. Scan inbox**

Call `email_inbox` (without `unreadOnly`) to retrieve emails. Filter for read emails.

**2. Apply age threshold**

Default: emails older than 7 days. If user specifies different threshold, use that.

**3. Categorize candidates**

Per the Shared Context in SKILL.md:

- **Archive:** Read, unflagged, no pending action
- **Delete:** Newsletters, automated notifications > 7 days, marketing/promotional

**4. Sensitivity exclusion**

Exclude any email matching sensitivity keywords from both categories. These are never auto-archived or deleted.

**5. Present batch recommendation:**

```
## 🧹 Inbox Cleanup — {date}
Scanned inbox for read emails older than {threshold} days.

### Archive ({count})
{list of subjects, or sample of 5 if more}

### Delete ({count})
{list of subjects, or sample of 5 if more}

---
**Recommendations:**
- Archive {n} emails?
- Delete {n} emails?
- Review individual items first?
```

**6. On approval, execute:**

- **Archive:** Call `email_move` with `targetFolder: "archive"` for each `conversationId`
- **Delete:** Call `email_move` with `targetFolder: "deleteditems"` for each `conversationId`
- Report totals after execution
