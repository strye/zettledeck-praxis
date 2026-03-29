---
module: zettledeck-praxis
---

# Note Rules — Praxis

Rules for the note skill when operating within Praxis module workspace folders.

---

## Workspace Folders

| Role | Folder | Accept new notes? |
|------|--------|-------------------|
| `praxis` | `Praxis/` | Yes — operational notes, meeting notes, task-adjacent captures |
| `scriptorium` | `Scriptorium/` | Yes — correspondence drafts and communication workspace files |

---

## Placement Rules

When a docType is identified, suggest the following folder by default:

| docType | Default role | Notes |
|---------|-------------|-------|
| `meeting` | `praxis` | Meeting notes live in Praxis; see delegation below |
| `note` | `praxis` | General operational notes |
| `correspondence` | `scriptorium` | Any correspondence draft; see delegation below |

---

## Delegation Rules

When the user's intent matches the following patterns, offer to delegate to a specialist skill rather than creating a bare file. The user decides — the note skill does not invoke the specialist automatically.

### Meeting notes (`docType: meeting`)

- **Skill:** `/diary meeting-summary`
- **Offer:** "The `diary` skill has a guided workflow for importing and structuring meeting notes — use it instead of a bare file?"
- **When:** User wants to create a meeting note, especially from an email summary or calendar event. If the user just wants a blank meeting file, proceed with bare creation.

### Correspondence drafts (`docType: correspondence` or Scriptorium destination)

- **Skill:** `/comms new`
- **Offer:** "The `comms` skill creates a structured correspondence workspace file with iteration tracking — use it instead?"
- **When:** User wants to draft a response to an email, Slack message, or other professional communication. Files created by `comms` have Ask/Context/Response/Conversation Log sections. If the user just wants a blank file in Scriptorium, proceed with bare creation.

---

## Exclusions

No Praxis folders are excluded from note creation. Both `praxis` and `scriptorium` are valid destinations.
