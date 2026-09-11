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

### Product/story expansion — now the main track
1. **Complete Level 1 fully before active Level 2 development**
2. existing Episodes 1–6 become Arc 1 of the Level 1 beginner season
3. expand Level 1 toward the approved 24-episode / 4-arc structure in `docs/LEVEL_1_PLAN.md`
4. add arc checkpoints, cross-episode recycling, story-memory callbacks, and progressive reduction of English support
5. finish Level 1 visual continuity and scene-image switching across the expanded season
6. verify Personalized Review and My Drama Journey against the expanded Level 1 content
7. only after Level 1 meets its completion criteria, resume Level 2 and later Levels 3–5

### Backend/account track — secondary until product expansion is stronger
1. preserve the existing email/password auth and Supabase sync implementation
2. fix any critical persistence/auth defects found during normal use
3. complete the full interactive auth/session/sync verification matrix before final release
4. **Google OAuth/login is intentionally deferred to the very end of the project**
5. do not prioritize OAuth setup ahead of story/level expansion unless the user changes direction

### Story direction
The user explicitly changed the sequencing on 2026-09-12: **finish Level 1 completely first, then move to Level 2**. One short story is not enough for Level 1; it should cover the important beginner foundations through multiple connected story arcs.

Canonical Level 1 expansion plan: `docs/LEVEL_1_PLAN.md`.


## Level 1 expansion decision

Level 1 is now the primary product focus before Level 2 expansion.

The user explicitly removed any artificial content cap: Level 1 may become very large if each episode adds genuine learning, story, repetition, mastery, or review value.

Canonical design: `docs/LEVEL_1_MASTERPLAN.md`

Level 2 should not be used to hold beginner material that belongs in Level 1.


## Canonical Level 1 episode map

The fixed 80-episode Level 1 production map is now defined at:

- `docs/LEVEL_1_80_EPISODE_MAP.md`

Structure:
- 20 current Online KSI Beginner 1 curriculum units
- 4 Haneul episodes per unit
- 10 connected arcs
- 8 episodes per arc

Implementation now proceeds from the architecture/migration step, starting with Arc 1 and reusing the existing six Level 1 episodes where they fit.


## Level 1 source migration checkpoint

Updated: **2026-09-12**

Implementation commit:
- `56bae0174b5938e1a27532edfc4e1f5a887ee693`

What changed in the real app source:
- Level 1 now has a fixed UI/progress target of **80 episodes**.
- the Levels overview is Level 1-first and labels the current content as KSI Beginner 1 / Arc 1.
- Level 2 is no longer displayed in the Levels overview during this phase.
- existing Level 2 source content is preserved rather than deleted.
- Level 2 progression is blocked; completing current Episode 6 no longer unlocks Level 2.
- completing Episode 6 now explains that Episode 7 is the next Level 1 production chapter.
- My Drama Journey now counts Level 1 progress against 80 episodes.

Important remaining migration work:
- current Episodes 1–6 were written before the fixed KSI 80-episode map and do not yet fully match the new Arc 1 curriculum sequence.
- migrate/rewrite those six scripts while preserving useful dialogue, visuals, state logic, review behavior, and learner data.
- then create Episodes 7–8 to finish Arc 1.
- after Arc 1 content is aligned and verified, replace the temporary six-episode indexing assumptions with durable episode metadata before scaling through Episodes 9–80.

Canonical map:
- `docs/LEVEL_1_80_EPISODE_MAP.md`


## Level 1 Arc 1 implementation checkpoint — 2026-09-12

Implemented in GitHub source:
- added the 10-arc / 80-episode Level 1 navigation model
- rebuilt Level 1 Episodes 1–6 around KSI Beginner 1 Lessons 1–2
- added Episodes 7–8 to complete Arc 1
- preserved existing Level 1 artwork and working scene/review/progress mechanics
- moved the six previous Level 2 episode objects into dormant `preservedLevel2Episodes` data so Level 1 can grow to 80 without repeatedly renumbering Level 2
- retargeted Level 2 artwork/scene-image assignments to the preserved data
- set the active Level 1 numbering boundary to 80
- protected Arc 1 from accidentally using the old Level 2 index-based branch reactions

Active Arc 1 episodes:
1. Arrival at Haneul
2. The Name Tag Mix-Up
3. Who’s New Here?
4. First Introductions
5. The Wrong Guess
6. Student, Not Staff
7. Guess Who?
8. The Welcome Board

Arc 1 now covers KSI Beginner 1 Lessons 1–2 and is structurally complete.

Preserved Level 2 data:
- Text Me When You Arrive
- Lunch Rush
- Lost in Hongdae
- Study Group
- Festival Night
- A Small Promise

Level 2 is intentionally dormant during the Level 1 rebuild. It has not been deleted.

Verification:
- the current inline JavaScript compiles successfully after the migration
- production deployment has not yet been claimed as updated or visually verified for this checkpoint

Source commits:
- ten-arc navigation model: `576dd65e2d6cb64687aba0b490e422d67c4df82d`
- rebuilt Episodes 1–6: `4243454fdf7d9a648b441066a86052148269d9c6`
- completed Arc 1 + dormant Level 2 migration: `5e00fd8cc112d1d1cb6e3c984c7f0a5b44d9a1c3`



## Production checkpoint — Level 1 Arc 1 live

Verified production deployment:
- deployment ID: `dpl_9DxrXWQBd4KzZzJmrcrbtWEzWVXw`
- deployment URL: `haneul-kdrama-interactive-8wljrltw4-sushan5140s-8170s-projects.vercel.app`
- target: `production`
- state: `READY`
- stable URL: https://haneul-kdrama-interactive.vercel.app

Verified on both the deployment-specific URL and the stable URL:
- Episode 1 marker: `Arrival at Haneul`
- Episode 8 marker: `The Welcome Board`
- 10-arc Level 1 navigation data is present
- active Level 1 numbering boundary is 80

The inline JavaScript also passed a compile-only syntax check before deployment.

This verifies the intended source reached production. Full interactive browser testing of every scene/choice is still a separate verification step.

## Next implementation target

Proceed with **Arc 2 — Things We Like, Places We Know**:
- Episodes 9–16
- KSI Beginner 1 Lessons 3–4
- preserve Arc 1 progress/review compatibility
- keep Level 2 dormant until the Level 1 rebuild is complete
- Google OAuth remains final-stage work


## Review-unlocked production mode

Updated 2026-09-12.

For active development/review, all currently available episodes are directly open. Learners/reviewers do not need to complete earlier episodes first.

Implementation:
- `REVIEW_ALL_EPISODES = true`
- normal completion/progression state is still preserved underneath
- `isLocked()` now resolves from the same review-aware unlock function
- startup/session restoration no longer rejects a later episode merely because prior episodes are incomplete

GitHub commit:
- `826d5610d9fe72111291c151d298e54399d6d8db`

Production deployment:
- `dpl_AdfqheRAGCdnDtr7fAh9dye1uokC`
- state: READY
- stable URL: https://haneul-kdrama-interactive.vercel.app

Live HTML verification confirmed the review flag, unlock path, and Level 1 arc architecture are present on the stable production URL.

This review override is temporary product-development behavior. It can be switched off later when normal learner progression should be enforced again.
