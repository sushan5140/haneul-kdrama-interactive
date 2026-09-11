# Backend State Snapshot

Snapshot date: **2026-09-11**

Supabase:
- project: `Haneul`
- ref: `uyltjaftajwkujjhuric`
- region: `ap-south-1`
- observed status: `ACTIVE_HEALTHY`

## Learner tables

### profiles
Primary key: `user_id` -> `auth.users.id`

Includes current_level, current_episode, current_scene, subtitle_mode, preferred_voice, timestamps.

RLS: enabled.

### episode_progress
Primary key: `id`
Foreign key: `user_id` -> `auth.users.id`

Includes level, episode, scene, completed, completed_at, last_opened_at.

Client upsert conflict key: `user_id,level,episode`.

RLS: enabled.

### saved_lines
Primary key: `id`
Foreign key: `user_id` -> `auth.users.id`

Includes level, episode, scene, line_key, korean, meaning, context, title, saved_at.

RLS: enabled.

### vocabulary_state
Primary key: `id`
Foreign key: `user_id` -> `auth.users.id`

Includes token, seen_count, mistake_count, mastery_score, last_seen, next_review, updated_at.

Client upsert conflict key: `user_id,token`.

RLS: enabled.

### line_state
Primary key: `id`
Foreign key: `user_id` -> `auth.users.id`

Includes line_key, seen_count, correct_count, wrong_count, mastery_score, last_seen, next_review, updated_at.

Client upsert conflict key: `user_id,line_key`.

RLS: enabled.

### review_history
Primary key: `id`
Foreign key: `user_id` -> `auth.users.id`

Includes item_type, item_key, result, reviewed_at.

Allowed item_type values observed: `line`, `vocab`.
Allowed result values observed: `again`, `got_it`, `correct`, `wrong`.

RLS: enabled.

### activity_days
Composite primary key:
- `user_id`
- `activity_date`

Foreign key: `user_id` -> `auth.users.id`.

RLS: enabled.

## Live RLS pattern
Policies observed on learner-owned tables use authenticated ownership checks equivalent to:

`auth.uid() = user_id`

with both access and write ownership enforced.

Observed policy names:
- `profiles own row`
- `episode_progress own rows`
- `saved_lines own rows`
- `vocabulary_state own rows`
- `line_state own rows`
- `review_history own rows`
- `activity_days own rows`

## Other tables
Also present:
- `content_candidates`
- `content_pipeline_runs`

They use no-direct-access style RLS and are out of scope for K-Drama learner persistence.

## Auth client hardening — 2026-09-11

Production source commit: `090c9d14f240d10016ef2fc246e8f5594756a2d2`

Deployed fixes:
- auth state changes are handled outside the immediate `onAuthStateChange` callback to avoid the documented `supabase-js` async-callback deadlock class
- `SIGNED_OUT` transitions learner state to the isolated guest snapshot instead of leaving the previous signed-in learner state exposed as a guest
- manual browser sign-out uses Supabase local scope so it does not intentionally sign the learner out on every device

Verified after deployment:
- Vercel deployment `dpl_4FWNuovrq4SVgLKycoR95hX3gQzk` is READY and production
- official stable alias serves the patched source with HTTP 200
- no grouped Vercel runtime errors were observed in the post-deploy scan
- Supabase security advisor returned no security lints

## Still needs product-level verification
The schema and policies were inspected, but real-session behavior still needs testing for signup, login, refresh, sync, hydration, account switching, and strict isolation.
