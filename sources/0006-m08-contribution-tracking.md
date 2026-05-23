---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - contribution-tracking
  - achievements
  - badges
  - reputation
---

# M08 Contribution Tracking — Achievements and Reputation

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M08 makes regenerative action visible and rewarding through points, badges, and reputation.

**Status:** Live (v3.01+)

## Achievements Catalog

Badges earned through community participation. Key badges:

| Key | Name | Earn condition |
|---|---|---|
| `profile_pioneer` | Profile Pioneer | Profile wizard reaches 100% |
| `community_joiner` | Community Joiner | Complete M05 Join and get accepted |

Profile completion scoring (11 checks):
- avatar, first_name, headline, city, user_types
- values_principles, skills, goals
- 1+ active offer, 1+ active request, 1+ travel plan

## Auto-Award System

- Profile Pioneer: `useEffect` in `/profile/edit` watches form state; on pct === 100 calls upsert with `onConflict: 'user_id,achievement_key', ignoreDuplicates: true` — idempotent, fires at most once
- Community Joiner: auto-upserted for `joining+` roles on home page load

## Schema

```sql
CREATE TABLE user_achievements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  achievement_key TEXT NOT NULL,
  achievement_name TEXT NOT NULL,
  earned_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(user_id, achievement_key)
);
```

RLS: users read their own rows; insert their own rows.

## Access Control

- All main pages are guest-browsable
- Achievements page requires `member` role or above
- Achievement catalog visible to all; earned badges personalised per user