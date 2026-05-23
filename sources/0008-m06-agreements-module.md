---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - agreements
  - collaboration
  - proposals
  - rewards
---

# M06 Agreements — Collaboration Proposals

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M06 enables `joining+` members to propose collaboration on open projects. Members specify what they will do and what they expect in return.

**Status:** Live

## Proposal Submission

A collaboration proposal includes:
- `work_description` — what the member will do
- `expected_reward` — what they expect in return
- `conditions` — timing, availability, dependencies (optional)

One proposal per (project, user) combination — `UNIQUE(project_id, user_id)`.

Upsert with `onConflict: 'project_id,user_id'` prevents duplicate-key errors.

## Review and Acceptance

Admin reviews proposals. Accepting one:
1. Sets `collaboration_agreements.status = 'accepted'`
2. Spawns a new `deliverables` row with `from_agreement_id` pointing to the accepted proposal
3. Proposal appears in the Ideas column of the per-project Kanban

## Schema

```sql
CREATE TABLE collaboration_agreements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users(id),
  work_description TEXT NOT NULL,
  expected_reward TEXT NOT NULL,
  conditions TEXT,
  status TEXT DEFAULT 'pending',
    -- pending | accepted | active | rejected | completed
  admin_notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(project_id, user_id)
);
```

## Access Control

- Guest-browsable (public projects visible)
- `joining+` roles can submit proposals
- Admin reviews all proposals