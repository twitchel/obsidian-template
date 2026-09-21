# CLAUDE.md

This file is the complete operating context for any AI agent working with this vault. A fresh session must be able to operate the vault correctly from this file alone.

## Vault Purpose

[REPLACE THIS SECTION: Describe this vault's purpose — work or personal instantiation, owner, domain. Example: "Work vault for [Name], software engineer/tech lead at [Company]. Covers engineering leadership: team management, project tracking, technical decisions, meeting notes." or "Personal vault for [Name]. Covers personal finance, side projects, learning, purchases, and relationships."]

## Folder Structure

```
Projects/          Active time-boxed work (features, side projects, initiatives)
Areas/             Ongoing responsibilities (team, finances, relationships)
Resources/         Reference material (wiki, learning, purchases, assets)
Archive/           Completed/retired content
_daily/
  daily-notes/     YYYY-MM-DD.md
_inbox/            Capture inbox — triage later
_generated/
  weekly-summaries/   YYYY-Www.md  (agent-written)
  monthly-summaries/  YYYY-MM.md   (agent-written)
  meeting-summaries/  YYYY-MM-DD - Title - summary.md  (agent-written)
_assets/
  templates/       Templater .template.md files
  bases/           .base query files
  scripts/         Templater JS scripts
  attachments/     Images, PDFs (Obsidian default paste target)
  excalidraw/      .excalidraw diagrams
  canvas/          .canvas files
```

Notes live inside Projects/, Areas/, Resources/. Type is a frontmatter property, not a folder. Agent locates entities by querying the `type` property, not traversing paths.

## Entity Types

### task
- **Path**: `Projects/<project>/` or `Areas/<area>/`
- **Template**: `_assets/templates/task.template.md`
- **Schema**:
  - `type: task`
  - `status: todo | in-progress | done | blocked`
  - `priority: p1 | p2 | p3`
  - `project: "[[Project Name]]"`
  - `area: "[[Area Name]]"`
  - `due: YYYY-MM-DD`
  - `created-from: "[[Meeting or Daily Note]]"` (optional — set when agent creates task from another note)
  - `related: []`
  - `.template-name: "[[task.template]]"`
  - `.template-version: 1.0.0`

### project
- **Path**: `Projects/<project-name>.md`
- **Template**: `_assets/templates/project.template.md` (auto via Templater folder trigger on `Projects/`)
- **Schema**:
  - `type: project`
  - `status: active | paused | complete`
  - `area: "[[Area Name]]"`
  - `start-date: YYYY-MM-DD`
  - `target-date: YYYY-MM-DD`
  - `outcome: one-line goal statement`
  - `related: []`
  - `.template-name: "[[project.template]]"`
  - `.template-version: 1.0.0`
- **Embeds**: `![[Tasks.base#Project Tasks]]`, `![[Meetings.base#Meetings - For Project]]`

### area
- **Path**: `Areas/<area-name>.md`
- **Template**: `_assets/templates/area.template.md` (auto via Templater folder trigger on `Areas/`)
- **Schema**:
  - `type: area`
  - `related: []`
  - `.template-name: "[[area.template]]"`
  - `.template-version: 1.0.0`
- **Embeds**: `![[Projects.base#Active]]`, `![[Tasks.base#Active]]`

### meeting
- **Path**: `Projects/<project>/meetings/YYYY-MM-DD - Title.md` or `Areas/<area>/meetings/YYYY-MM-DD - Title.md`
- **Template**: `_assets/templates/meeting.template.md` (manual) or `1-1-meeting.template.md` for 1:1s
- **Schema**:
  - `type: meeting`
  - `date: YYYY-MM-DD`
  - `attendees: ["[[Person Name]]"]`
  - `project: "[[Project Name]]"`
  - `area: "[[Area Name]]"`
  - `meeting-type: 1-1 | standup | planning | review | ad-hoc`
  - `related: []`
  - `.template-name: "[[meeting.template]]"`
  - `.template-version: 1.0.0`

### person
- **Path**: `Areas/People/<Person Name>.md`
- **Template**: `_assets/templates/person.template.md` (manual)
- **Schema**:
  - `type: person`
  - `relationship: colleague | direct-report | manager | friend | family | contact`
  - `org: company or group name`
  - `email:`
  - `birthday: YYYY-MM-DD`
  - `related: []`
  - `.template-name: "[[person.template]]"`
  - `.template-version: 1.0.0`
- **Embeds**: `![[Meetings.base#Meetings - With Person]]`

### learning
- **Path**: `Resources/Learning/<title>.md`
- **Template**: `_assets/templates/learning.template.md` (manual)
- **Schema**:
  - `type: learning`
  - `medium: book | course | article | podcast | video`
  - `status: want | in-progress | done`
  - `author:`
  - `url:`
  - `project: "[[Project Name]]"`
  - `related: []`
  - `.template-name: "[[learning.template]]"`
  - `.template-version: 1.0.0`

### purchase
- **Path**: `Resources/Purchases/<item-name>.md`
- **Template**: `_assets/templates/purchase.template.md` (manual)
- **Schema**:
  - `type: purchase`
  - `status: researching | decided | purchased`
  - `category: electronics | home | clothing | tools | other`
  - `price:`
  - `vendor:`
  - `decision-date: YYYY-MM-DD`
  - `related: []`
  - `.template-name: "[[purchase.template]]"`
  - `.template-version: 1.0.0`

### asset
- **Path**: `Resources/Assets/<asset-name>.md`
- **Template**: `_assets/templates/asset.template.md` (auto via Templater folder trigger on `Resources/Assets/`)
- **Schema**:
  - `type: asset`
  - `asset-type: digital | physical`
  - `status: active | inactive | disposed`
  - `category: electronics | software | vehicle | furniture | appliance | tool | subscription | domain | other`
  - `acquired-date: YYYY-MM-DD`
  - `value:`
  - `serial:`
  - `warranty-expiry: YYYY-MM-DD`
  - `location:` (physical: where stored; digital: platform/account)
  - `url:` (digital assets)
  - `vendor:`
  - `related: []`
  - `.template-name: "[[asset.template]]"`
  - `.template-version: 1.0.0`

### finance
- **Path**: `Areas/Finances/<YYYY-MM> - <description>.md`
- **Template**: `_assets/templates/finance.template.md` (manual)
- **Schema**:
  - `type: finance`
  - `period: YYYY-MM`
  - `category: income | expense | investment | savings`
  - `amount:`
  - `account:`
  - `related: []`
  - `.template-name: "[[finance.template]]"`
  - `.template-version: 1.0.0`

### wiki
- **Path**: `Resources/Wiki/<topic>.md`
- **Template**: `_assets/templates/wiki.template.md` (auto via Templater folder trigger on `Resources/Wiki/`)
- **Schema**:
  - `type: wiki`
  - `tags: []`
  - `related: []`
  - `.template-name: "[[wiki.template]]"`
  - `.template-version: 1.0.0`

### daily-note
- **Path**: `_daily/daily-notes/YYYY-MM-DD.md`
- **Template**: `_assets/templates/daily-note.template.md` (auto via Templater folder trigger)
- **Schema**:
  - `type: daily-note`
  - `date: YYYY-MM-DD`
  - `related: []`
  - `.template-name: "[[daily-note.template]]"`
  - `.template-version: 1.0.0`
- **Embeds**: `![[Tasks.base#Today's Tasks]]`, `![[Meetings.base#Meetings - This Day]]`

### weekly-summary
- **Path**: `_generated/weekly-summaries/YYYY-Www.md` (agent-written)
- **Template**: `_assets/templates/weekly-summary.template.md` (agent reference only — not auto-triggered)
- **Schema**:
  - `type: weekly-summary`
  - `week: YYYY-Www`
  - `related: []`

## Bases Reference

| Base file | View name | Embed syntax |
|---|---|---|
| `_assets/bases/Kanban.base` | Kanban | `![[Kanban.base#Kanban]]` |
| `_assets/bases/Kanban.base` | Blocked | `![[Kanban.base#Blocked]]` |
| `_assets/bases/Tasks.base` | Active | `![[Tasks.base#Active]]` |
| `_assets/bases/Tasks.base` | By Project | `![[Tasks.base#By Project]]` |
| `_assets/bases/Tasks.base` | Today's Tasks | `![[Tasks.base#Today's Tasks]]` |
| `_assets/bases/Tasks.base` | Project Tasks | `![[Tasks.base#Project Tasks]]` |
| `_assets/bases/Tasks.base` | Overdue | `![[Tasks.base#Overdue]]` |
| `_assets/bases/Tasks.base` | Done | `![[Tasks.base#Done]]` |
| `_assets/bases/Meetings.base` | All Meetings | `![[Meetings.base#All Meetings]]` |
| `_assets/bases/Meetings.base` | Meetings - With Person | `![[Meetings.base#Meetings - With Person]]` |
| `_assets/bases/Meetings.base` | Meetings - This Day | `![[Meetings.base#Meetings - This Day]]` |
| `_assets/bases/Meetings.base` | Meetings - For Project | `![[Meetings.base#Meetings - For Project]]` |
| `_assets/bases/Projects.base` | Active | `![[Projects.base#Active]]` |
| `_assets/bases/Projects.base` | All | `![[Projects.base#All]]` |
| `_assets/bases/Projects.base` | By Area | `![[Projects.base#By Area]]` |
| `_assets/bases/People.base` | All | `![[People.base#All]]` |
| `_assets/bases/People.base` | Colleagues | `![[People.base#Colleagues]]` |
| `_assets/bases/People.base` | Friends | `![[People.base#Friends]]` |
| `_assets/bases/People.base` | Family | `![[People.base#Family]]` |
| `_assets/bases/Learning.base` | In Progress | `![[Learning.base#In Progress]]` |
| `_assets/bases/Learning.base` | Want To | `![[Learning.base#Want To]]` |
| `_assets/bases/Learning.base` | Done | `![[Learning.base#Done]]` |
| `_assets/bases/Purchases.base` | Researching | `![[Purchases.base#Researching]]` |
| `_assets/bases/Purchases.base` | Decided | `![[Purchases.base#Decided]]` |
| `_assets/bases/Purchases.base` | All | `![[Purchases.base#All]]` |
| `_assets/bases/Assets.base` | All | `![[Assets.base#All]]` |
| `_assets/bases/Assets.base` | Physical | `![[Assets.base#Physical]]` |
| `_assets/bases/Assets.base` | Digital | `![[Assets.base#Digital]]` |
| `_assets/bases/Assets.base` | Warranties | `![[Assets.base#Warranties]]` |
| `_assets/bases/Assets.base` | By Category | `![[Assets.base#By Category]]` |
| `_assets/bases/Assets.base` | Related To | `![[Assets.base#Related To]]` |
| `_assets/bases/Finances.base` | By Period | `![[Finances.base#By Period]]` |
| `_assets/bases/Finances.base` | By Category | `![[Finances.base#By Category]]` |
| `_assets/bases/Finances.base` | Expenses | `![[Finances.base#Expenses]]` |
| `_assets/bases/Inbox.base` | Inbox | `![[Inbox.base#Inbox]]` |

## Naming Conventions

| Entity | Pattern | Example |
|---|---|---|
| Daily note | `YYYY-MM-DD.md` | `2026-09-18.md` |
| Meeting | `YYYY-MM-DD - Title.md` | `2026-09-18 - Sprint Planning.md` |
| Weekly summary | `YYYY-Www.md` | `2026-W38.md` |
| Monthly summary | `YYYY-MM.md` | `2026-09.md` |
| Meeting summary | `YYYY-MM-DD - Title - summary.md` | `2026-09-18 - Sprint Planning - summary.md` |
| Finance entry | `YYYY-MM - description.md` | `2026-09 - Salary.md` |
| All other notes | Natural title or kebab-case | `side-project-ideas.md` |
| All dates in frontmatter | `YYYY-MM-DD` | `2026-09-18` |

## Plugin Conventions

- **Daily Notes core plugin**: folder `_daily/daily-notes`, format `YYYY-MM-DD`, template field empty (Templater folder trigger handles it)
- **Templater**: `trigger_on_file_creation_mode: folder`; auto-applies matching template when note created in a watched folder; scripts live in `_assets/scripts/`; **folder triggers require manual configuration in Templater settings — no plugin config file is included in this template**
- **Buttons**: `type command` delegates to a command palette action (exact name, case-sensitive); `type note` always tries to create (errors if note already exists). Button action string for daily notes: `Daily Notes: Open today's daily note`
- **Bases embed syntax**: `![[BaseName.base#View Name]]` — view name must exactly match the `name:` field in the base YAML file (case-sensitive, spaces preserved)
- **Templater date syntax**:
  - Current date: `<% tp.date.now("YYYY-MM-DD") %>`
  - Date relative to file title: `<% tp.date.now("YYYY-MM-DD", -1, tp.file.title, "YYYY-MM-DD") %>`
  - ISO week: `<% tp.date.now("YYYY-[W]WW") %>`
  - File creation date: `<% tp.file.creation_date("YYYY-MM-DD") %>`

## Agent Operations

### Create daily note
1. Use Obsidian Daily Notes command ("Daily Notes: Open today's daily note") or create `_daily/daily-notes/YYYY-MM-DD.md` directly
2. Templater auto-applies `_assets/templates/daily-note.template.md` via folder trigger on creation
3. Populate the Overview section with relevant context if available

### Generate weekly summary
1. Determine ISO week: the week containing the target Monday (e.g. 2026-09-14 → W38 → filename `2026-W38.md`)
2. Read Mon–Fri daily notes for that week from `_daily/daily-notes/`
3. Synthesise content from their Overview, Decisions, and Summary sections
4. Write to `_generated/weekly-summaries/2026-W38.md` using `_assets/templates/weekly-summary.template.md` as section structure
5. Fill in actual Mon–Fri dates in the Daily Notes section

### Create task
1. Determine which project or area this task belongs to from context
2. Create note in the relevant subfolder of `Projects/` or `Areas/`
3. Apply `task.template.md` frontmatter: set `status: todo`, fill `project` or `area`, set `priority` and `due` if known
4. If creating from a meeting or daily note, set `created-from: "[[Source Note]]"` and add `related: ["[[Source Note]]"]`; also add a back-reference link to the task in the source note's `related:` property

### Log meeting
1. Determine relevant project or area
2. Create `YYYY-MM-DD - Title.md` in `Projects/<project>/meetings/` or `Areas/<area>/meetings/`
3. Apply `meeting.template.md` frontmatter: set `date`, `attendees`, `project` or `area`, `meeting-type`
4. For 1:1s use `1-1-meeting.template.md` instead

### Triage inbox
1. Read all files in `_inbox/`
2. For each file: infer `type` from content and title
3. Move file to correct destination folder (Projects/, Areas/, Resources/)
4. Apply correct frontmatter schema for that type
5. Report: original filename → destination path → type assigned

### Add person
1. Create `Areas/People/<Full Name>.md`
2. Apply `person.template.md` frontmatter: set `relationship`, `org`, `email` if known
3. No Templater folder trigger for People — apply template manually or via QuickAdd macro

### Log purchase research
1. Create `Resources/Purchases/<item-name>.md`
2. Apply `purchase.template.md` frontmatter: set `status: researching`, `category`
3. Fill Options table with alternatives being compared
4. When decision made: update `status: decided`, set `decision-date`, add `related: ["[[asset note]]"]` once the asset is catalogued

### Catalogue asset
1. Create `Resources/Assets/<asset-name>.md` (Templater auto-applies `asset.template.md`)
2. Fill frontmatter: `asset-type`, `category`, `acquired-date`, `value`, `serial`, `warranty-expiry`, `location`/`url`, `vendor`
3. If asset came from a tracked purchase, link bidirectionally via `related`

### Answer common queries
- "What did I decide this week?" → read `_daily/daily-notes/` for the week, extract Decisions sections
- "Meetings with [person]?" → find `Areas/People/<Person Name>.md`, check `Meetings.base#Meetings - With Person` embed
- "Blocked tasks?" → query notes where `type == task AND status == blocked`
- "What am I researching to buy?" → check `Purchases.base#Researching`
- "Warranties expiring soon?" → check `Assets.base#Warranties` (sorted ASC by warranty-expiry)
- "What projects are active?" → check `Projects.base#Active`
- "What's in my inbox?" → read `_inbox/` directory

## Work Log

Agent appends one line per significant change:
```
YYYY-MM-DD — <description of what was created or changed>
```

2026-09-21 — Created CLAUDE.md agent operating manual
