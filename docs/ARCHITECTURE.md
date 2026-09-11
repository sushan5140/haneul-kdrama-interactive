# Architecture

## Frontend
Canonical implementation is a self-contained `index.html`.

Major responsibilities:
- render levels, episodes, scenes, and reply choices
- maintain learning state and scene progression
- Saved Dialogue
- Personalized Review
- My Drama Journey
- audio/browser speech
- account UI
- Supabase auth/sync

## Local persistence
Browser state is the resilience layer.

Known local keys include:
- `haneul-kdrama-memory-v1`
- `haneul-kdrama-session-days`
- `haneul-kdrama-last-cloud-sync`
- per-episode completion / last-scene keys
- subtitle-mode state

Local learner memory covers seen words/lines, mistakes, mastery, last-seen/review state, saved dialogue, episode completion, current scene, and review activity.

## Supabase client
The frontend uses `@supabase/supabase-js@2`.

Project:
- `Haneul`
- ref/host: `uyltjaftajwkujjhuric`

A publishable browser key is expected to be public. A service-role/private key must never appear in client source.

## Cloud model
Observed behavior:
1. app works locally without login
2. sign-in creates a cloud user context
3. cloud state is loaded/hydrated
4. if cloud has no learning data, existing local progress is backed up
5. learner-owned state syncs to Supabase
6. offline/backend errors leave local progress usable

K-Drama learner tables:
- `profiles`
- `episode_progress`
- `saved_lines`
- `vocabulary_state`
- `line_state`
- `review_history`
- `activity_days`

Saved lines are currently rebuilt during sync by deleting that user's saved-line rows and inserting the current local set. Be careful with partial failures/races if refactoring this.

## Database boundary
The same Supabase project currently has unrelated `content_candidates` and `content_pipeline_runs` tables. They are outside K-Drama learner persistence.

## Deployment
Static/single-file app hosted on the existing Vercel project:
- `haneul-kdrama-interactive`
- https://haneul-kdrama-interactive.vercel.app
