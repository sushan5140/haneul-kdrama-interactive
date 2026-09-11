# Current State

Last refreshed: **2026-09-11**

## Production
- Stable URL: https://haneul-kdrama-interactive.vercel.app
- Vercel project: `haneul-kdrama-interactive`
- Project ID: `prj_oungX4pizKLxeyQzmnu0NdCTmmRr`
- Team ID: `team_2qP7AnUVZ2NnshuJiNVh464v`
- Latest observed production deployment: `dpl_CAP73AHhoVE1UrGP5meCXJAt5mT2`
- State: `READY`
- Target: `production`
- Stable production URL returned HTTP 200 during continuation verification on 2026-09-11 after the auth-state hardening deployment.

READY plus HTTP 200 proves Vercel served the deployment; it does not by itself prove all auth/sync behavior.

## Canonical source
`index.html` is the latest captured Library build from:
- `/Haneul KDrama Build/index-updated.html`
- Library version observed: `23`
- Captured 2026-09-11

The current GitHub app source is commit `98fe156d8892918349ad6a015595b990708b501d` with `index.html` blob `164188fe7d150c4470b9067e0db7634d7c757717`.

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

## Deployment/source correspondence

The previous connector payload blocker is resolved, but a second deployment lesson was discovered on 2026-09-11: a Vercel deployment can be `READY` while the stable URL is still serving an older uploaded source.

Observed stale case:
- deployment `dpl_4FWNuovrq4SVgLKycoR95hX3gQzk` was READY/production
- stable URL still served the older 2,448,889-byte build

Current verified deployment path:
- deploy the exact current GitHub `index.html` blob directly to the existing Vercel project
- verify the stable URL after deployment instead of trusting READY alone
- current production deployment: `dpl_CAP73AHhoVE1UrGP5meCXJAt5mT2`
- stable URL serves HTTP 200
- served length: 2,452,229 bytes, matching the current GitHub source
- deployed source contains the new pending-review, auth-queue, and pre-switch flush markers

Do not assume a GitHub push alone updates production until source correspondence is verified.

## Auth-state hardening deployed

On 2026-09-11 the production auth client was hardened after comparing the live code with current Supabase guidance:
- `onAuthStateChange` no longer performs async Supabase work directly inside the callback; the work is deferred until after the callback returns
- signed-out auth events now move the browser into the isolated guest learner state while preserving the signed-in user's local snapshot
- manual sign-out now uses `{ scope: 'local' }` so signing out this browser does not intentionally revoke sessions on the learner's other devices
- production deployment: `dpl_4FWNuovrq4SVgLKycoR95hX3gQzk`
- stable URL verified HTTP 200 and confirmed to serve the patched auth code
- Vercel runtime-error scan after deployment returned no grouped runtime errors

## Sync race and review durability hardening

On 2026-09-11 source commit `98fe156d8892918349ad6a015595b990708b501d` added a second account/sync hardening pass:
- in-flight cloud syncs are now awaited instead of being skipped during sign-out/account switching
- auth events are serialized through a queue
- different-user activation waits for prior activation/sync work before changing local learner ownership
- pending sync requests raised while the cloud layer is busy are queued for a follow-up sync
- sign-in/sign-up from an already signed-in browser flushes and snapshots the current learner first
- review outcomes are persisted to a local pending queue with stable client-generated IDs
- review-history uploads use idempotent upsert by `id`, so retrying a failed upload does not intentionally duplicate the same queued event
- first cloud backup can reconstruct pending review events from existing local review aggregates
- per-user local snapshots continue to isolate pending review data across account switches

Production verification:
- deployment: `dpl_CAP73AHhoVE1UrGP5meCXJAt5mT2`
- stable URL: HTTP 200
- current source length and hardening markers matched the GitHub blob
- Vercel grouped runtime-error scan: no runtime errors found
- Supabase security advisor: no security lints
- required unique upsert indexes were verified for `episode_progress(user_id,level,episode)`, `vocabulary_state(user_id,token)`, `line_state(user_id,line_key)`, plus the `activity_days` composite primary key

This is source/infrastructure verification, not a substitute for the remaining interactive multi-account browser matrix.

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
- client upsert conflict keys are backed by live unique indexes/primary keys

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


## Cross-chat continuity

Fresh-chat continuation was hardened on 2026-09-11.

- Root entry point: `START_HERE.md`
- New chats are instructed to load GitHub state instead of relying on old conversation history or sandbox paths.
- Fresh chats must not ask the user to reconstruct prior context when the repository is accessible.
- If the GitHub connector is unavailable, agents should use normal web access to read this public repository when possible.
- `AGENTS.md`, `README.md`, and `docs/CONTINUATION.md` all point to the same deterministic bootstrap flow.

This is now the canonical cross-chat recovery mechanism for Haneul K-Drama Interactive.

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
