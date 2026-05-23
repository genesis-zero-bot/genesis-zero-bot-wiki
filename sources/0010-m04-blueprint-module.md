---
type: source
status: active
updated: 2026-05-23
privacyTier: public
url: https://github.com/regentribes/tribesplatform-v2-docs
tags:
  - myconet
  - blueprint
  - community-planning
  - spark-prove-build-live
---

# M04 Blueprint — Community Planning Document

**Source:** [tribesplatform-v2-docs/ARCHITECTURE.md](https://github.com/regentribes/tribesplatform-v2-docs/blob/master/ARCHITECTURE.md)

---

## Overview

M04 Blueprint is the shared community planning document. Admin uploads and edits; all members read. AI document scanning (MiniMax M2.7) fills fields from uploaded PDFs and documents.

**Status:** Live

## SPARK -> PROVE -> BUILD -> LIVE

The Blueprint guides a community through four phases of development. Phase gates are defined in the Blueprint document itself.

## AI Document Scanning

The `/api/scan` route uses MiniMax M2.7 to extract structured data from uploaded documents:

- `CHUNK_SIZE = 5000` chars
- `CONCURRENCY = 4` parallel calls
- `temperature: 0.3`
- No system message (prevents token budget burn)
- `max_completion_tokens: 8192`
- `extractJSON()` strips `<think>...` blocks before parsing

## Key Answers Field

`blueprints.answers.join_application_questions` (text, one question per line) is read by M05 Join to build the application form dynamically. Admin edits these in Blueprint step 1.4.

## Access Control

- Read-only for all roles (including admin reads)
- Admin edit mode for Blueprint content
- Guest-browsable (teaser for non-members)