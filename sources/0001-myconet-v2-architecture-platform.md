---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - architecture
  - modules
  - platform
  - regenerative-community
---

# MyCoNet v2 — Architecture and Platform Overview

**Status:** v3.41 LIVE | **Updated:** 2026-05-23

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## What Is MyCoNet

MyCoNet is a **community operating system for regenerative neighborhoods** — a single portal where a community shares its blueprint, onboard members, manage projects, create collaboration agreements, track contributions, and decide together.

The current deployment is a **single-community portal**: one community, one URL, all users are members or prospective members of the same project.

## 14 Modules

| # | Module | Status | Notes |
|---|---|---|---|
| 00 | MyCoNet Dashboard | live | Personal home screen, role-scoped panels, guest-browsable |
| 01 | Community Network | live | Profiles, bio wizard, AI matching, tile/list view, guest-browsable |
| 02 | Neighborhood Directory | v1 link | Links to v1 tribesplatform.app, v2 cross-portal directory planned |
| 03 | Resources and Tools | v1 link | Links to v1, v2 building next |
| 04 | Blueprint | live | Shared community document, admin edits, all members read, AI scanning |
| 05 | Join | live | Application form, admin reviews, guest-browsable |
| 06 | Agreements | live | Collaboration proposals on open projects, admin reviews, guest-browsable |
| 07 | Operations | live | Five-column Project Kanban (Ideas/Backlog/In progress/Review/Done), drag-and-drop, role+circle gating, per-project Kanban with proposal-to-deliverable flow, guest-browsable |
| 08 | Contribution Tracking | live | Achievements catalog, Profile Pioneer badge at 100% profile |
| 09 | Governance | live | Proposals, consent/concern/object voting, discussion threads |
| 10 | Genesis Bot | planned | Telegram bridge for DB change requests |
| 11 | Quinn | planned | Personal AI per member |
| 12 | MycoNet Agent | planned | Community brain, coordinates all agents and modules |
| 13 | Hive | planned | Inter-community network layer |

## System Architecture

```
Browser / Client
  Next.js 16 App Router (React)
  Deployed to Cloudflare Workers via @opennextjs/cloudflare
         |
         v
Supabase
  Auth — magic link / email+password
  PostgreSQL — all module data
  Row Level Security — per-user access
  Realtime — planned for M12 agents
         |
         v
AI Layer
  MiniMax M2.7 — Blueprint doc scanning
  Claude API — planned (Quinn, MycoNet, Governance, M01 match)
```

## Code Organisation

- `web/src/app/` — Next.js route files only (thin re-exports). Do not add logic here.
- `web/src/core/` — Shared infrastructure: Supabase clients, UI primitives, shell layout, types.
- `web/src/modules/mXX-name/` — One folder per feature module. Each has its own README.

## Five Pillars

The platform organises community work around five circles:
`ecology` | `hardware` | `humanware` | `economy` | `tech`

Projects in M07 carry a `circle` field. `user_profiles.lead_circles` tracks which circles a `circle_lead` manages.

## Role System

| Role | How obtained | Access |
|---|---|---|
| `explorer` | Creates account | Blueprint (read), member profiles |
| `joining` | Join application accepted | Above + submit collaboration proposals (M06) |
| `member` | First accepted agreement or active project | Above + governance vote, contribution tracking |
| `circle_lead` | Admin assigns + `lead_circles` set | Resident access + can edit Blueprint sections for their circle + drag projects in their circles |
| `project_lead` | Admin assigns | Resident access + manage all aspects of their own projects |
| `admin` | Directly assigned | Full access everywhere |

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (App Router, Turbopack) |
| Language | TypeScript |
| Auth | Supabase Auth (@supabase/ssr) |
| Database | Supabase PostgreSQL |
| Access control | Supabase Row Level Security |
| Hosting | Cloudflare Workers (@opennextjs/cloudflare) |
| AI — Blueprint scanning | MiniMax M2.7 (via /api/scan route) |
| AI — future | Claude API (Anthropic) |
| UI components | shadcn/ui (Radix primitives + Tailwind) |
| Styling | CSS custom properties + Tailwind |
| Payments (planned) | Stripe |
| Telegram (planned) | node-telegram-bot-api |

## Deployment

Live URL: `https://myconet.correa-oscar11.workers.dev`

```bash
cd web && npm run deploy:cf
# Runs: opennextjs-cloudflare build && wrangler deploy
```

Each community clones the codebase, creates a new Supabase project, and deploys their own Cloudflare Workers instance. Community-specific identity lives in the database — no hardcoded community names in the codebase.

## Module Color Order

| # | Module | Color |
|---|---|---|
| 00 | Dashboard | Black `#18181b` |
| 01 | Community Network | Brown `#92400e` |
| 02 | Neighborhood Directory | Red `#b91c1c` |
| 03 | Resources and Tools | Orange `#ea580c` |
| 04 | Blueprint | Yellow `#92640a` |
| 05 | Join | Green `#15803d` |
| 06 | Agreements | Blue `#1d4ed8` |
| 07 | Operations | Indigo `#4338ca` |
| 08 | Contribution Tracking | Violet `#7c3aed` |
| 09 | Governance | Pink `#db2777` |
| 10 | Genesis Bot | Slate `#475569` |
| 11 | Quinn | Silver `#64748b` |
| 12 | MycoNet Agent | Gold `#b45309` |
| 13 | Hive | Brown-dark `#78350f` |

Source of truth: `src/lib/module-meta.ts`