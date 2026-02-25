# 3. Cross‑File Before/After Graphs

Single‑file consistency is only half the story. Many systems express temporal structure **indirectly** via references:

- `before_event_id` / `after_event_id`
- parent/child relationships
- causal links between incidents in different services

This guide focuses on *before/after* edges across multiple files.

## 3.1. Model

Assume each event lives in exactly one file and has a unique `(file_label, id)` pair, for example:

- File A events: `a:1`, `a:2`, …
- File B events: `b:1`, `b:2`, …

Events have at least:

- `day` (integer)
- `time_utc` (`HH:MM`)
- optional `before_event_id` / `after_event_id` (strings like `"a:3"`, `"b:5"`)

Define a **sort key** for each event:

```text
sort_key(event) = (day, minutes_since_midnight, id)
```

where `minutes_since_midnight = 60 * hour + minute`.

## 3.2. Intended semantics

For each event `E`:

- If `E.after_event_id = X`, then **X must be strictly earlier than E**.
- If `E.before_event_id = X`, then **X must be strictly later than E**.

Translated into sort‑key comparisons:

- `after_event_id = X` → `sort_key(X) < sort_key(E)`
- `before_event_id = X` → `sort_key(E) < sort_key(X)`

These constraints must hold even when X lives in another file.

## 3.3. Acyclic global graph

Treat each event as a node, and add directed edges:

- `after_event_id = X` → edge `X → E`
- `before_event_id = X` → edge `E → X`

The resulting graph must be a **DAG** (no directed cycles). A cycle indicates mutually contradictory before/after claims.

A simple depth‑first search (DFS) with a recursion stack is enough to detect cycles.

## 3.4. Validation checklist

When validating or repairing cross‑file timelines:

1. **Reference syntax**
   - Every `before_event_id` / `after_event_id` is either `null`/absent or a string like `"a:3"` or `"b:7"`.
   - No other prefixes or free‑form text.

2. **Reference existence**
   - Every reference points to a real event.
   - No duplicates of `(file_label, id)`.

3. **Temporal ordering**
   - Compute `sort_key` for all events.
   - Check each edge against the inequalities above.

4. **No cycles**
   - Build the directed graph from edges.
   - Run cycle detection; if any cycle exists, you must change at least one time or reference.

## 3.5. Repair strategies

When you find inconsistencies:

- Prefer to adjust **times** and **day numbers** when the high‑level sequence is clear but the clock stamps drifted.
- Prefer to adjust **references** when the clock stamps look consistent but an event was wired to the wrong neighbor.
- Avoid deleting events; instead, repair them so the story remains intact.

The multi‑file exercises in this repo are designed to surface these failure modes in a controlled setting.
