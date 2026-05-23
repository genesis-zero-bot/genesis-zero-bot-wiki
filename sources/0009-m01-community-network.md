---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - network
  - profiles
  - matching
  - ai
---

# M01 Community Network — Profiles and AI Matching

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M01 provides profile-based discovery with AI match scoring across values, skills, personality (OCEAN + MBTI), and archetypes.

**Status:** Live

## Profile Completion

11-check scoring system:
1. avatar
2. first_name
3. headline
4. city
5. user_types
6. values_principles
7. skills
8. goals
9. 1+ active offer
10. 1+ active request
11. 1+ travel plan

Profile completion percentage displays in AppTopBar and drives the Profile Pioneer badge.

## AI Match Scoring

`computeMatch(mine, theirs)` scores two bio objects on 4 axes:

| Axis | Max Points | Method |
|---|---|---|
| Shared skills | 30 (5 each) | 6 skill categories |
| Shared interests | 20 (4 each) | interest overlap |
| OCEAN similarity | 25 | euclidean distance |
| MBTI compatibility | 25 | ideal pair / temperament tables |

Returns `{ score, reasons[] }`.

## Access Control

- Tile/list view: guest-browsable
- Match scores: visible to logged-in members viewing other profiles
- AI match page: guests see a teaser, members see full results
- Profile editing: `joining+` roles