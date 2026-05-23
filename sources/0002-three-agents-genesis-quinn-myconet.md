---
type: source
status: planned
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - agents
  - genesis
  - quinn
  - myconet-agent
  - telegram
  - ai
---

# Three Agents — Genesis, Quinn, MycoNet

**Status:** Planned (Phase 4) | **Source:** [tribesplatform-v2-docs/AiNSP_RnDev.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/AiNSP_RnDev.md)

---

## Overview

Three AI agents coordinate the MyCoNet platform. Each has a distinct role, direction, and presence.

| Agent | Scope | Primary function |
|---|---|---|
| Genesis | Per community | Telegram bridge — members request DB changes, leadership approves |
| Quinn | Per member | Personal AI — daily reminders, goal tracking, routing |
| MycoNet | Per community | Community brain — coordinates all agents, maintains memory |

## Genesis Bot (Module 10)

One Genesis per community. Present in both the Community Group and the Leadership Group.

**Community Group:**
- Members invoke: `@Genesis record that we decided X`
- Genesis confirms approvals back to the community
- Shares info about what is happening in the database

**Leadership Group:**
- Genesis posts approval requests directly to the group chat
- Leaders see all pending requests and approve/reject in chat

**Permission model:**
- Community members suggest database changes via Genesis
- Only Community Manager and Department Leads can approve
- All requests require human approval before any database write

**Technical:** Telegram bot via `node-telegram-bot-api`. Connects to MycoNet via REST API (`POST /genesis-requests`). Receives approval notifications via webhook or polling.

## Quinn (Module 11)

One Quinn per community member. Personal AI assistant that lives with each individual.

**What it does:**
- Daily reminders: tasks, events, agreements due, responsibilities
- Personalised community, people, and resource recommendations
- Receives input from members and routes it appropriately
- Feeds member context upward to MycoNet (preferences, confirmations, concerns)
- Pushes to members via: in-app dashboard + Telegram + WhatsApp

**Direction:** Bottom-up — individual context flows to MycoNet.

**Technical:** Single inference instance per community serves all members. Each member has their own conversation context and memory stored in PostgreSQL.

## MycoNet Agent (Module 12)

One MycoNet per community. The operating system layer that coordinates everything.

**Two components:**

**MyCoNet Dashboard (Module 00)** — Viewing layer:
- Real-time display of all 14 modules
- Community Manager and Department Leads use it as their command center
- Shows: pending decisions, active tasks, budget status, member activity, governance queue

**MycoNet Agent (Module 12)** — AI coordination layer:
- Subscribes to all module events (every database write triggers a notification)
- Maintains community memory — structured state (JSONB) + vector embeddings (pgvector)
- **MycoNet Pro** (premium tier): pushes proactive updates to leadership on every module write
- Makes cross-module connections: "Your governance decision affects your budget"
- Sends heartbeat reminders every 30 minutes for pending decisions
- Holds approval queue — all Genesis requests show here pending human approval
- Executes database writes after leadership approves

**Direction:** Top-down + bottom-up — community synthesis flows both ways.

## How They Work Together

```
MEMBER (Telegram Community Group)
  @Genesis "record that I finished the garden task"
         |
         v
  Genesis -> MycoNet API -> MycoNet Dashboard (pending queue)
  Genesis -> posts to Leadership Group (approval request)
         |
         v
  Leader approves in chat OR dashboard
         |
         v
  MycoNet -> writes database
  MycoNet -> messages Leadership Group (confirms write)
  MycoNet -> notifies Genesis
         |
         v
  Genesis confirms in Community Group: "Recorded"
         |
         v
  MycoNet Pro -> pushes update to Leadership Group
         |
         v
  Quinn -> notifies affected members
```

**Heartbeat:** Every 30 minutes, MycoNet Pro reminds leadership of all pending decisions until resolved.

## Event Types

| Event | Triggered By | Who Receives |
|---|---|---|
| `task.created` | Module 07 | MycoNet Pro -> Leadership Group |
| `task.completed` | Module 07 | MycoNet Pro -> Leadership Group, Quinn -> assignee |
| `member.joined` | Module 05 | MycoNet Pro -> Leadership Group, Quinn -> member |
| `governance.vote_started` | Module 09 | MycoNet Pro -> Leadership Group, Quinn -> affected members |
| `agreement.signed` | Module 06 | MycoNet Pro -> Leadership Group |
| `genesis.request.pending` | Genesis | MycoNet Dashboard (pending queue) |
| `genesis.request.approved` | Leader (chat or dashboard) | Genesis -> Community Group, MycoNet -> Leadership Group |
| `genesis.request.rejected` | Leader (chat or dashboard) | Genesis -> Community Group |

## Two-Group Telegram Architecture

**Community Group (Full Community):**
- All community members + Genesis bot
- Members make requests via @Genesis, receive confirmations
- Genesis shares DB activity info

**Leadership Group (Private):**
- Community Manager + Department Leads + MycoNet bot + Genesis
- MyCoNet Dashboard open as control center
- MycoNet Pro pushes proactive updates on every module write
- Genesis posts approval requests

## Open Technical Questions

1. **Event schema** — JSON schema for all events, schema versioning ownership
2. **MycoNet hosting** — per-community self-hosted OR multi-tenant SaaS
3. **Quinn hosting** — one inference server serves all members per community
4. **Genesis security** — rate limiting, member verification
5. **Offline handling** — what happens when MycoNet goes down
6. **Vector DB** — pgvector in Postgres OR separate Pinecone/Milvus
7. **Genesis + MycoNet API** — REST, Webhooks, or message queue