# Email — Draft Response Mode

Draft an email reply for user review and approval.

## Steps

**1. Identify target email**

User specifies which email by:
- Subject or sender name ("reply to the Nike email")
- Triage item number ("draft a reply to #2")
- Conversation ID (if provided directly)

If ambiguous, ask user to clarify.

**2. Read full thread**

Call `email_read` with `conversationId` and `format: "markdown"` to get complete conversation. Note the `itemId` and `itemChangeKey` of most recent message — these are needed to send reply.

**3. Gather user intent**

If user provided instructions (e.g. "decline politely", "ask for deadline extension", "confirm and schedule follow-up"), use those. If not, infer likely response type from email content and ask user to confirm before drafting.

**4. Generate draft**

Write response that:
- Matches user's tone: warm, direct, concise
- Addresses specific points raised in email
- Incorporates any user instructions
- Does not fabricate information not in thread or provided by user
- Gets to the point in first sentence
- Uses appropriate greeting and sign-off

**5. Present for review:**

```
## ✉️ Draft Reply

**To:** {recipients}
**Subject:** RE: {subject}
**Mode:** {Reply / Reply All}

---

{draft body}

---

**Actions:**
- Send as-is?
- Edit? (tell me what to change)
- Switch to Reply All / Reply?
- Cancel?
```

**6. On approval:**

- **Send:** Call `email_reply` with `itemId`, `itemChangeKey`, `body` (HTML formatted), and `replyAll` as appropriate
- **Edit:** Revise per user feedback, present again
- **Cancel:** Discard, no action taken
