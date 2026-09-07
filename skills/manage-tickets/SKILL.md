---
description: Use when creating a new ticket, picking up or resuming work on one, moving a ticket between open/in_progress/blocked/finished, editing its Definition of Done or metadata, or closing it out. Governs this repo's file-based tickets/ convention — tickets as markdown files with metadata, a Definition of Done, and an append-only Log.
---

# Managing tickets

This repo tracks work as markdown files under `tickets/`, one file per
ticket, organized into four state folders:

```
tickets/
  open/
  in_progress/
  blocked/
  finished/
```

A ticket's location *is* its status. Moving a ticket between folders is
how its state changes — there is no separate status field to keep in
sync with the folder.

Each ticket's YAML frontmatter conforms to
[OKF v0.1](https://okf.md/spec/) (Google's Open Knowledge Format,
published 2026-06-12) — a directory of markdown files with YAML
frontmatter where the only required field is `type`, and `title`,
`description`, `resource`, `tags`, and `timestamp` are recommended.
This means any OKF-aware tool or agent can read this repo's tickets
without special-casing this project's format. `id`, `priority`, and
`links` are producer-defined extensions, which OKF explicitly permits
and requires consumers to preserve.

## Creating a ticket

1. Copy `template.md` (bundled with this skill) into `tickets/open/<slug>.md`,
   where `<slug>` is a short kebab-case identifier derived from the title
   (e.g. `fix-flaky-ingest-test.md`).
2. Fill in the metadata block, Description, and Definition of Done.
3. Add one Log entry: `- YYYY-MM-DD: Created.`

Keep the Definition of Done concrete and checkable — each line should be
something you can look at when the ticket is done and say yes or no to,
not a restatement of the description.

## Working a ticket

When you start work on an open ticket:

1. Move the file: `tickets/open/<slug>.md` → `tickets/in_progress/<slug>.md`.
   Use `git mv` so history follows the file.
2. Add a Log entry noting you started, and anything relevant a future
   reader (agent or human) would need to pick this up cold — decisions
   made, approaches tried and abandoned, why, blockers hit.

If the ticket becomes blocked on something external, move it to
`tickets/blocked/` and log what it's waiting on. Move it back to
`in_progress/` (with a log entry) once unblocked.

When all Definition of Done items are satisfied, move the file to
`tickets/finished/`, check off every DoD item, and add a closing Log
entry summarizing what shipped.

## The Log section is mandatory, not optional

Every ticket's Log is the running record of everything that happened to
it: work done, decisions made, and — just as importantly — any edit to
the ticket's own metadata or Definition of Done. If you change the
priority, retitle it, or add/remove a DoD item, add a Log entry saying
what changed and why. The Log is what lets an agent with no memory of
previous sessions resume a ticket without re-deriving context from
scratch — treat gaps in it as a bug in how the ticket was worked.

Log entries are appended in chronological order (oldest first, newest
at the bottom), each starting with the date:

```
## Log

- 2026-09-06: Created.
- 2026-09-07: Started. Tried approach X, hit <problem>; switched to Y.
- 2026-09-09: Bumped priority P2 -> P1 per user request. Marked DoD item
  2 done; added a new DoD item for the edge case found while testing.
- 2026-09-10: Finished. All DoD items satisfied. Moved to finished/.
```

## Conventions

- One ticket = one markdown file. Filename and `id` in the metadata
  block should match.
- `type` is always `ticket`. This is the field OKF requires — don't
  remove or repurpose it.
- `description` in the metadata block is a single-line summary (for
  previews and index generation, per OKF); it's not a substitute for
  the full `## Description` section below it.
- `timestamp` is OKF's field for "last significant change" — bump it
  (a bare date is precise enough) any time you touch metadata, the
  DoD, or add a Log entry, the same way `updated` worked before OKF.
- `resource` is optional and omitted by default — OKF treats it as the
  canonical URI of an external asset the document describes, so only
  set it when a ticket mirrors an issue in an external tracker
  (GitHub, Jira, etc).
- `priority` is one of `P1` (urgent), `P2` (normal), `P3` (someday).
- `links` can hold related ticket ids, PR URLs, or commit SHAs — use it
  instead of vague prose references where possible.
- Never delete a ticket to "cancel" it; move it to `finished/`, note in
  the Log that it was abandoned and why, and leave the DoD unchecked.
- Always use `git mv` (not a plain move) when relocating a ticket file,
  so its history stays attached across the folder change.
