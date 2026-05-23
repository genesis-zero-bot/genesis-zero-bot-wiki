---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - operations
  - kanban
  - projects
  - deliverables
  - collaboration-agreements
---

# M07 Operations — Kanban, Projects, Deliverables

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M07 Operations is the community project management module. It provides a five-column Kanban board for projects and their subtasks, with drag-and-drop gated by role and circle.

**Status:** Live (v3.41)

## Kanban Columns

Five columns, defined once in `@/core/lib/project-status`:

`Ideas` -> `Backlog` -> `In Progress` -> `Review` -> `Done`

`paused` exists in the schema as an off-board state.

## Two Surfaces

### Main `/ops` board

Cards = **projects**. Drag-and-drop changes `projects.status` with permission gating.

### Per-project `/ops/[id]` board

Cards = **deliverables** for that project.

The Ideas column shows **pending collaboration proposals** (visible to admin / project creator only).

Drag-and-drop works across both card types (projects and proposals).

## Proposal-to-Deliverable Flow

Dropping a proposal card from Ideas into any non-Ideas column (or clicking Accept) calls `acceptProposal()`:

1. Flips `collaboration_agreements.status` to `'accepted'`
2. Inserts a new `deliverables` row with `from_agreement_id` linking back to the proposal

## Schema

### projects table

- `status` — pending | backlog | in_progress | review | done | paused
- `circle` — ecology | hardware | humanware | economy | tech
- `open_for_collaborators` — boolean
- `needs` — text array
- `lead_user_id` — assigned project lead

### deliverables table

- `status` — backlog | in_progress | review | done
- `due_date`, `assignee_id`, `progress`
- `from_agreement_id` — set when spawned from accepted proposal

## Circle System

Five pillars drive project organisation:

| Circle | Description |
|---|---|
| `ecology` | Natural systems, food production, water |
| `hardware` | Physical infrastructure, tools, buildings |
| `humanware` | Education, health, social fabric |
| `economy` | Resource flows, exchange, contributions |
| `tech` | Digital systems, connectivity, data |

`user_profiles.lead_circles` tracks which circles a `circle_lead` can manage.

## Access Control

- All main pages are guest-browsable
- Drag-and-drop permission: admin / project_lead / circle_lead (for their assigned circles)
- Creating projects: admin only
- Proposing collaboration: `joining+` roles on open projects