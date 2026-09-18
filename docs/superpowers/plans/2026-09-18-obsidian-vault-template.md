# Obsidian Vault Template Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a complete Obsidian vault template repository implementing PARA structure with type-as-property entity model, full base query files, Templater templates, and comprehensive CLAUDE.md for agent-first workflows.

**Architecture:** PARA folders (Projects/Areas/Resources/Archive) contain all notes; entity type is a frontmatter `type` property, not a folder. Bases query by type across the whole vault. `_assets/`, `_daily/`, `_inbox/`, `_generated/` are meta/system dirs prefixed with `_`.

**Tech Stack:** Obsidian markdown vault, YAML frontmatter, Obsidian Bases (.base files), Templater plugin, Buttons plugin, obsidian-git.

**Spec:** `docs/superpowers/specs/2026-09-15-obsidian-vault-template-design.md`

## Global Constraints

- All frontmatter dates use `YYYY-MM-DD` format
- Every entity note has `type` as first frontmatter property and `related: []` as last user property before `.template-name`
- `.template-name` and `.template-version` always present in frontmatter
- `_` prefix on system dirs (`_assets`, `_daily`, `_inbox`, `_generated`)
- Attachment default path: `_assets/attachments`
- Templater templates folder: `_assets/templates`
- Templater scripts folder: `_assets/scripts`
- Daily Notes folder: `_daily/daily-notes`, format: `YYYY-MM-DD`
- Base view names must exactly match embed syntax used in templates (case-sensitive)

---

### Task 1: Repository scaffold and directory structure

**Files:**
- Create: `.gitignore`
- Create: `Projects/.gitkeep`
- Create: `Areas/.gitkeep`
- Create: `Resources/Wiki/.gitkeep`
- Create: `Resources/Learning/.gitkeep`
- Create: `Resources/Purchases/.gitkeep`
- Create: `Resources/Assets/.gitkeep`
- Create: `Archive/.gitkeep`
- Create: `_daily/daily-notes/.gitkeep`
- Create: `_inbox/.gitkeep`
- Create: `_generated/weekly-summaries/.gitkeep`
- Create: `_generated/monthly-summaries/.gitkeep`
- Create: `_generated/meeting-summaries/.gitkeep`
- Create: `_assets/templates/.gitkeep`
- Create: `_assets/bases/.gitkeep`
- Create: `_assets/scripts/.gitkeep`
- Create: `_assets/attachments/.gitkeep`
- Create: `_assets/excalidraw/.gitkeep`
- Create: `_assets/canvas/.gitkeep`

**Interfaces:**
- Produces: directory structure all subsequent tasks write into

- [ ] **Step 1: Create directory structure**

```bash
mkdir -p Projects Areas \
  Resources/Wiki Resources/Learning Resources/Purchases Resources/Assets \
  Archive \
  _daily/daily-notes \
  _inbox \
  _generated/weekly-summaries _generated/monthly-summaries _generated/meeting-summaries \
  _assets/templates _assets/bases _assets/scripts \
  _assets/attachments _assets/excalidraw _assets/canvas
```

- [ ] **Step 2: Create .gitkeep files**

```bash
find Projects Areas Resources Archive _daily _inbox _generated _assets \
  -type d -exec touch {}/.gitkeep \;
```

- [ ] **Step 3: Create .gitignore**

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/plugins/obsidian-livesync/
.trash/
*.tmp
```

- [ ] **Step 4: Verify structure**

```bash
find . -type d | sort | grep -v '.git'
```
Expected output includes: `./Projects`, `./Areas`, `./Resources/Wiki`, `./Resources/Learning`, `./Resources/Purchases`, `./Resources/Assets`, `./Archive`, `./_daily/daily-notes`, `./_inbox`, `./_generated/weekly-summaries`, `./_generated/monthly-summaries`, `./_generated/meeting-summaries`, `./_assets/templates`, `./_assets/bases`, `./_assets/scripts`, `./_assets/attachments`, `./_assets/excalidraw`, `./_assets/canvas`

- [ ] **Step 5: Commit**

```bash
git add .gitignore Projects/ Areas/ Resources/ Archive/ _daily/ _inbox/ _generated/ _assets/
git commit -m "chore: scaffold directory structure"
```

---

### Task 2: Obsidian plugin configuration

**Files:**
- Create: `.obsidian/app.json`
- Create: `.obsidian/core-plugins.json`
- Create: `.obsidian/community-plugins.json`
- Create: `.obsidian/daily-notes.json`
- Create: `.obsidian/plugins/templater-obsidian/data.json`

**Interfaces:**
- Produces: vault config that Obsidian reads on open; Templater folder triggers; Daily Notes path

- [ ] **Step 1: Create .obsidian directory**

```bash
mkdir -p .obsidian/plugins/templater-obsidian
```

- [ ] **Step 2: Create app.json**

```json
{
  "attachmentFolderPath": "_assets/attachments",
  "alwaysUpdateLinks": true,
  "readableLineLength": false,
  "promptDelete": false,
  "showLineNumber": true
}
```
Write to `.obsidian/app.json`.

- [ ] **Step 3: Create core-plugins.json**

```json
{
  "file-explorer": true,
  "global-search": true,
  "switcher": true,
  "graph": true,
  "backlink": true,
  "canvas": true,
  "outgoing-link": true,
  "tag-pane": true,
  "properties": true,
  "page-preview": true,
  "daily-notes": true,
  "templates": false,
  "note-composer": true,
  "command-palette": true,
  "slash-command": true,
  "editor-status": true,
  "bookmarks": true,
  "outline": true,
  "word-count": true,
  "file-recovery": true
}
```
Write to `.obsidian/core-plugins.json`.

- [ ] **Step 4: Create community-plugins.json**

```json
[
  "obsidian-git",
  "omnisearch",
  "obsidian-excalidraw-plugin",
  "templater-obsidian",
  "obsidian-livesync",
  "buttons",
  "kanban-bases-view",
  "periodic-notes",
  "quickadd",
  "dataview",
  "calendar",
  "cmdr",
  "obsidian-local-rest-api",
  "smart-connections"
]
```
Write to `.obsidian/community-plugins.json`.

- [ ] **Step 5: Create daily-notes.json**

```json
{
  "folder": "_daily/daily-notes",
  "template": "",
  "format": "YYYY-MM-DD"
}
```
Write to `.obsidian/daily-notes.json`.

- [ ] **Step 6: Create Templater config**

```json
{
  "data_version": 2,
  "command_timeout": 5,
  "templates_folder": "_assets/templates",
  "templates_pairs": [],
  "trigger_on_file_creation_mode": "folder",
  "auto_jump_to_cursor": false,
  "jump_to_cursor_after_file_name": false,
  "shell_path": "",
  "user_scripts_folder": "_assets/scripts",
  "folder_templates": [
    {
      "folder": "_daily/daily-notes",
      "template": "_assets/templates/daily-note.template.md"
    },
    {
      "folder": "Projects",
      "template": "_assets/templates/project.template.md"
    },
    {
      "folder": "Areas",
      "template": "_assets/templates/area.template.md"
    },
    {
      "folder": "Resources/Wiki",
      "template": "_assets/templates/wiki.template.md"
    },
    {
      "folder": "Resources/Assets",
      "template": "_assets/templates/asset.template.md"
    }
  ],
  "file_templates": [],
  "syntax_highlighting": true,
  "syntax_highlighting_mobile": false,
  "enabled_templates_hotkeys": [],
  "startup_templates": [],
  "intellisense_render": "1",
  "ignore_folders_on_creation": []
}
```
Write to `.obsidian/plugins/templater-obsidian/data.json`.

- [ ] **Step 7: Verify JSON validity**

```bash
for f in .obsidian/app.json .obsidian/core-plugins.json .obsidian/community-plugins.json \
          .obsidian/daily-notes.json .obsidian/plugins/templater-obsidian/data.json; do
  python3 -c "import json; json.load(open('$f')); print('OK: $f')"
done
```
Expected: `OK:` for every file, no errors.

- [ ] **Step 8: Commit**

```bash
git add .obsidian/
git commit -m "chore: add obsidian plugin configuration"
```

---

### Task 3: Base query files

**Files:**
- Create: `_assets/bases/Kanban.base`
- Create: `_assets/bases/Tasks.base`
- Create: `_assets/bases/Meetings.base`
- Create: `_assets/bases/Projects.base`
- Create: `_assets/bases/People.base`
- Create: `_assets/bases/Learning.base`
- Create: `_assets/bases/Purchases.base`
- Create: `_assets/bases/Finances.base`
- Create: `_assets/bases/Assets.base`
- Create: `_assets/bases/Inbox.base`

**Interfaces:**
- Consumes: entity notes with `type` frontmatter property (created by templates in Tasks 4–7)
- Produces: queryable views embedded in templates and Home.md; view names used as embed anchors

- [ ] **Step 1: Create Kanban.base**

```yaml
filters:
  and:
    - type == "task"
views:
  - type: board
    name: Kanban
    groupBy: status
    order:
      - file.name
      - priority
      - project
      - due
  - type: table
    name: Blocked
    filters:
      and:
        - status == "blocked"
    order:
      - file.name
      - priority
      - project
      - area
      - due
```

- [ ] **Step 2: Create Tasks.base**

```yaml
filters:
  and:
    - type == "task"
views:
  - type: table
    name: Active
    filters:
      and:
        - status != "done"
    order:
      - file.name
      - status
      - priority
      - project
      - area
      - due
    sort:
      - property: priority
        direction: ASC
      - property: due
        direction: ASC
  - type: table
    name: By Project
    filters:
      and:
        - status != "done"
    order:
      - file.name
      - status
      - priority
      - due
    sort:
      - property: project
        direction: ASC
      - property: priority
        direction: ASC
  - type: table
    name: Overdue
    filters:
      and:
        - status != "done"
    order:
      - file.name
      - priority
      - due
      - project
    sort:
      - property: due
        direction: ASC
  - type: table
    name: Done
    filters:
      and:
        - status == "done"
    order:
      - file.name
      - project
      - file.mtime
    sort:
      - property: file.mtime
        direction: DESC
  - type: table
    name: Today's Tasks
    filters:
      and:
        - note["created-from"] == link(file(this))
    order:
      - file.name
      - status
      - priority
      - due
  - type: table
    name: Project Tasks
    filters:
      and:
        - note.project == link(file(this))
        - status != "done"
    order:
      - file.name
      - status
      - priority
      - due
```

Note: The `Overdue` view above lists tasks with a due date sorted oldest-first; Obsidian Bases date comparison (`due < today`) may not be supported — verify in Obsidian after opening vault and add a Dataview block as fallback if needed.

- [ ] **Step 3: Create Meetings.base**

```yaml
filters:
  and:
    - type == "meeting"
views:
  - type: table
    name: All Meetings
    order:
      - date
      - file.name
      - meeting-type
      - attendees
    sort:
      - property: date
        direction: DESC
  - type: table
    name: Meetings - With Person
    filters:
      and:
        - attendees.contains(link(file(this)))
    order:
      - date
      - file.name
      - meeting-type
    sort:
      - property: date
        direction: DESC
    limit: 10
  - type: table
    name: Meetings - This Day
    filters:
      and:
        - date == file(this).name
    order:
      - file.name
      - meeting-type
      - attendees
    sort:
      - property: attendees
        direction: ASC
  - type: table
    name: Meetings - For Project
    filters:
      and:
        - note.project == link(file(this))
    order:
      - date
      - file.name
      - meeting-type
      - attendees
    sort:
      - property: date
        direction: DESC
```

Note: `Meetings - This Day` filter `date == file(this).name` assumes the daily note file name is `YYYY-MM-DD` and meeting `date` property is also `YYYY-MM-DD`. Verify this filter works in Obsidian after opening the vault; if not, alternative is `date == tp.file.title` which requires Dataview.

- [ ] **Step 4: Create Projects.base**

```yaml
filters:
  and:
    - type == "project"
views:
  - type: table
    name: Active
    filters:
      and:
        - status == "active"
    order:
      - file.name
      - area
      - target-date
      - outcome
    sort:
      - property: target-date
        direction: ASC
  - type: table
    name: All
    order:
      - file.name
      - status
      - area
      - target-date
    sort:
      - property: status
        direction: ASC
      - property: target-date
        direction: ASC
  - type: table
    name: By Area
    order:
      - file.name
      - status
      - target-date
    sort:
      - property: area
        direction: ASC
      - property: status
        direction: ASC
```

- [ ] **Step 5: Create People.base**

```yaml
filters:
  and:
    - type == "person"
views:
  - type: table
    name: All
    order:
      - file.name
      - relationship
      - org
      - email
    sort:
      - property: file.name
        direction: ASC
  - type: table
    name: Colleagues
    filters:
      and:
        - relationship == "colleague"
    order:
      - file.name
      - org
      - email
    sort:
      - property: file.name
        direction: ASC
  - type: table
    name: Personal
    filters:
      and:
        - relationship == "friend"
    order:
      - file.name
      - relationship
      - birthday
    sort:
      - property: file.name
        direction: ASC
```

Note: Obsidian Bases may not support `.in(["a","b"])` multi-value filtering. Colleagues and Personal views above use single-value filters as a starting point. Add additional views per relationship value (direct-report, manager, family) as needed once vault is open, or use Dataview for multi-value relationship filtering.

- [ ] **Step 6: Create Learning.base**

```yaml
filters:
  and:
    - type == "learning"
views:
  - type: table
    name: In Progress
    filters:
      and:
        - status == "in-progress"
    order:
      - file.name
      - medium
      - author
      - project
  - type: table
    name: Want To
    filters:
      and:
        - status == "want"
    order:
      - file.name
      - medium
      - author
      - file.ctime
    sort:
      - property: file.ctime
        direction: DESC
  - type: table
    name: Done
    filters:
      and:
        - status == "done"
    order:
      - file.name
      - medium
      - author
    sort:
      - property: file.mtime
        direction: DESC
```

- [ ] **Step 7: Create Purchases.base**

```yaml
filters:
  and:
    - type == "purchase"
views:
  - type: table
    name: Researching
    filters:
      and:
        - status == "researching"
    order:
      - file.name
      - category
      - price
      - vendor
    sort:
      - property: file.ctime
        direction: DESC
  - type: table
    name: Decided
    filters:
      and:
        - status == "decided"
    order:
      - file.name
      - category
      - price
      - vendor
      - decision-date
    sort:
      - property: decision-date
        direction: DESC
  - type: table
    name: All
    order:
      - file.name
      - status
      - category
      - price
      - vendor
    sort:
      - property: file.mtime
        direction: DESC
```

- [ ] **Step 8: Create Finances.base**

```yaml
filters:
  and:
    - type == "finance"
views:
  - type: table
    name: By Period
    order:
      - period
      - file.name
      - category
      - amount
      - account
    sort:
      - property: period
        direction: DESC
  - type: table
    name: By Category
    order:
      - category
      - file.name
      - amount
      - period
    sort:
      - property: category
        direction: ASC
  - type: table
    name: Expenses
    filters:
      and:
        - category == "expense"
    order:
      - period
      - file.name
      - amount
      - account
    sort:
      - property: period
        direction: DESC
```

- [ ] **Step 9: Create Assets.base**

```yaml
filters:
  and:
    - type == "asset"
views:
  - type: table
    name: All
    order:
      - file.name
      - asset-type
      - category
      - status
      - value
      - vendor
    sort:
      - property: category
        direction: ASC
      - property: file.name
        direction: ASC
  - type: table
    name: Physical
    filters:
      and:
        - asset-type == "physical"
    order:
      - file.name
      - category
      - status
      - value
      - location
      - warranty-expiry
    sort:
      - property: category
        direction: ASC
  - type: table
    name: Digital
    filters:
      and:
        - asset-type == "digital"
    order:
      - file.name
      - category
      - status
      - url
    sort:
      - property: category
        direction: ASC
  - type: table
    name: Warranties
    filters:
      and:
        - warranty-expiry != ""
    order:
      - file.name
      - warranty-expiry
      - vendor
      - value
    sort:
      - property: warranty-expiry
        direction: ASC
  - type: table
    name: By Category
    order:
      - file.name
      - asset-type
      - status
      - value
    sort:
      - property: category
        direction: ASC
  - type: table
    name: Related To
    filters:
      and:
        - related.contains(link(file(this)))
    order:
      - file.name
      - asset-type
      - category
      - status
```

Note: `Warranties` filters on `warranty-expiry != ""` rather than a date comparison; sorts ASC so soonest expiry appears first.

- [ ] **Step 10: Create Inbox.base**

```yaml
filters:
  and:
    - file.inFolder("_inbox")
views:
  - type: table
    name: Inbox
    order:
      - file.name
      - file.ctime
    sort:
      - property: file.ctime
        direction: DESC
```

- [ ] **Step 11: Verify YAML validity of all base files**

```bash
for f in _assets/bases/*.base; do
  python3 -c "import yaml; yaml.safe_load(open('$f')); print('OK: $f')"
done
```
Expected: `OK:` for all 10 files.

- [ ] **Step 12: Verify view names match embed syntax used in templates (cross-reference)**

```bash
# View names that must exist exactly — these are used as embed anchors in templates
echo "Required view names:"
echo "  Tasks.base: Active, Today's Tasks, Project Tasks, Overdue, Done, By Project"
echo "  Meetings.base: All Meetings, Meetings - With Person, Meetings - This Day, Meetings - For Project"
echo "  Projects.base: Active, All, By Area"
echo "  Kanban.base: Kanban, Blocked"
echo "  People.base: All"
echo "  Learning.base: In Progress, Want To, Done"
echo "  Purchases.base: Researching, Decided, All"
echo "  Assets.base: All, Physical, Digital, Warranties, By Category, Related To"
echo "  Finances.base: By Period, By Category, Expenses"
echo "  Inbox.base: Inbox"
grep -h "name:" _assets/bases/*.base | sort
```
Expected: all required names appear in grep output.

- [ ] **Step 13: Commit**

```bash
git add _assets/bases/
git commit -m "feat: add base query files"
```

---

### Task 4: Daily-note and weekly-summary templates

**Files:**
- Create: `_assets/templates/daily-note.template.md`
- Create: `_assets/templates/weekly-summary.template.md`

**Interfaces:**
- Consumes: `Tasks.base#Today's Tasks`, `Meetings.base#Meetings - This Day`
- Produces: daily note structure; weekly summary structure for agent use

- [ ] **Step 1: Create daily-note.template.md**

```markdown
---
type: daily-note
date: <% tp.date.now("YYYY-MM-DD", 0, tp.file.title, "YYYY-MM-DD") %>
related: []
.template-name: "[[daily-note.template]]"
.template-version: 1.0.0
---
[<< <% tp.date.now("YYYY-MM-DD", -1, tp.file.title, "YYYY-MM-DD") %>](_daily/daily-notes/<% tp.date.now("YYYY-MM-DD", -1, tp.file.title, "YYYY-MM-DD") %>) | [<% tp.date.now("YYYY-MM-DD", 1, tp.file.title, "YYYY-MM-DD") %> >>](_daily/daily-notes/<% tp.date.now("YYYY-MM-DD", 1, tp.file.title, "YYYY-MM-DD") %>)

# Overview


# Decisions


# Tasks
![[Tasks.base#Today's Tasks]]

# Meetings
![[Meetings.base#Meetings - This Day]]

# Summary
```

- [ ] **Step 2: Create weekly-summary.template.md**

This template is used by the agent as a structure reference — not auto-triggered by Templater.

```markdown
---
type: weekly-summary
week: <% tp.file.title %>
related: []
.template-name: "[[weekly-summary.template]]"
.template-version: 1.0.0
---
# Week <% tp.file.title %>

[<< Previous week](_generated/weekly-summaries/<% tp.date.now("YYYY-[W]WW", -7, tp.file.title.replace("W",""), "YYYY-W") %>) | [Next week >>](_generated/weekly-summaries/<% tp.date.now("YYYY-[W]WW", 7, tp.file.title.replace("W",""), "YYYY-W") %>)

## Highlights


## Work


## Personal


## Decisions


## Carry Forward


## Daily Notes
- Monday: [[_daily/daily-notes/YYYY-MM-DD]]
- Tuesday: [[_daily/daily-notes/YYYY-MM-DD]]
- Wednesday: [[_daily/daily-notes/YYYY-MM-DD]]
- Thursday: [[_daily/daily-notes/YYYY-MM-DD]]
- Friday: [[_daily/daily-notes/YYYY-MM-DD]]
```

Note: agent replaces the `YYYY-MM-DD` placeholders in the Daily Notes section with the actual dates for that week when generating a summary.

- [ ] **Step 3: Verify frontmatter properties present**

```bash
grep -q "type: daily-note" _assets/templates/daily-note.template.md && echo "OK: daily-note type"
grep -q "related: \[\]" _assets/templates/daily-note.template.md && echo "OK: daily-note related"
grep -q ".template-name" _assets/templates/daily-note.template.md && echo "OK: daily-note template-name"
grep -q "type: weekly-summary" _assets/templates/weekly-summary.template.md && echo "OK: weekly-summary type"
```
Expected: all 4 `OK:` lines.

- [ ] **Step 4: Commit**

```bash
git add _assets/templates/daily-note.template.md _assets/templates/weekly-summary.template.md
git commit -m "feat: add daily note and weekly summary templates"
```

---

### Task 5: Project, Area, Resource core templates

**Files:**
- Create: `_assets/templates/project.template.md`
- Create: `_assets/templates/area.template.md`
- Create: `_assets/templates/wiki.template.md`
- Create: `_assets/templates/asset.template.md`

**Interfaces:**
- Consumes: `Tasks.base#Project Tasks`, `Meetings.base#Meetings - For Project`, `Projects.base#Active`, `Tasks.base#Active`, `Assets.base#Related To`
- Produces: Templater folder-triggered templates for `Projects/`, `Areas/`, `Resources/Wiki/`, `Resources/Assets/`

- [ ] **Step 1: Create project.template.md**

```markdown
---
type: project
status: active
area: 
start-date: <% tp.date.now("YYYY-MM-DD") %>
target-date: 
outcome: 
related: []
.template-name: "[[project.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Overview


## Goals


## Tasks
![[Tasks.base#Project Tasks]]

## Meetings
![[Meetings.base#Meetings - For Project]]
```

- [ ] **Step 2: Create area.template.md**

```markdown
---
type: area
related: []
.template-name: "[[area.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Overview


## Active Projects
![[Projects.base#Active]]

## Active Tasks
![[Tasks.base#Active]]
```

- [ ] **Step 3: Create wiki.template.md**

```markdown
---
type: wiki
tags: []
related: []
.template-name: "[[wiki.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>


## Related
```

- [ ] **Step 4: Create asset.template.md**

```markdown
---
type: asset
asset-type: 
status: active
category: 
acquired-date: 
value: 
serial: 
warranty-expiry: 
location: 
url: 
vendor: 
related: []
.template-name: "[[asset.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Details


## Notes


## Related
![[Assets.base#Related To]]
```

- [ ] **Step 5: Verify all four templates**

```bash
for f in project area wiki asset; do
  grep -q "type:" _assets/templates/${f}.template.md && echo "OK type: $f"
  grep -q "related: \[\]" _assets/templates/${f}.template.md && echo "OK related: $f"
  grep -q ".template-name" _assets/templates/${f}.template.md && echo "OK template-name: $f"
done
```
Expected: 12 `OK` lines.

- [ ] **Step 6: Commit**

```bash
git add _assets/templates/project.template.md _assets/templates/area.template.md \
        _assets/templates/wiki.template.md _assets/templates/asset.template.md
git commit -m "feat: add project, area, wiki and asset templates"
```

---

### Task 6: People and meeting templates

**Files:**
- Create: `_assets/templates/person.template.md`
- Create: `_assets/templates/meeting.template.md`
- Create: `_assets/templates/1-1-meeting.template.md`

**Interfaces:**
- Consumes: `Meetings.base#Meetings - With Person`, `Tasks.base#Project Tasks`
- Produces: person notes with meeting history embed; meeting notes with task and transcript sections

- [ ] **Step 1: Create person.template.md**

```markdown
---
type: person
relationship: 
org: 
email: 
birthday: 
related: []
.template-name: "[[person.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Meetings
![[Meetings.base#Meetings - With Person]]

## Overview


## Notes
```

- [ ] **Step 2: Create meeting.template.md**

```markdown
---
type: meeting
date: <% tp.file.creation_date("YYYY-MM-DD") %>
attendees: []
project: 
area: 
meeting-type: 
related: []
.template-name: "[[meeting.template]]"
.template-version: 1.0.0
---
# Manual Notes

## Discussion Points


## Outcomes


# Tasks
![[Tasks.base#Project Tasks]]

# Transcript

## Transcript Summary


## Raw Transcript
```

- [ ] **Step 3: Create 1-1-meeting.template.md**

```markdown
---
type: meeting
date: <% tp.file.creation_date("YYYY-MM-DD") %>
attendees: []
project: 
area: 
meeting-type: 1-1
related: []
.template-name: "[[1-1-meeting.template]]"
.template-version: 1.0.0
---
# Manual Notes

## Personal Check-in


## Current Work & Blockers


## Career Development


## Discussion Points


## Outcomes


# Tasks
![[Tasks.base#Project Tasks]]

# Transcript

## Transcript Summary


## Raw Transcript
```

- [ ] **Step 4: Verify**

```bash
for f in person meeting 1-1-meeting; do
  grep -q "type:" _assets/templates/${f}.template.md && echo "OK type: $f"
  grep -q "related: \[\]" _assets/templates/${f}.template.md && echo "OK related: $f"
done
grep -q "Meetings - With Person" _assets/templates/person.template.md && echo "OK embed: person meetings"
grep -q "meeting-type: 1-1" _assets/templates/1-1-meeting.template.md && echo "OK: 1-1 meeting type"
```
Expected: 8 `OK` lines.

- [ ] **Step 5: Commit**

```bash
git add _assets/templates/person.template.md _assets/templates/meeting.template.md \
        _assets/templates/1-1-meeting.template.md
git commit -m "feat: add person and meeting templates"
```

---

### Task 7: Entity templates — task, learning, purchase, finance

**Files:**
- Create: `_assets/templates/task.template.md`
- Create: `_assets/templates/learning.template.md`
- Create: `_assets/templates/purchase.template.md`
- Create: `_assets/templates/finance.template.md`

**Interfaces:**
- Produces: remaining entity templates; completes full template set

- [ ] **Step 1: Create task.template.md**

```markdown
---
type: task
status: todo
priority: 
project: 
area: 
due: 
created-from: 
related: []
.template-name: "[[task.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Background


## Requirements


## Related
```

- [ ] **Step 2: Create learning.template.md**

```markdown
---
type: learning
medium: 
status: want
author: 
url: 
project: 
related: []
.template-name: "[[learning.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Summary


## Key Takeaways


## Notes


## Related
```

- [ ] **Step 3: Create purchase.template.md**

```markdown
---
type: purchase
status: researching
category: 
price: 
vendor: 
decision-date: 
related: []
.template-name: "[[purchase.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Options

| Option | Price | Pros | Cons |
|--------|-------|------|------|
|        |       |      |      |

## Decision


## Related
```

- [ ] **Step 4: Create finance.template.md**

```markdown
---
type: finance
period: <% tp.date.now("YYYY-MM") %>
category: 
amount: 
account: 
related: []
.template-name: "[[finance.template]]"
.template-version: 1.0.0
---
# <% tp.file.title %>

## Details


## Notes
```

- [ ] **Step 5: Verify all four**

```bash
for f in task learning purchase finance; do
  grep -q "type:" _assets/templates/${f}.template.md && echo "OK type: $f"
  grep -q "related: \[\]" _assets/templates/${f}.template.md && echo "OK related: $f"
  grep -q ".template-name" _assets/templates/${f}.template.md && echo "OK template-name: $f"
done
```
Expected: 12 `OK` lines.

- [ ] **Step 6: Verify complete template set**

```bash
ls _assets/templates/*.template.md | wc -l
```
Expected: `13` (daily-note, weekly-summary, project, area, wiki, asset, meeting, 1-1-meeting, person, task, learning, purchase, finance).

- [ ] **Step 7: Commit**

```bash
git add _assets/templates/task.template.md _assets/templates/learning.template.md \
        _assets/templates/purchase.template.md _assets/templates/finance.template.md
git commit -m "feat: add task, learning, purchase and finance templates"
```

---

### Task 8: Home.md dashboard

**Files:**
- Create: `Home.md`

**Interfaces:**
- Consumes: `Kanban.base#Kanban`, `Projects.base#Active`, `Inbox.base#Inbox`
- Produces: vault entry point with daily note button and live dashboards

- [ ] **Step 1: Create Home.md**

```markdown
---
type: home
---
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

Note: the button uses `type command` delegating to Daily Notes core plugin. The exact action string `Daily Notes: Open today's daily note` must match the command palette name exactly (lowercase 'n', colon-space prefix). The Buttons plugin renders this on vault open.

- [ ] **Step 2: Verify**

```bash
grep -q "type command" Home.md && echo "OK: button type"
grep -q "Daily Notes: Open today's daily note" Home.md && echo "OK: button action"
grep -q "Inbox.base#Inbox" Home.md && echo "OK: inbox embed"
grep -q "Kanban.base#Kanban" Home.md && echo "OK: kanban embed"
grep -q "Projects.base#Active" Home.md && echo "OK: projects embed"
```
Expected: 5 `OK` lines.

- [ ] **Step 3: Commit**

```bash
git add Home.md
git commit -m "feat: add Home.md dashboard"
```

---

### Task 9: CLAUDE.md agent context

**Files:**
- Create: `CLAUDE.md`

**Interfaces:**
- Consumes: all entity schemas, base view names, template paths, naming conventions from spec
- Produces: complete agent operating manual; a fresh Claude session reads this file and can operate the vault correctly with zero other context

- [ ] **Step 1: Create CLAUDE.md**

```markdown
# CLAUDE.md

This file is the complete operating context for any AI agent working with this vault. A fresh session must be able to operate the vault correctly from this file alone.

## Vault Purpose

[REPLACE THIS SECTION: Describe this vault's purpose — work or personal instantiation, owner, domain. Example: "Work vault for [Name], software engineer/tech lead at [Company]. Covers engineering leadership: team management, project tracking, technical decisions, meeting notes." or "Personal vault for [Name]. Covers personal finance, side projects, learning, purchases, and relationships."]

## Folder Structure

\`\`\`
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
\`\`\`

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
- **Embeds**: `![[Tasks.base#Project Tasks]]`, `![[Meetings.base#Meetings - For Project]]`

### area
- **Path**: `Areas/<area-name>.md`
- **Template**: `_assets/templates/area.template.md` (auto via Templater folder trigger on `Areas/`)
- **Schema**:
  - `type: area`
  - `related: []`
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

### wiki
- **Path**: `Resources/Wiki/<topic>.md`
- **Template**: `_assets/templates/wiki.template.md` (auto via Templater folder trigger on `Resources/Wiki/`)
- **Schema**:
  - `type: wiki`
  - `tags: []`
  - `related: []`

### daily-note
- **Path**: `_daily/daily-notes/YYYY-MM-DD.md`
- **Template**: `_assets/templates/daily-note.template.md` (auto via Templater folder trigger)
- **Schema**:
  - `type: daily-note`
  - `date: YYYY-MM-DD`
  - `related: []`
- **Embeds**: `![[Tasks.base#Today's Tasks]]`, `![[Meetings.base#Meetings - This Day]]`

## Bases Reference

| Base file | View name | Embed syntax |
|---|---|---|
| `_assets/bases/Kanban.base` | Kanban | `![[Kanban.base#Kanban]]` |
| `_assets/bases/Kanban.base` | Blocked | `![[Kanban.base#Blocked]]` |
| `_assets/bases/Tasks.base` | Active | `![[Tasks.base#Active]]` |
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
| `_assets/bases/People.base` | Personal | `![[People.base#Personal]]` |
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
- **Templater**: `trigger_on_file_creation_mode: folder`; auto-applies matching template when note created in a watched folder; scripts live in `_assets/scripts/`
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
```

- [ ] **Step 2: Verify required sections present**

```bash
for section in "Vault Purpose" "Folder Structure" "Entity Types" "Bases Reference" \
               "Naming Conventions" "Plugin Conventions" "Agent Operations" "Work Log"; do
  grep -q "## $section" CLAUDE.md && echo "OK: $section" || echo "MISSING: $section"
done
```
Expected: 8 `OK:` lines, zero `MISSING:`.

- [ ] **Step 3: Verify all entity types documented**

```bash
for entity in task project area meeting person learning purchase asset finance wiki daily-note; do
  grep -q "### $entity" CLAUDE.md && echo "OK: $entity" || echo "MISSING: $entity"
done
```
Expected: 11 `OK:` lines.

- [ ] **Step 4: Commit**

```bash
git add CLAUDE.md
git commit -m "feat: add CLAUDE.md agent context"
```

---

### Task 10: README and final validation

**Files:**
- Create: `README.md`

**Interfaces:**
- Consumes: all files created in Tasks 1–9
- Produces: human-facing documentation for the GitHub template repo; final validation confirming vault is complete

- [ ] **Step 1: Create README.md**

```markdown
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

\`\`\`
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
\`\`\`
```

- [ ] **Step 2: Final file count verification**

```bash
echo "=== Templates ===" && ls _assets/templates/*.template.md | wc -l
echo "=== Bases ===" && ls _assets/bases/*.base | wc -l
echo "=== Obsidian config ===" && ls .obsidian/*.json | wc -l
echo "=== Core files ===" && ls Home.md CLAUDE.md README.md .gitignore 2>/dev/null | wc -l
```
Expected: Templates: `13`, Bases: `10`, Obsidian config: `4`, Core files: `4`.

- [ ] **Step 3: Verify all base embed anchors referenced in templates actually exist in base files**

```bash
# Check embeds used in templates exist as view names in base files
grep -rh "!\[\[.*\.base#" _assets/templates/ Home.md | \
  sed 's/.*\[\[\(.*\)#\(.*\)\]\].*/\1 → \2/' | sort -u
```
Review the output: every `BaseName.base → View Name` pair should have a matching `name: View Name` in `_assets/bases/BaseName.base`.

- [ ] **Step 4: Verify CLAUDE.md bases reference table is complete**

```bash
# All view names from base files
grep "name:" _assets/bases/*.base | sed 's/.*bases\/\(.*\)\.base:.*name: \(.*\)/\1 → \2/' | sort

# All view names in CLAUDE.md bases table
grep "^\|" CLAUDE.md | grep "\.base" | awk -F'|' '{print $2, "→", $3}' | sort
```
Both lists should cover the same set.

- [ ] **Step 5: Commit**

```bash
git add README.md
git commit -m "feat: add README and complete vault template"
```

- [ ] **Step 6: Tag release**

```bash
git tag v1.0.0
```

---

## Post-Implementation: Open in Obsidian

After completing all tasks, open the vault in Obsidian and verify manually:

1. Home.md opens without errors; button renders correctly
2. Install and enable all community plugins listed in `community-plugins.json`
3. Create a test daily note via the Home button — confirm Templater applies the template
4. Create a note in `Projects/` — confirm Templater applies `project.template.md`
5. Create a note in `Areas/` — confirm `area.template.md` applies
6. Create a note in `Resources/Wiki/` — confirm `wiki.template.md` applies
7. Create a note in `Resources/Assets/` — confirm `asset.template.md` applies
8. Open `_assets/bases/Kanban.base` — confirm it loads without errors (requires `kanban-bases-view` plugin)
9. Open `_assets/bases/Tasks.base` — confirm table views render
10. Open `_assets/bases/Meetings.base` — confirm `Meetings - This Day` filter works correctly on a daily note
11. Check `Meetings - With Person` on a test person note

Note any base filters that require adjustment (date comparisons, multi-value `in()` filters) and add Dataview fallback blocks as needed.
