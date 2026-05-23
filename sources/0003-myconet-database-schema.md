---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - database
  - schema
  - supabase
  - postgresql
  - rls
---

# MyCoNet v2 — Database Schema

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Core Tables

### user_profiles

Extends Supabase auth users. Created by trigger on signup.

```sql
CREATE TABLE user_profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  first_name TEXT,
  username TEXT UNIQUE,
  avatar_url TEXT,
  role TEXT DEFAULT 'explorer',
    -- explorer | joining | member | circle_lead | project_lead | admin
  bio TEXT,
  location TEXT,
  user_types TEXT[],
  personality_details JSONB,
    -- { myersBriggs, ocean: { openness, conscientiousness, ... } }
  archetypes JSONB,
    -- { primary, secondary, description }
  offers JSONB[],
    -- [{ category, title, description }]
  seeks JSONB[],
  places_traveling TEXT,
  lead_circles TEXT[] DEFAULT '{}',
    -- ecology | hardware | humanware | economy | tech
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

### blueprints

One shared community document. Admin creates and edits; all members read.

```sql
CREATE TABLE blueprints (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  answers JSONB DEFAULT '{}',
    -- all wizard field values, keyed by field id
  flags JSONB DEFAULT '{}',
    -- step completion / gate-check flags
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

**Key field:** `answers.join_application_questions` (text, one question per line) — read by M05 Join to build the application form.

### applications

Join applications submitted by prospective members.

```sql
CREATE TABLE applications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) UNIQUE,
  answers JSONB DEFAULT '{}',
    -- keyed q0, q1, q2... matching blueprint questions
  status TEXT DEFAULT 'pending',
    -- pending | reviewing | accepted | rejected
  admin_notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

**Accept flow:** Admin sets `status = 'accepted'` -> `user_profiles.role` updated to `'joining'` -> member gains M06 access.

### projects

Community projects in M07 Operations.

```sql
CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  status TEXT DEFAULT 'pending',
    -- pending | backlog | in_progress | review | done | paused
  open_for_collaborators BOOLEAN DEFAULT false,
  circle TEXT,
    -- ecology | hardware | humanware | economy | tech
  needs TEXT[],
  deadline DATE,
  sprint_name TEXT,
  created_by UUID REFERENCES auth.users(id),
  lead_user_id UUID REFERENCES auth.users(id),
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

Kanban columns: Ideas -> Backlog -> In Progress -> Review -> Done.
`circle` drives visual badge and circle-lead permission check.

### project_updates

Timeline updates posted by admin for each project.

```sql
CREATE TABLE project_updates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users(id),
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### collaboration_agreements

Proposals submitted by `joining+` members on open projects. Accepted proposals spawn deliverables.

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

**Accept flow:** Accepting a proposal inserts a `deliverables` row with `from_agreement_id` linking back.

### deliverables

Project subtasks — the cards in Backlog/In progress/Review/Done columns on the per-project Kanban.

```sql
CREATE TABLE deliverables (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  status TEXT DEFAULT 'backlog',
    -- backlog | in_progress | review | done
  due_date DATE,
  assignee_id UUID REFERENCES auth.users(id),
  progress NUMERIC DEFAULT 0,
  from_agreement_id UUID REFERENCES collaboration_agreements(id) ON DELETE SET NULL,
    -- Set when spawned by accepting a collaboration proposal. Null for ad-hoc subtasks.
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### user_bio

Extended profile fields (bio wizard steps 2-5).

```sql
CREATE TABLE user_bio (
  user_id UUID PRIMARY KEY REFERENCES auth.users(id),
  values_principles TEXT,
  skills TEXT[],
  goals TEXT,
  places_traveling JSONB[],
    -- [{ location, from_date, to_date, notes }]
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);
```

### user_offers / user_requests

Offer and request items from profile wizard steps 6-7.

```sql
CREATE TABLE user_offers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE user_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  request_text TEXT NOT NULL,
  category TEXT,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### user_achievements

Earned badges (M08 Contribution Tracking).

```sql
CREATE TABLE user_achievements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  achievement_key TEXT NOT NULL,
    -- e.g. 'profile_pioneer'
  achievement_name TEXT NOT NULL,
  earned_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(user_id, achievement_key)
);
```

### proposals

Governance proposals (M09).

```sql
CREATE TABLE proposals (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  decision_mode TEXT DEFAULT 'consent',
    -- consent | democracy | meritocracy | ai
  status TEXT DEFAULT 'open',
    -- open | closed | decided
  created_by UUID REFERENCES auth.users(id),
  closes_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

### proposal_votes

Individual votes on governance proposals.

```sql
CREATE TABLE proposal_votes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  proposal_id UUID REFERENCES proposals(id) ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  vote TEXT NOT NULL,
    -- consent | concern | object
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(proposal_id, user_id)
);
```

### proposal_comments

Discussion thread per governance proposal.

```sql
CREATE TABLE proposal_comments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  proposal_id UUID REFERENCES proposals(id) ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

## Access Control

**Page level:** Every page calls `supabase.auth.getUser()`. Guests see public content; members see role-scoped panels.

**Database level:** Row-level security policies enforce the same rules at the data layer.

```sql
CREATE OR REPLACE FUNCTION is_admin()
RETURNS boolean LANGUAGE sql SECURITY DEFINER STABLE AS $$
  SELECT EXISTS (
    SELECT 1 FROM user_profiles
    WHERE id = auth.uid()
    AND role IN ('admin', 'circle_lead', 'project_lead')
  );
$$;
```

| Role | Dashboard | Blueprint | Network | Agreements | Join | Admin panels |
|---|---|---|---|---|---|---|
| guest | public view | public view | public view | public view | public view | no |
| `explorer` | yes | read-only | yes | yes | apply | no |
| `joining` | yes | read-only | yes | propose | view status | no |
| `member` | yes | read-only | yes | propose | view status | no |
| `admin` | full | edit | yes | propose+review | review all | yes |