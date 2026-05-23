---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - governance
  - proposals
  - voting
  - decision-making
---

# M09 Governance — Proposals, Voting, Discussion

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M09 Governance enables the community to make collective decisions through proposals, voting, and threaded discussion.

**Status:** Live (v3.01+)

## Decision Modes

Four decision modes are supported:

| Mode | Description |
|---|---|
| `consent` | No objections — consensus-based, anyone can raise a concern |
| `democracy` | Majority rules — votes counted, majority wins |
| `meritocracy` | Weighted by expertise — role or demonstrated competence determines influence |
| `ai` | AI-facilitated — algorithm assists but humans decide |

## Data Model

**proposals** table:
- `title`, `description` — proposal content
- `decision_mode` — consent | democracy | meritocracy | ai
- `status` — open | closed | decided
- `created_by` — member who authored the proposal
- `closes_at` — optional deadline

**proposal_votes** table:
- One row per (proposal, user) combination
- `vote` field: `consent` | `concern` | `object`

**proposal_comments** table:
- Threaded discussion per proposal
- `content` — comment text

## Four Governance Layers

The platform evaluates proposals across four layers:

1. **Consent** — No objections registered. Any `object` vote blocks.
2. **Democracy** — Majority of consent votes wins.
3. **Meritocracy** — Weighted by role or demonstrated expertise.
4. **AI Facilitation** — Algorithm-assisted evaluation.

A proposal becomes **LIVE** when at least 3 of 4 layers pass.

## Voting Mechanics

- `consent` vote options: consent | concern | object
- `democracy` vote options: in favour | against | abstain
- Un-vote on re-click (toggle)
- Discussion thread: Cmd+Enter to post

## Access Control

- `explorer` — read-only (cannot vote or propose)
- `joining` and above — can vote and create proposals
- `member` role required to create new proposals