# 2. Event Log Invariants

Once you have a canonical Day↔Date mapping, the next step is to keep individual log files internally consistent.

This section assumes a simple JSON structure:

```json
{
  "metadata": {
    "total_events": 3,
    "max_id": 3,
    "days_covered": [329, 330],
    "last_updated_day": 330
  },
  "events": [
    {"id": 1, "day": 329, "date": "2026-02-24", ...},
    {"id": 2, "day": 330, "date": "2026-02-25", ...},
    {"id": 3, "day": 330, "date": "2026-02-25", ...}
  ]
}
```

The exact shape of events can vary. What matters is that the **metadata is a truthful summary of the events**, and the events themselves respect the day/date mapping and ordering rules.

## 2.1. Core invariants

For any log with this shape, enforce at least:

1. `total_events == len(events)`.
2. `max_id == max(event.id)` across all events.
3. Event IDs are **positive and unique**.
4. `days_covered` is the sorted list of distinct `event.day` values.
5. `last_updated_day == max(event.day)`.
6. Every event satisfies `event.date == date_for_day(event.day)`.
7. Events are globally sorted by `(day, time, id)` (or at minimum `(day, id)` if no time is present).

These are cheap to check and catch a large fraction of real‑world mistakes.

## 2.2. Why ordering matters

If logs are not sorted, then:

- binary search and range queries become fragile
- “scan until day X” style code can silently miss events
- cross‑file references become harder to reason about

Even if your storage engine can handle unsorted input, it is still worth enforcing a **canonical on‑disk order**.

## 2.3. Derive, don’t hand‑maintain

Patterns to prefer:

- Derive `days_covered`, `first_day`, `last_day`, `first_date`, `last_date` directly from events at write time.
- Re‑compute them during validation rather than trusting what’s stored.

Patterns to avoid:

- Copy‑pasting metadata between files and editing only some fields.
- Allowing application code to specify `date` directly when `day` is known.

The exercises folder contains small, malformed logs that violate these invariants; your task is to spot and repair them.
