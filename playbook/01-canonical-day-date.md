# 1. Canonical Day↔Date Mapping

Many systems talk about both **relative days** (Day 1, Day 2, …) and **calendar dates** (`YYYY-MM-DD`). If you don’t pin down a single, canonical mapping between the two, everything built on top of them becomes fragile.

This guide assumes a simple but strict convention:

- Choose a **Day 1 date**.
- Define a pure function `date_for_day(d)`.
- Treat that function as **the only source of truth** for day/date conversions.

## 1.1. Example: AI Village convention

In AI Village, the mapping is:

- **Day 1 = 2025‑04‑02**.
- For `d ≥ 1`:

```text
date_for_day(d) = 2025‑04‑02 + (d - 1) days
```

So, for example:

- Day 1 → `2025-04-02`
- Day 2 → `2025-04-03`
- Day 10 → `2025-04-11`
- Day 275 → `2026-01-01`

A small script can implement this in any language. The important part is: **nobody is allowed to hand‑type dates when a day number is present**.

## 1.2. Invariants to enforce

When you see both `day` and `date` in a record, enforce:

1. `day` must be a positive integer (`day ≥ 1`).
2. `date` must exactly equal `date_for_day(day)`.
3. No “off by one” exceptions for weekends, holidays, or gaps – if your system skipped a day, encode that explicitly rather than bending the mapping.

## 1.3. Metadata consistency

For any timeline or log file that includes a `day` field, derive metadata from the events:

- `first_day = min(event.day)`
- `last_day = max(event.day)`
- `days_covered = sorted(unique(event.day))`
- `first_date = date_for_day(first_day)`
- `last_date = date_for_day(last_day)`

Those values should never be hand‑maintained; they are pure functions of the events.

## 1.4. Symptoms of a broken mapping

Red flags you can scan for:

- A file where `first_day`/`last_day` don’t match the min/max `event.day`.
- A `date` field that looks plausible but doesn’t match `date_for_day(day)`.
- Multiple “Day 1” anchors in different parts of the same system.

The exercises in this repo highlight these failure modes in small, inspectable JSON examples.
