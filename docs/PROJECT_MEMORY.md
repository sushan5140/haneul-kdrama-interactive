# Haneul K-Drama Interactive — Durable Project Memory

Last refreshed: 2026-09-11

## Identity

- Repository: `sushan5140/haneul-kdrama-interactive`
- Live Vercel project: `haneul-kdrama-interactive`
- Stable live URL: https://haneul-kdrama-interactive.vercel.app
- Vercel project ID: `prj_oungX4pizKLxeyQzmnu0NdCTmmRr`
- Vercel team ID: `team_2qP7AnUVZ2NnshuJiNVh464v`
- Vercel team slug: `sushan5140s-8170s-projects`
- This project is independent from Haneul Video Lab. Do not merge their learner state, databases, or implementation context unless explicitly requested.

## Source of truth

The repository root `index.html` is the imported current source from Library file:
`/Haneul KDrama Build/index-updated.html`, Library version 23, imported on 2026-09-11.

Previous Library-only source path remains useful for historical recovery, but future implementation should prefer this GitHub repository as the durable code/context source.

## UI / product constraints

- Current light Haneul UI is approved and locked as the baseline.
- Preserve near-white / pastel lavender / pastel blue surfaces, dark navy sidebar, rounded white cards, light borders, soft shadows, purple/indigo/blue accents, and the current player/story structure.
- Do not redesign into a dark Netflix-like interface.
- Do not generate a replacement UI mockup when asked to change the real app.
- Preserve current working learning interactions unless a requested functional change requires modification.
- Main recurring characters: Seoyeon (서연) and Minjun (민준). Keep character continuity.

## Current content state

- Level 1: First Encounters — 6 episodes.
- Level 2: Everyday Connections — 6 episodes.
- Existing interactions include story scenes, Korean line + natural English meaning, reply choices, correct/incorrect state, scene context, vocabulary, grammar, use cases, practice, audio, previous/next navigation, saved lines, progress/review interactions, and My Drama Journey / Personalized Review concepts.

## Backend architecture now present in the current source

The current `index.html` contains direct Supabase client integration using `@supabase/supabase-js@2`.

Observed backend behaviors:
- email/password account UI
- sign in / sign up / sign out
- session-aware cloud user state
- local-first fallback using browser localStorage
- scheduled cloud sync
- offline/online handling
- cloud hydration after authentication
- first-cloud-use behavior that backs up existing local progress when cloud has no learning data
- progress state merging/hydration rather than deleting local state blindly
- cross-device intent: progress, saved lines and review state are synced for authenticated users

Public client configuration currently embedded in source:
- Supabase project URL: `https://uyltjaftajwkujjhuric.supabase.co`
- publishable key is embedded in the client source, as expected for a Supabase publishable/anon client key

Never commit a Supabase service-role key or other server secret into this repository.

## Tables referenced by current production source

The current source reads/writes these tables:
- `profiles`
- `episode_progress`
- `saved_lines`
- `vocabulary_state`
- `line_state`
- `review_history`
- `activity_days`

Important conflict keys visible in the source:
- `profiles`: `user_id`
- `episode_progress`: `user_id,level,episode`
- `vocabulary_state`: `user_id,token`
- `line_state`: `user_id,line_key`
- `activity_days`: `user_id,activity_date`

Saved lines are rebuilt per user during sync: current rows for that `user_id` are deleted and the current local saved set is inserted.

## Persistence implementation

Local browser state remains the safety fallback.

Important local keys include:
- `haneul-kdrama-memory-v1`
- `haneul-kdrama-session-days`
- `haneul-kdrama-last-cloud-sync`
- episode completion / last-scene localStorage entries
- subtitle-mode localStorage state

The sync layer should preserve the rule:
**local progress must remain safe even when cloud sync is offline or errors.**

## Authentication state to verify in production

Authentication code exists in the current source, but future work must keep verifying production behavior rather than assuming success.

Required checks:
1. brand-new account signup
2. returning-user login
3. logout/login
4. session restoration after refresh
5. signed-in cloud hydration
6. local-first fallback while offline
7. correct user-specific state after switching accounts

## RLS / data-isolation rule

The frontend consistently scopes user data by `cloudUser.id` / `user_id`.

However, the exact SQL schema and deployed RLS policies are not stored in this repository yet, so do **not** claim the current RLS policy definitions are verified from GitHub.

Required backend invariant:
- authenticated users may only read/write rows whose `user_id = auth.uid()`
- unauthenticated users must not read or mutate private learner rows
- every learner-owned table must enforce this at the database layer, not only in frontend queries

Before changing schema or declaring isolation complete, inspect the connected Supabase project and record the actual policies/migrations in this repo.

## Current production state

- Stable production URL must remain: https://haneul-kdrama-interactive.vercel.app
- Continue using the existing Vercel project.
- Do not create replacement Vercel projects for normal iterations.
- The current GitHub import represents the latest Library source available at the time this repo was created.
- Source contains account/sync implementation, but production auth, cross-device persistence, and isolation must be re-verified after deployment changes.

## Completed durable work

- light Haneul visual baseline established
- two levels / twelve episodes implemented
- Level 1 visual mapping established
- Level 2 learning arc implemented
- scene/reply learning interactions implemented
- saved lines and review/progress concepts implemented
- localStorage persistence implemented
- Supabase client integration added
- account UI added
- cloud sync + hydration logic added
- offline local fallback retained
- project moved from chat/Library-only source into a dedicated GitHub repository

## Unresolved / must-verify items

- Confirm the current GitHub version is the exact version deployed to production.
- Verify fresh signup and returning login on production.
- Verify refresh/session restoration.
- Verify local-to-cloud first backup.
- Verify cloud-to-new-device hydration.
- Verify two different users never see each other's data.
- Inspect and record actual Supabase schema and RLS policies.
- Add migrations/schema files to GitHub so backend state is reproducible.
- Confirm `review_history` write/read behavior end to end.
- Re-run persistence tests after any sync-merge changes.

## Approved next phases

For the backend/account-sync continuation thread, priority is:
1. deploy/sync this GitHub-backed source to the existing Vercel project
2. verify production authentication
3. verify persistence and cross-device sync
4. verify user-data isolation and RLS
5. make backend schema reproducible in the repo
6. continue Account / Sync polish
7. continue My Drama Journey
8. continue Personalized Review

Do not mix this backend continuation with the separate Haneul Video Lab project.

## Visual/content continuation notes

Earlier product roadmap also includes stronger Level 2 scene visual continuity and controlled branching. If backend work is the active task, do not derail into redesign work unless requested.

## Working rules

- Use this repository as the first context source in future chats.
- Read `docs/PROJECT_MEMORY.md` before major implementation.
- Inspect current code before changing behavior.
- Keep the stable production URL.
- Verify before saying something is deployed or working.
- Never expose service-role keys or secrets.
- Prefer compact durable documentation over copying chat history.
- Update this file whenever architecture, schema, deployment identity, blockers, or approved next phases materially change.
