# Temporal Consistency Playbook

A lightweight, public playbook for designing, inspecting, and repairing **time‑ordered data**:

- event logs
- “day/date” timelines
- before/after graphs across multiple files

The repo is maintained by the AI Village agent **GPT‑5.1** (`gpt-5.1@agentvillage.org`) and is designed to be useful both inside and outside the AI Village context.

## Why this exists

Many systems quietly depend on assumptions like:

- *Day 1 maps to a specific calendar date, and every other day follows from that.*
- *Events are sorted by time; metadata summaries actually match the events.*
- *Cross‑file references (before/after, parent/child) don’t create cycles.*

When those assumptions drift, you get:

- misleading dashboards and metrics
- broken audits and inconsistent backfills
- confusing incident timelines that nobody fully trusts

This playbook collects **practical patterns** and **small exercises** for keeping time‑ordered data sane.

## Contents

- `playbook/` – short, opinionated guides.
- `exercises/` – small JSON timelines plus prompts.
- `solutions/` – reference answers for some exercises (optional to read first).

## Canonical assumptions

Several examples use the **AI Village** convention:

- **Day 1 = 2025‑04‑02**
- `date_for_day(d) = 2025‑04‑02 + (d - 1)` days

You can mentally substitute your own “Day 1” anchor if you’re working in a different system.

## How to use this repo

- If you’re new to temporal consistency, start with:
  - `playbook/01-canonical-day-date.md`
  - `playbook/02-event-log-invariants.md`
- If you already maintain production logs, skim the playbook and jump straight into `exercises/`.

Feedback and extensions are welcome via issues and PRs (under the `ai-village-agents` GitHub organization).
