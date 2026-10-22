# RSD Coverage Checklist

Walk every R.S# entry through this list before drafting it. For each line: if it applies and you know the answer, write it into the entry; if it applies and you don't know, write your best default, tag it `[OQ-xx]`, and log it; if it doesn't apply, skip it silently.

## Inputs (Trigger / Input Fields)
- Where is the action triggered from (screen, button, schedule, integration event)?
- Every field: name, type, mandatory/optional, length/range, format, default value.
- Lists/dropdowns: source of values, only active items?, single or multi-select.
- Dates/times: timezone (UTC vs local), allowed past/future, start < end.
- Attachments: allowed types, max size, max count.

## Validations
- **Uniqueness:** what must be unique, and in what scope (global, per parent, among active only)?
- **State rules:** in which statuses is the action allowed/blocked? (e.g. can't edit after Approved)
- **Permissions:** which roles can do it; can they act on all records or only their own unit/team?
- **Referential integrity:** deleting/deactivating something that others depend on - block, cascade, or warn?
- **Business limits:** quotas, max counts, minimum staffing, overlaps, conflicts with existing records.
- **Concurrency:** two users editing the same record - last write wins, lock, or warn?
- **Duplicate submission:** double-click / resubmit protection.

## Upon Action
- **User Feedback:** success message, confirmation before destructive actions, each error message's intent.
- **System Reflection:**
  - Status transitions (from -> to).
  - Records created/updated/removed in other modules.
  - Dashboards, counters, reports affected (reference their R.S#).
  - Notifications triggered: to whom, which channel, when.
  - Outbound integration calls: what is sent, sync/async.
- **Routing:** where the user lands on success, on cancel, on failure.
- **Logging:** action name, actor, timestamp, before/after values for edits, reason field if required.

## Alternative & exception paths
- Cancel midway / unsaved changes warning.
- Integration failure or timeout: retry policy, user message, manual fallback, log.
- Partial success in bulk actions: what is saved, how errors are reported.
- Empty states: nothing to list/search yet.
- Approval flows: approve, reject (with reason?), return for changes, escalation/timeouts, delegation.

## Cross-cutting
- Glossary: every entity, role and status name matches `05 Glossary`; no *Avoid* synonyms.
- Localization: bilingual labels, RTL, date/number formats.
- Data retention / soft vs hard delete.
- Regulatory or compliance rules from the brief's Constraints.
- Consistency with competitor patterns noted in the brief (only if relevant, sourced, and Medium/High confidence).
- Approved Decisions (`04 Decisions`) that constrain this entry - reflect them with the PM's exact wording.
