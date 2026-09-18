# Obsidian Vault Template — Design Spec

**Date:** 2026-09-15  
**Status:** Draft

## Overview

A general-purpose Obsidian vault template designed to work for two instantiations:
- **Work vault** — software engineer / technical lead (meetings, people, projects, services)
- **Personal vault** — life management (finances, projects, learning, purchases, relationships)

Primary design constraints:
- **PARA structure** — folder = significance/lifecycle, type = frontmatter property
- **Agent-first** — Claude Code can read, query, and write the vault with zero onboarding via CLAUDE.md
- **Bases + properties** — all cross-cutting views driven by frontmatter, not folder paths
- **Kanban task management** — todo / in-progress / done / blocked

---

## Folder Structure

```
/
├── Home.md                        # Dashboard — buttons, active tasks, inbox count
├── CLAUDE.md                      # Agent context (comprehensive)
│
├── Projects/                      # Active time-boxed work (work features, side projects)
├── Areas/                         # Ongoing responsibilities (finances, team, relationships)
├── Resources/                     # Reference material (wiki, learning, purchases)
├── Archive/                       # Completed/retired (mirrors PARA structure inside)
│
├── _daily/
│   └── daily-notes/               # YYYY-MM-DD.md
│
├── _inbox/                        # Capture inbox — triage later
│
├── _generated/
│   ├── weekly-summaries/          # YYYY-Www.md (agent-written)
│   ├── monthly-summaries/         # YYYY-MM.md (agent-written)
│   └── meeting-summaries/         # YYYY-MM-DD - Title - summary.md (agent-written)
│
└── _assets/
    ├── templates/                 # Templater .template.md files
    ├── bases/                     # .base query files
    ├── scripts/                   # Templater JS scripts
    ├── attachments/               # Images, PDFs (Obsidian default paste target)
    ├── excalidraw/                # .excalidraw diagrams
    └── canvas/                    # .canvas files
```

`_` prefix on meta dirs sorts them to top in the file explorer and signals system/infrastructure vs. content.

Notes live inside `Projects/`, `Areas/`, `Resources/` — no entity-type subfolders. Type is a **frontmatter property**, not a folder. Agent locates entities by querying `type`, not traversing paths.

---

## Entity Types & Property Schemas

Every note has `type` as its first frontmatter property. This is the anchor for all bases and agent queries. `.template-name` and `.template-version` track which template was applied.

**Universal property on every entity type:**
```yaml
related: []   # links to any related notes — agent populates bidirectionally on creation
```

### task
```yaml
type: task
status: todo | in-progress | done | blocked
priority: p1 | p2 | p3
project: "[[Project Name]]"
area: "[[Area Name]]"
due: YYYY-MM-DD
created-from: "[[Meeting or Daily Note]]"
related: []
.template-name: "[[task.template]]"
.template-version: 1.0.0
```
`created-from` is optional — set when agent creates a task from a meeting or daily note. Enables `Tasks.base#Today's Tasks` and `Tasks.base#Meeting Tasks` views to filter by source entity.

Path: anywhere in `Projects/` or `Areas/` subdirectory relevant to it.

### project
```yaml
type: project
status: active | paused | complete
area: "[[Area Name]]"
start-date: YYYY-MM-DD
target-date: YYYY-MM-DD
outcome: one-line goal statement
related: []
.template-name: "[[project.template]]"
.template-version: 1.0.0
```
Path: `Projects/<project-name>.md` or `Projects/<project-name>/index.md` for multi-file projects.

### area
```yaml
type: area
related: []
.template-name: "[[area.template]]"
.template-version: 1.0.0
```
Path: `Areas/<area-name>.md`

### meeting
```yaml
type: meeting
date: YYYY-MM-DD
attendees: ["[[Person Name]]"]
project: "[[Project Name]]"
area: "[[Area Name]]"
meeting-type: 1-1 | standup | planning | review | ad-hoc
related: []
.template-name: "[[meeting.template]]"
.template-version: 1.0.0
```
Path: `Areas/<area>/meetings/YYYY-MM-DD - Title.md` or `Projects/<project>/meetings/YYYY-MM-DD - Title.md`

### person
```yaml
type: person
relationship: colleague | direct-report | manager | friend | family | contact
org: company or group name
email:
birthday: YYYY-MM-DD
related: []
.template-name: "[[person.template]]"
.template-version: 1.0.0
```
Path: `Areas/People/<Person Name>.md`

### learning
```yaml
type: learning
medium: book | course | article | podcast | video
status: want | in-progress | done
author:
url:
project: "[[Project Name]]"
related: []
.template-name: "[[learning.template]]"
.template-version: 1.0.0
```
Path: `Resources/Learning/<title>.md`

### purchase
```yaml
type: purchase
status: researching | decided | purchased
category: electronics | home | clothing | tools | other
price:
vendor:
decision-date: YYYY-MM-DD
related: []
.template-name: "[[purchase.template]]"
.template-version: 1.0.0
```
Path: `Resources/Purchases/<item-name>.md`

### finance
```yaml
type: finance
period: YYYY-MM
category: income | expense | investment | savings
amount:
account:
related: []
.template-name: "[[finance.template]]"
.template-version: 1.0.0
```
Path: `Areas/Finances/<YYYY-MM> - <description>.md`

### wiki
```yaml
type: wiki
tags: []
related: ["[[Note Name]]"]
.template-name: "[[wiki.template]]"
.template-version: 1.0.0
```
Path: `Resources/Wiki/<topic>.md`

### asset
```yaml
type: asset
asset-type: digital | physical
status: active | inactive | disposed
category: electronics | software | vehicle | furniture | appliance | tool | subscription | domain | other
acquired-date: YYYY-MM-DD
value:
serial:
warranty-expiry: YYYY-MM-DD
location:        # physical: where stored; digital: platform/account
url:             # digital assets
vendor:
related: []
.template-name: "[[asset.template]]"
.template-version: 1.0.0
```
Path: `Resources/Assets/<asset-name>.md`

### daily-note
```yaml
type: daily-note
date: YYYY-MM-DD
related: []
.template-name: "[[daily-note.template]]"
.template-version: 1.0.0
```
Path: `_daily/daily-notes/YYYY-MM-DD.md`

---

## Bases

All base files in `_assets/bases/`. Filters on `type` property, not folder path (except Inbox which uses `file.inFolder`).

### Kanban.base
Primary task view. Board grouped by status.
```yaml
filters:
  and:
    - type == "task"
views:
  - type: board
    name: Kanban
    groupBy: status
    order: [file.name, priority, project, due]
  - type: table
    name: Blocked
    filters:
      and:
        - status == "blocked"
    order: [file.name, priority, project, area, due]
```

### Tasks.base
```
Views: Active | By Project | Overdue | Done
```
- `Active` — status != done, sorted by priority ASC then due ASC
- `By Project` — grouped by project, status != done
- `Overdue` — due < today AND status != done
- `Done` — status == done, sorted file.mtime DESC
- `Today's Tasks` — `note["Created From Entity"] == link(this)` (embeds in daily notes)
- `Project Tasks` — `project == link(file(this))` (embeds in project notes)

### Meetings.base
```
Views: All Meetings | Meetings - With Person | Meetings - This Day | Meetings - For Project
```
- `All Meetings` — type == meeting, sorted by date DESC
- `Meetings - With Person` — `attendees.contains(link(file(this)))`, limit 10 (embeds in person notes)
- `Meetings - This Day` — date matches `file.ctime.format("YYYY-MM-DD")` (embeds in daily notes)
- `Meetings - For Project` — `project == link(file(this))` (embeds in project notes)

### Projects.base
```
Views: Active | All | By Area
```
- `Active` — type == project AND status == active
- `All` — all projects, sorted status then target-date
- `By Area` — grouped by area

### People.base
```
Views: All | Colleagues | Personal
```
- `All` — type == person, sorted by file.name
- `Colleagues` — relationship in [colleague, direct-report, manager]
- `Personal` — relationship in [friend, family]

### Learning.base
```
Views: In Progress | Want To | Done
```
- `In Progress` — type == learning AND status == in-progress
- `Want To` — type == learning AND status == want, sorted file.ctime DESC
- `Done` — type == learning AND status == done

### Purchases.base
```
Views: Researching | Decided | All
```
- `Researching` — type == purchase AND status == researching
- `Decided` — type == purchase AND status == decided
- `All` — all purchases, sorted file.mtime DESC

### Finances.base
```
Views: By Period | By Category | Expenses
```
- `By Period` — grouped by period, sorted DESC
- `By Category` — grouped by category
- `Expenses` — category == expense

### Assets.base
```
Views: All | Physical | Digital | Warranties | By Category
```
- `All` — type == asset, sorted by category then file.name
- `Physical` — asset-type == physical
- `Digital` — asset-type == digital
- `Warranties` — warranty-expiry >= today, sorted by warranty-expiry ASC
- `By Category` — grouped by category
- `Related To` — `related.contains(link(file(this)))` (embeds in project/purchase notes)

### Inbox.base
```yaml
filters:
  and:
    - file.inFolder("_inbox")
views:
  - type: table
    name: Inbox
    order: [file.name, file.ctime]
    sort:
      - property: file.ctime
        direction: DESC
```

---

## Templates

All templates in `_assets/templates/`. Templater configured with `trigger_on_file_creation_mode: folder`.

| Template | Auto-trigger folder | Key contents |
|---|---|---|
| `daily-note.template.md` | `_daily/daily-notes/` | prev/next nav links, Overview, Decisions, `![[Tasks.base#Today's Tasks]]`, `![[Meetings.base#Meetings - This Day]]`, Summary |
| `project.template.md` | `Projects/` | Overview, Goals, `![[Tasks.base#Project Tasks]]`, `![[Meetings.base#Meetings - For Project]]` |
| `area.template.md` | `Areas/` | Overview, `![[Projects.base#Active]]`, `![[Tasks.base#Active]]` |
| `wiki.template.md` | `Resources/Wiki/` | Content, Related |
| `meeting.template.md` | manual | Manual Notes (Discussion Points, Outcomes), `![[Tasks.base#Project Tasks]]`, Transcript (Summary + Raw) |
| `1-1-meeting.template.md` | manual | Meeting sections + Personal Check-in, Current Work/Blockers, Career Development |
| `task.template.md` | manual | Background, Requirements, Related |
| `person.template.md` | manual | `![[Meetings.base#Meetings - With Person]]`, Overview, Notes |
| `learning.template.md` | manual | Summary, Key Takeaways, Notes, Related |
| `purchase.template.md` | manual | Options comparison table (name/price/pros/cons), Decision, Related |
| `finance.template.md` | manual | Details, Notes |
| `asset.template.md` | `Resources/Assets/` | Details, `![[Assets.base#Related To]]` |
| `weekly-summary.template.md` | none (agent reference only) | Highlights, Work, Personal, Decisions, Carry Forward, Daily note links Mon–Fri |

---

## Home.md

```markdown
# Home

\`\`\`button
name Today's Note
type command
action Daily Notes: Open today's daily note
\`\`\`

## Inbox
![[Inbox.base#Inbox]]

## Active Tasks
![[Kanban.base#Kanban]]

## Active Projects
![[Projects.base#Active]]
```

---

## Plugins

### Core (carry from reference vault)
| Plugin | Role |
|---|---|
| Daily Notes (core) | Creates notes in `_daily/daily-notes/` |
| Templater | Folder-triggered templates + JS scripting |
| Buttons | Home.md quick actions |
| Obsidian Git | Version control; agent commits changes |
| Excalidraw | Diagrams → `_assets/excalidraw/` |
| OmniSearch | Full-text search |
| Livesync | Cross-device sync |

### Add
| Plugin | Role |
|---|---|
| **Periodic Notes** | Proper weekly/monthly note support; triggers `_generated/weekly-summaries/` folder template |
| **QuickAdd** | Fast inbox capture without opening file; macros for "new task", "log purchase", "capture idea" |
| **Dataview** | Agent writes inline `dataview` queries for ad-hoc reporting; complements Bases |
| **Calendar** | Visual sidebar calendar linked to daily notes |
| **Commander** | Custom sidebar/ribbon buttons (Open Today, New Task, Triage Inbox) |
| **Local REST API** | Exposes vault as REST API — Claude Code can query/write programmatically |
| **Smart Connections** | Semantic embeddings index; enables "find related notes" for agent-generated summaries |

---

## CLAUDE.md Design

The vault's CLAUDE.md is the agent's complete operating manual. A fresh Claude session must be able to operate the vault correctly from CLAUDE.md alone.

### Required sections

1. **Vault Purpose** — one paragraph describing this instantiation (work or personal). Agent uses this to calibrate domain context.

2. **Folder Structure** — annotated tree matching spec above.

3. **Entity Types** — for each type:
   - Full frontmatter schema
   - Path convention
   - Template path
   - Which base views to embed and with what syntax

4. **Bases Reference** — table mapping base file → view names → embed syntax  
   e.g. `![[Meetings.base#Meetings - With Person]]`

5. **Naming Conventions**
   - Daily notes: `YYYY-MM-DD.md`
   - Meetings: `YYYY-MM-DD - Title.md`
   - Weekly summaries: `YYYY-Www.md` (ISO week, e.g. `2026-W38.md`)
   - Monthly summaries: `YYYY-MM.md`
   - All other: natural title or kebab-case
   - Frontmatter dates: always `YYYY-MM-DD`

6. **Plugin Conventions** — Templater syntax used in templates; Buttons format; Daily Notes plugin config.

7. **Agent Operations** — explicit step-by-step instructions:

   **Create daily note**
   1. Create `_daily/daily-notes/YYYY-MM-DD.md`
   2. Templater auto-applies `daily-note.template.md`
   3. Populate Overview if context available

   **Generate weekly summary**
   1. Read Mon–Fri daily notes for the target week
   2. Synthesise: highlights, decisions, tasks completed, carry-forward
   3. Write to `_generated/weekly-summaries/YYYY-Www.md` using `weekly-summary.template.md`

   **Create task**
   1. Determine project/area from context
   2. Create note in relevant `Projects/` or `Areas/` subfolder
   3. Apply `task.template.md` frontmatter; set status: todo

   **Log meeting**
   1. Create `YYYY-MM-DD - Title.md` in relevant project or area subfolder
   2. Apply `meeting.template.md`; populate attendees, project, area properties

   **Triage inbox**
   1. Read all files in `_inbox/`
   2. For each: determine type from content, move to correct folder, apply frontmatter schema
   3. Report what was moved where

   **Answer queries**
   - "What did I decide this week?" → read daily notes Decisions sections
   - "Meetings with X?" → filter Meetings.base by attendee property
   - "Blocked tasks?" → filter Tasks.base by status == blocked

8. **Work Log** — agent appends one line per significant change:
   ```
   YYYY-MM-DD — <what was created or changed>
   ```

---

## Naming Conventions Summary

| Entity | Pattern | Example |
|---|---|---|
| Daily note | `YYYY-MM-DD.md` | `2026-09-16.md` |
| Meeting | `YYYY-MM-DD - Title.md` | `2026-09-16 - Sprint Planning.md` |
| Weekly summary | `YYYY-Www.md` | `2026-W38.md` |
| Monthly summary | `YYYY-MM.md` | `2026-09.md` |
| Meeting summary | `YYYY-MM-DD - Title - summary.md` | `2026-09-16 - Sprint Planning - summary.md` |
| All others | Natural title or kebab-case | `side-project-ideas.md` |

---

## Open Questions / Future Considerations

- **Habit tracking** — not in scope for v1 but `area.template.md` can embed a dataview habits block later
- **Finance aggregation** — monthly finance summary could be agent-generated into `_generated/monthly-summaries/`
- **Work vs personal tagging** — if ever merging vaults, add `domain: work | personal` property; all bases already support adding this filter
- **Obsidian Publish** — wiki notes designed to be publishable if needed (no personal data in `Resources/Wiki/`)
