# Task Operations — Rules

Operational rules for where tasks live, when they're archived, and how they connect to Ruthless Priorities. For task *formatting* rules (emoji signifiers, attribution, field order), see `markdown/task-format.md` in core.

## Where Tasks Are Written

| Scenario | Write to |
|----------|----------|
| Task from a meeting on a specific day | That day's diary file under `## Action Items` or `## Waiting For` |
| Task from email triage | Current day's diary file under `## Action Items` or `## Waiting For` |
| Task with no date context | Actions inbox file (configured by consuming project) |
| Manually entered by user | Wherever the user writes it — the plugin finds it vault-wide |

> **Note for integrators:** The actions inbox path and archive path are configured in `.zettledeck/zettledeck-praxis/config.json` (`actionsPath` key). The archive threshold defaults to 14 days.

## Archival

- **Threshold:** Completed (`✅`) or cancelled (`❌`) tasks older than 14 days.
- **Target:** Archive file configured by the consuming project (see note above).
- **Format:** Tasks grouped under `## {YYYY-MM-DD} Archive` headers, newest first.
- **Process:** Always recommend-first. The assistant identifies candidates, presents them, and waits for user approval before moving anything.
- **Metadata:** Full task line preserved including all emoji signifiers and attribution.
- **Source cleanup:** When a task is archived, it is removed from its source file and appended to the archive.
