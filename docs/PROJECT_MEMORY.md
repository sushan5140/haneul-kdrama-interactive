# Haneul K-Drama Interactive — Durable Project Memory

Last refreshed: **2026-09-11**

## Identity
- Repository: `sushan5140/haneul-kdrama-interactive`
- Vercel project: `haneul-kdrama-interactive`
- Stable live URL: https://haneul-kdrama-interactive.vercel.app
- Vercel project ID: `prj_oungX4pizKLxeyQzmnu0NdCTmmRr`
- Supabase project: `Haneul`
- Supabase ref: `uyltjaftajwkujjhuric`
- Independent from Haneul Video Lab and unrelated apps.

## Source of truth
- `index.html` is the canonical latest captured frontend.
- Project continuity should come from GitHub rather than old chats or sandbox state.
- Read `AGENTS.md`, `CURRENT_STATE.md`, `PROJECT_RULES.md`, and `CONTINUATION.md` before implementation.

## Locked rules
- preserve approved light Haneul UI
- same story/player structure unless functionality requires change
- no dark Netflix redesign
- keep Seoyeon (서연) and Minjun (민준) consistent
- same Vercel project/domain
- verify before claiming success
- local progress survives cloud problems
- RLS enforces ownership
- never commit secrets
- do not mix project contexts/data

## Current backend reality
The old handoff statement “no dedicated Haneul Supabase yet” is obsolete.

Live project observed:
- `Haneul`
- `uyltjaftajwkujjhuric`
- `ap-south-1`
- `ACTIVE_HEALTHY`

Learner tables:
- profiles
- episode_progress
- saved_lines
- vocabulary_state
- line_state
- review_history
- activity_days

RLS is enabled on all learner tables. Policies restrict authenticated rows with `auth.uid() = user_id`.

The frontend contains email/password auth, local-first fallback, cloud sync/hydration, learner-state sync, and activity tracking.

## Implemented but still must be tested end-to-end
- fresh signup
- returning login
- logout/login
- refresh restoration
- first local-to-cloud backup
- cross-device hydration
- account switching
- strict user-data isolation
- review-history behavior

## Approved continuation
Backend/account:
1. verify deployed source
2. auth matrix
3. persistence/cross-device sync
4. isolation
5. reproducible backend docs
6. Account/Sync polish
7. My Drama Journey
8. Personalized Review

Visual/story:
1. Level 2 visual continuity
2. scene image switching
3. controlled branching
4. review/journey polish
5. Level 3 later
