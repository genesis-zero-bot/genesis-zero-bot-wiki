---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - join
  - onboarding
  - applications
  - role-system
---

# M05 Join — Application Flow and Role Journey

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M05 Join is the member onboarding flow. Admin configures application questions in the Blueprint; prospective members submit applications; admin accepts or declines.

**Status:** Live

## Application Questions

Admin sets `join_application_questions` in Blueprint step 1.4 (stored in `blueprints.answers`). M05 reads this to build the application form dynamically.

One question per line. Questions appear as q0, q1, q2... in the `applications.answers` JSONB.

## Accept Flow

Admin sets `applications.status = 'accepted'`:
1. `user_profiles.role` updated to `'joining'`
2. Member gains access to M06 (Agreements)
3. `community_joiner` achievement auto-awarded

## Role Journey

| Role | How obtained | Access gained |
|---|---|---|
| `explorer` | Creates account | Blueprint (read), member profiles |
| `joining` | Application accepted | M06 proposals, edit profile |
| `member` | First accepted agreement OR active project | Governance vote, contribution tracking, full network matching |
| `circle_lead` | Admin assigns + `lead_circles` set | Edit Blueprint sections for their circle, drag projects in their circles |
| `project_lead` | Admin assigns | Manage own projects |
| `admin` | Directly assigned | Full access everywhere |

## Schema

```sql
CREATE TABLE applications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) UNIQUE,
  answers JSONB DEFAULT '{}',
  status TEXT DEFAULT 'pending',
    -- pending | reviewing | accepted | rejected
  admin_notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

## Access Control

- Guest-browsable (see teaser, join CTA)
- `joining+` roles can view their own application status
- Admin reviews all applications at `/join`