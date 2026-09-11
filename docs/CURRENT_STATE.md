# Current State

Last refreshed: **2026-09-11**

## Production
- Stable URL: https://haneul-kdrama-interactive.vercel.app
- Vercel project: `haneul-kdrama-interactive`
- Project ID: `prj_oungX4pizKLxeyQzmnu0NdCTmmRr`
- Team ID: `team_2qP7AnUVZ2NnshuJiNVh464v`
- Latest observed production deployment: `dpl_Dzy8nogfmX9bmEJBVK5dYkJjvdsV`
- State: `READY`
- Target: `production`
- Stable production URL returned HTTP 200 during continuation verification on 2026-09-11.

READY plus HTTP 200 proves Vercel served the deployment; it does not by itself prove all auth/sync behavior.

## Canonical source
`index.html` is the latest captured Library build from:
- `/Haneul KDrama Build/index-updated.html`
- Library version observed: `23`
- Captured 2026-09-11

The GitHub blob matches that source exactly except for a single trailing newline byte.

## Product state
- Level 1: 6 episodes
- Level 2: 6 episodes
- recurring leads: Seoyeon (`서연`) and Minjun (`민준`)
- current source includes scene learning, reply choices, vocabulary, grammar, use cases, audio, saved dialogue, Personalized Review, My Drama Journey, local progress, auth UI, Supabase sync/hydration, and offline fallback

## Supabase live state
Dedicated project exists:
- name: `Haneul`
- ref: `uyltjaftajwkujjhuric`
- region: `ap-south-1`
- observed status: `ACTIVE_HEALTHY`
- database engine observed: PostgreSQL 17

Learner tables:
- `profiles`
- `episode_progress`
- `saved_lines`
- `vocabulary_state`
- `line_state`
- `review_history`
- `activity_days`

All seven learner tables were re-verified with RLS enabled on 2026-09-11.

Observed ownership policy pattern on every learner table:
- role: `authenticated`
- operation: `ALL`
- `USING ((select auth.uid()) = user_id)`
- `WITH CHECK ((select auth.uid()) = user_id)`

This confirms row ownership is enforced in the database for existing and inserted/updated rows.

The same Supabase project also contains `content_candidates` and `content_pipeline_runs`. Those are not K-Drama learner-persistence tables and K-Drama code must not start depending on them.

## Deployment blocker resolved

The previous connector payload blocker is resolved. On 2026-09-11 the canonical ~2.4 MB backend-enabled build was streamed directly into the existing Vercel project and verified on the stable production domain with HTTP 200. The stable URL serves the auth/Supabase-enabled build.

## Implemented vs verified

Implemented in source:
- email/password sign in, sign up, sign out
- session-aware Supabase client
- local-first progress
- cloud sync
- cloud hydration
- saved-line sync
- vocabulary/line-state sync
- activity-day sync
- review-history loading
- offline/error local fallback
- per-user local snapshots for account switching

Verified in live infrastructure:
- official Vercel project is healthy and latest production deployment is READY
- stable production domain responds successfully
- dedicated Supabase project is ACTIVE_HEALTHY
- all seven learner tables have RLS enabled
- all seven learner-table policies enforce `auth.uid() = user_id` for both access and write ownership

Still requires interactive end-to-end production verification:
- brand-new signup
- returning login
- logout/login
- refresh/session restoration
- first local-to-cloud backup
- clean-browser/device cloud hydration
- two-account switching
- strict no-cross-user-data behavior from the browser client
- review-history write/read behavior

## Current continuation priorities

Backend/account thread:
1. run the interactive auth matrix against production
2. verify persistence/cross-device sync
3. verify two-user isolation from the browser client
4. fix any auth/sync defects found
5. keep backend state reproducible in GitHub
6. Account/Sync polish
7. My Drama Journey
8. Personalized Review

Visual/story thread when requested:
1. Level 2 visual continuity
2. 2–3 visual states per episode
3. scene image switching
4. controlled short branching
5. review/journey polish
6. Level 3 later
