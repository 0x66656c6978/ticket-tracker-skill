# ticket-tracker

A Claude Code plugin for file-based ticket tracking: tickets live as
markdown files in a repo's `tickets/` folder, moved between
`open/`, `in_progress/`, `blocked/`, and `finished/` as work progresses.
Each ticket carries metadata, a Definition of Done, and an append-only
Log documenting everything relevant that happens to it — including
changes to the ticket's own metadata or DoD.

Designed for repos where an AI agent does a meaningful share of the
work: the folder-per-state model and the mandatory Log section exist
specifically so an agent picking up a ticket with no memory of prior
sessions can resume it without re-deriving context.

See [`skills/manage-tickets/SKILL.md`](skills/manage-tickets/SKILL.md)
for the full convention, and
[`skills/manage-tickets/template.md`](skills/manage-tickets/template.md)
for the ticket template.

## What this is not

This is a convention, not a database or a Kanban tool. It has no CLI,
no dependency graph, and no visualization. It's the right amount of
tooling for a project maintained by one person (with AI agents doing
a lot of the driving) that wants tickets as plain, git-diffable text.
If a project outgrows that — a large backlog, several
concurrently-working agents, a need for structural dependency
tracking — a heavier tool (e.g. Backlog.md or Beads) is a better fit;
this plugin's ticket files stay plain markdown either way, so nothing
here is wasted if that day comes.

## Install

In a project where you want this convention active for everyone who
opens the repo, add to that project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "ticket-tracker": {
      "source": {
        "source": "github",
        "repo": "<your-github-username>/ticket-tracker"
      }
    }
  },
  "enabledPlugins": {
    "ticket-tracker@ticket-tracker": true
  }
}
```

Or install it into just your own Claude Code, everywhere, with:

```bash
claude plugin marketplace add <your-github-username>/ticket-tracker
claude plugin install ticket-tracker@ticket-tracker
```

Either way, a project also needs the actual folders to exist:

```bash
mkdir -p tickets/{open,in_progress,blocked,finished}
touch tickets/open/.gitkeep tickets/in_progress/.gitkeep \
      tickets/blocked/.gitkeep tickets/finished/.gitkeep
```

## Test locally before publishing

```bash
claude --plugin-dir /path/to/ticket-tracker
```

Then ask Claude to create a ticket, or type `/ticket-tracker:manage-tickets`
directly, to confirm it behaves as expected.
