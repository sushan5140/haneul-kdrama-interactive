# START_HERE — Fresh Chat Bootstrap

This file exists to make **Haneul K-Drama Interactive** continue reliably in a brand-new ChatGPT/agent conversation without depending on old chat history or temporary sandbox files.

## Canonical project

- Repository: `sushan5140/haneul-kdrama-interactive`
- Production: https://haneul-kdrama-interactive.vercel.app
- Vercel project: `haneul-kdrama-interactive`
- Supabase project: `Haneul`
- Supabase ref: `uyltjaftajwkujjhuric`

This project is independent from Haneul Video Lab, KMate, CTET Companion, and all unrelated projects.

## Mandatory fresh-chat protocol

When a user says **continue**, **resume**, **take over**, or asks for work on Haneul K-Drama Interactive:

1. Open this repository.
2. Read, in order:
   - `AGENTS.md`
   - `docs/CURRENT_STATE.md`
   - `docs/PROJECT_RULES.md`
   - `docs/CONTINUATION.md`
3. Read `docs/ARCHITECTURE.md` and `docs/BACKEND_STATE.md` when the task touches implementation, auth, sync, persistence, deployment, or data.
4. Inspect only the relevant parts of `index.html`.
5. Inspect the live deployment/infrastructure when the task requires claims about production behavior.
6. Continue the user's requested work immediately.

## Do not stall the user

A fresh chat must **not**:
- ask the user to paste the old conversation
- ask "where were we?"
- ask for the project files again when GitHub is accessible
- depend on an old sandbox path
- reconstruct the project from memory when the repository can be read
- mix this project with Haneul Video Lab or another Haneul project
- claim deployment, auth, sync, isolation, or a fix works without verification

If GitHub connector access is unavailable, the repository is public, so use normal web access to read it when possible. Only report a blocker when the repository cannot actually be accessed by the available tools.

## Source-of-truth priority

1. live infrastructure for live-state claims
2. current GitHub source
3. `docs/CURRENT_STATE.md`
4. project-memory/history documents
5. old chat memory

If chat memory conflicts with GitHub, GitHub wins unless the user has just issued a newer instruction.

## Default continuation behavior

If the user provides no new priority, continue the first unfinished item in `docs/CURRENT_STATE.md`.

Current default backend continuation order is documented there and must not be guessed from old conversations.

## After substantial work

Before ending a substantial implementation session:

1. update `docs/CURRENT_STATE.md`
2. update any affected architecture/backend/rules docs
3. update `docs/CONTINUATION.md` when the next action changes materially
4. commit the durable state to GitHub

The goal is that the next chat can recover the project from this repository alone.

## Minimal prompt for a new chat

`Continue Haneul K-Drama Interactive from sushan5140/haneul-kdrama-interactive. Read START_HERE.md first and follow its fresh-chat bootstrap protocol, then continue my requested task.`
