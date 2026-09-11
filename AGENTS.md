# AGENTS.md — Haneul K-Drama Interactive

This is the operating entry point for every future ChatGPT/Codex/agent session.

## Fresh chats
If this is a new conversation, read `START_HERE.md` first. It contains the mandatory takeover protocol and anti-stall rules.

## Read first, in order
1. `START_HERE.md`
2. `docs/CURRENT_STATE.md`
3. `docs/PROJECT_RULES.md`
4. `docs/CONTINUATION.md`
5. `docs/ARCHITECTURE.md`
6. `docs/BACKEND_STATE.md`
7. Inspect `index.html` only for the requested implementation area.

Repository state overrides stale chat memory.

## Canonical identity
- Repo: `sushan5140/haneul-kdrama-interactive`
- Production: https://haneul-kdrama-interactive.vercel.app
- Vercel project: `haneul-kdrama-interactive`
- Supabase project: `Haneul` / `uyltjaftajwkujjhuric`

## Non-negotiable operating rules
- Preserve the approved light Haneul UI unless the user explicitly requests redesign.
- Continue on the existing Vercel project and stable domain.
- Never claim deployment/auth/sync works without verification.
- Keep local learner state safe if cloud sync fails.
- Enforce user isolation through Supabase RLS, not frontend filtering alone.
- Never commit service-role keys, passwords, private API keys, or tokens.
- Keep this project independent from Haneul Video Lab, KMate, CTET, and unrelated apps.
- Preserve Seoyeon (서연) and Minjun (민준) continuity.
- Do not jump to Level 3 while current Level 2/backend priorities are unfinished unless the user changes priority.
- Do not ask a fresh-chat user to restate old project context that exists in this repository.
- Do not depend on temporary sandbox state for durable continuation.

## Documentation duty
Update the docs whenever deployment identity, backend schema/RLS, auth/sync architecture, blockers, priorities, or project rules materially change.

The repository must remain sufficient to continue the project without reconstructing an old conversation.
