# Obsidian Vault Template

A general-purpose Obsidian vault template implementing PARA structure with agent-first design. Works for both work and personal life management.

## What's Included

- **PARA structure** — Projects / Areas / Resources / Archive
- **13 Templater templates** — daily notes, meetings, tasks, projects, people, learning, purchases, assets, finances, wiki
- **10 Base query files** — kanban board, task views, meeting views, project tracking, people, assets, finances
- **Agent-first CLAUDE.md** — complete operating manual for Claude Code or any AI agent
- **Obsidian plugin configuration** — pre-configured for Daily Notes, Templater, Buttons, and more

## Quick Start

1. Click **Use this template** on GitHub to create your own vault repository
2. Clone to your machine
3. Open the folder as an Obsidian vault
4. Install required plugins (see below)
5. Edit `CLAUDE.md` → replace the **Vault Purpose** section with your context
6. Open `Home.md` as your starting point

## Manual Configuration

After installing plugins, configure Templater folder triggers in Obsidian Settings → Templater → Folder Templates:

| Folder | Template |
|---|---|
| `_daily/daily-notes` | `_assets/templates/daily-note.template.md` |
| `Projects` | `_assets/templates/project.template.md` |
| `Areas` | `_assets/templates/area.template.md` |
| `Resources/Wiki` | `_assets/templates/wiki.template.md` |
| `Resources/Assets` | `_assets/templates/asset.template.md` |

Also set in Templater settings:
- Templates folder: `_assets/templates`
- Scripts folder: `_assets/scripts`
- Enable "Trigger Templater on new file creation"

## Required Plugins

Install via Obsidian Settings → Community Plugins:

| Plugin | ID | Required |
|---|---|---|
| Templater | `templater-obsidian` | ✅ |
| Buttons | `buttons` | ✅ |
| Kanban Bases View | `kanban-bases-view` | ✅ (for board view) |
| Obsidian Git | `obsidian-git` | Recommended |
| Periodic Notes | `periodic-notes` | Recommended |
| QuickAdd | `quickadd` | Recommended |
| Dataview | `dataview` | Recommended |
| Calendar | `calendar` | Optional |
| Commander | `cmdr` | Optional |
| Local REST API | `obsidian-local-rest-api` | Optional (agent API access) |
| Smart Connections | `smart-connections` | Optional (semantic search) |

> **Note:** No plugin configs are bundled in this template. All plugins must be installed and configured manually after cloning.

## Work vs Personal Instantiation

This template works for both. After setup, update `CLAUDE.md` Vault Purpose to reflect your context:

**Work vault example:**
> "Work vault for [Name], software engineer/tech lead at [Company]. Areas include team management, active projects, services/systems. People are colleagues and direct reports."

**Personal vault example:**
> "Personal vault for [Name]. Areas include finances, relationships, home. Resources include purchases under research, assets owned, learning queue."

## Agent-First Usage

With [Claude Code](https://claude.ai/code) pointed at this vault, an agent can:
- Answer queries about your notes ("what did I decide this week?")
- Create daily notes, meeting notes, tasks
- Generate weekly summaries from daily notes
- Triage inbox items
- Catalogue assets and track purchases

See `CLAUDE.md` for the full agent operations reference.

## Structure

```
Projects/       Active time-boxed work
Areas/          Ongoing responsibilities
Resources/      Reference (wiki, learning, purchases, assets)
Archive/        Completed/retired
_daily/         Daily notes
_inbox/         Capture inbox
_generated/     Agent-written summaries
_assets/        Templates, bases, scripts, attachments
Home.md         Dashboard
CLAUDE.md       Agent context
```
