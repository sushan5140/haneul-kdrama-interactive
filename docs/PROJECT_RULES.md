# Project Rules

These constraints remain active unless the user explicitly changes them.

## Independence
K-Drama Interactive is independent from Haneul Video Lab, KMate, CTET Companion, and unrelated prototypes. Do not merge learner state, personalization logic, databases/context assumptions, or implementation decisions across those projects.

## Visual baseline
The approved **light Haneul UI** is locked.

Preserve:
- near-white / pastel lavender / pastel blue surfaces
- dark navy sidebar
- rounded white cards
- thin light borders and soft shadows
- purple / indigo / blue accents
- spacious beginner-friendly layout
- current story/player and learning-panel structure

Do not:
- convert it into a dark Netflix-style UI
- replace the whole interface because one component needs fixing
- generate a mockup instead of editing the real app
- drift into a generic Lovable-style dashboard

## Story identity
Recurring leads:
- Seoyeon — `서연`
- Minjun — `민준`

Character/story continuity matters more than producing lots of unrelated artwork.

## Deployment
- keep the existing Vercel project `haneul-kdrama-interactive`
- preserve https://haneul-kdrama-interactive.vercel.app
- no replacement project for normal iterations
- never say “deployed” unless deployment actually happened
- verify production when tooling permits

## Persistence/security
- local browser state remains the safety fallback
- cloud failure must not destroy valid local progress
- user isolation must be enforced by RLS
- never expose a service-role key in browser code
- never commit secrets
- K-Drama persistence must not depend on unrelated `content_*` tables

## Controlled branching
Do not create giant story trees.

Preferred pattern:
- learner chooses reply
- short different reaction/micro-scene
- branch rejoins the main story quickly

## Product philosophy
Prioritize:
- cinematic story immersion
- contextual Korean
- meaningful reply choices
- adaptive review from real mistakes
- gradual reduction of English support
- saved dialogue from scenes
- cross-episode recycling
- progression that feels like living a drama

Avoid turning it into a generic flashcard, grammar, AI tutor, or streaming app.

## Verification language
Never claim auth, sync, isolation, deployment, or a visual change works unless it was inspected/tested.
