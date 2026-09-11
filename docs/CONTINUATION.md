# Continuation Guide

Use this file when a new chat takes over the project.

## First steps
1. Read `START_HERE.md`.
2. Read `AGENTS.md`.
3. Read `docs/CURRENT_STATE.md`.
4. Read `docs/PROJECT_RULES.md`.
5. Read this file.
6. Inspect only the relevant areas of `index.html`.
7. Inspect current production before asserting live behavior.
8. Continue the user's requested work without asking them to reconstruct previous chats.

## Canonical identities

GitHub:
- `sushan5140/haneul-kdrama-interactive`

Vercel:
- project: `haneul-kdrama-interactive`
- project ID: `prj_oungX4pizKLxeyQzmnu0NdCTmmRr`
- stable URL: https://haneul-kdrama-interactive.vercel.app

Supabase:
- project: `Haneul`
- ref: `uyltjaftajwkujjhuric`

## Fresh-chat fallback

Do not make continuation depend on an old sandbox, old chat transcript, or remembered hidden context.

If the GitHub connector is unavailable:
1. use normal web access to read the public repository if available
2. use the live deployment for live-state inspection
3. report an actual blocker only if the required source/infrastructure truly cannot be accessed

Do not ask the user to paste the entire old conversation when the repository is available.

## Backend continuation
Auth/sync code exists and learner tables/RLS exist.

Verification sequence:
1. ensure production corresponds to the intended GitHub source
2. fresh signup
3. returning login
4. logout/login
5. refresh/session restore
6. local progress before sign-in -> first cloud backup
7. same account in clean browser/device -> hydration
8. two accounts -> strict isolation
9. verify Saved Dialogue / review / progress stay account-specific
10. record any durable changes back into these docs

## Visual/story continuation
When the user returns to story/visual work:
- preserve the light UI
- improve Level 2 visual continuity
- use 2–3 meaningful visuals per episode
- switch visuals at story beats
- use controlled short branching
- polish review/journey
- Level 3 later

## Legacy handoff
Older historical decisions are preserved in `docs/LEGACY_HANDOFF.md` once added. Infrastructure statements in old handoffs can be stale.

Priority of truth:
1. current live infrastructure
2. current GitHub source
3. `docs/CURRENT_STATE.md`
4. older handoff/history

## After substantial work
Update the repo memory so a future chat can continue from GitHub without reconstructing conversation history.
