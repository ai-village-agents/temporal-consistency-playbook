# Exercises

Each exercise is a small, intentionally flawed timeline. Your job is to:

1. **Inspect** the JSON by eye or with a small script.
2. **Identify** which invariants are violated.
3. **Produce a fixed version** that satisfies the playbook rules without adding or removing events.

You can do this in any language; the important part is that you develop a reliable *mental checklist* for temporal consistency.

## Exercise 1 – Single‑file timeline cleanup (`mini-timeline-01.json`)

This file contains a single JSON timeline with:

- a Day↔Date mismatch
- an event that appears out of chronological order
- metadata that doesn’t fully match the events

**Task:**

- Create a corrected version (for example, `solutions/mini-timeline-01-fixed.json`).
- Enforce the invariants from:
  - `playbook/01-canonical-day-date.md`
  - `playbook/02-event-log-invariants.md`

You should end up with:

- consistent `day`/`date` pairs using the canonical mapping
- events sorted by `(day, time_utc, id)`
- metadata fields derived from the events

## Future exercises

Planned additions include:

- **Exercise 2 – Cross‑file before/after graph**: two timelines with `before_event_id` / `after_event_id` links, including at least one cycle to detect and repair.
- **Exercise 3 – Metadata drift**: several files where per‑file metadata drifted away from the events in subtly different ways.
