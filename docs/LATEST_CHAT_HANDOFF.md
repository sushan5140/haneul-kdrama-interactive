# Latest Chat Handoff

Updated: **2026-09-11**

## Active instruction

The user wants ChatGPT to keep managing **Haneul K-Drama Interactive** and to keep durable project state updated in GitHub during meaningful checkpoints.

## Latest completed checkpoint

Source:
- repo: `sushan5140/haneul-kdrama-interactive`
- source commit: `98fe156d8892918349ad6a015595b990708b501d`
- `index.html` blob: `164188fe7d150c4470b9067e0db7634d7c757717`

Implemented:
- serialize auth-state handling
- wait for in-flight syncs during account transitions
- preserve/snapshot the current learner before sign-in/sign-up switches identity
- queue sync requests that occur while cloud work is busy
- replace fire-and-forget review writes with a local pending-review queue
- use stable review event IDs and idempotent cloud upsert
- seed first cloud backup from existing local review aggregates when needed

Database verification:
- all seven learner tables have RLS enabled
- ownership policies enforce `auth.uid() = user_id`
- required unique conflict keys exist for episode, vocabulary, line-state, and activity upserts
- Supabase security advisor returned no security findings

Production:
- stable URL: https://haneul-kdrama-interactive.vercel.app
- production deployment: `dpl_CAP73AHhoVE1UrGP5meCXJAt5mT2`
- state: READY
- HTTP: 200
- served length: 2,452,229 bytes
- served source matches the new hardening markers from GitHub
- grouped Vercel runtime-error scan found no runtime errors

## Important deployment finding

Do not equate Vercel `READY` with the correct source being live.

A prior production deployment (`dpl_4FWNuovrq4SVgLKycoR95hX3gQzk`) was READY while the stable URL still served the older 2,448,889-byte build.

For future backend iterations:
1. update GitHub
2. deploy the exact current GitHub blob to the existing Vercel project
3. verify the stable URL content/markers
4. only then mark production current

## Still pending

Interactive end-to-end production verification still needs:
1. fresh signup
2. returning login
3. logout/login
4. refresh/session restoration
5. local progress -> first cloud backup
6. clean-browser/device hydration
7. two-account switching
8. strict no-cross-user-data behavior
9. Saved Dialogue / review / progress account specificity

Do not claim those browser behaviors are verified until they are actually exercised.

## New-chat bootstrap

Read:
1. `START_HERE.md`
2. `AGENTS.md`
3. `docs/CURRENT_STATE.md`
4. `docs/PROJECT_RULES.md`
5. `docs/CONTINUATION.md`
6. this file

Then continue the first unfinished priority unless the user changes direction.
