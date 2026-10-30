# PORT-PLAN.md — Mistral Port: Work Plan & Task List

The executable version of the plan. Work top to bottom, one task at a time. When a task is done, check it off in this file and commit to `mistral-port`. Tasks marked **[ASK]** require input from the user before proceeding.

## Working rules for the agent

1. Work only on the `mistral-port` branch of this fork; keep local config out of git (upstream's `.json.example` pattern).
2. Never invent hardware paths, ports, or device names — detect or ask.
3. After each phase, run its acceptance test before moving on.
4. Keep upstream files intact where possible; put Mistral-specific additions in separate files so `git pull` stays clean.

---

## Phase 0 — Fork & clone

- [x] T0.1 Fork `jaredrhod/fullstack-agent` (done: this repo)
- [x] T0.2 Create branch `mistral-port` (done)
- [x] T0.3 Add `MISTRAL-PORT.md` adaptation notes (done)
- [ ] T0.4 Fork `jaredrhod/backtalk` (the only repo we modify)
- [ ] T0.5 On the agentbox, clone into `~/my-agent/`: this fork (branch `mistral-port`), the backtalk fork, `jaredrhod/ai-visualizer`, `jaredrhod/barehands`
- [ ] T0.6 Add upstream remotes (`git remote add upstream https://github.com/jaredrhod/<repo>.git`) so updates stay pullable

## Phase 1 — Brain (agentbox)

- [ ] T1.1 Create the agent folder (suggested `~/my-agent`) as the working directory for all sessions
- [ ] T1.2 Write `AGENTS.md` with the agent's identity and rules — adapt the boot-config structure from `ai-memory-vault/templates/CLAUDE.md` as a reference, renamed to Vibe's convention
- [ ] T1.3 Memory decision: do NOT install ai-memory-vault. Document in `AGENTS.md` that the Vibe Personal Knowledge base is the memory layer; optionally port the "Jobs"/priming concept as Vibe skills
- [ ] T1.4 Verify a Vibe CLI session starts in the folder, loads `AGENTS.md`, and answers in the agent's identity

**Acceptance:** interactive session in the agent folder behaves as the defined persona with memory access.

## Phase 2 — Servers (agentbox)

- [ ] T2.1 Run the face: `ai-visualizer/run.sh` — verify the face gallery opens on localhost
- [ ] T2.2 Run the hands: `barehands/server.py` — verify `stage.html` loads (Chrome on the notebook later)
- [ ] T2.3 Set up SSHFS mounts from the notebook: agent folder + visualizer folder (backtalk needs the agent definition and must write the face's signal files) — **[ASK]** notebook user, mount paths
- [ ] T2.4 From the notebook, open face and hands pages via SSH tunnel (e.g. `ssh -L 8794:localhost:8794 <agentbox>`); confirm both render
- [ ] T2.5 Write Vibe hooks (`./.vibe/hooks.toml`, pre/post types) that mirror agent activity to `barehands/state/state` (thinking/idle)
- [ ] T2.6 Teach the agent the `bin/board.sh` show-me verb (as a skill or in `AGENTS.md`)

**Acceptance:** with a Vibe session running, the ring on the notebook's browser reacts (idle ↔ thinking), and the face page loads in demo mode.

## Phase 3 — Voice port (notebook) — the real coding work

- [ ] T3.1 Read the backtalk source and isolate the Claude Agent SDK session layer behind a thin adapter interface (input → agent turn → output stream)
- [ ] T3.2 Implement the Mistral session adapter: Devstral (or Agents API) via api.mistral.ai, API-key auth; port the spoken permission/approval flow
- [ ] T3.3 Keep and verify local hearing + voice: STT, webrtcvad, Kokoro TTS, push-to-talk key (~1 GB models)
- [ ] T3.4 Point backtalk's `signals_dir` at the SSHFS-mounted visualizer folder so the face reacts
- [ ] T3.5 End-to-end test: hold key → speak → transcribed → Mistral answers → spoken reply → face syncs
- [ ] T3.6 German voice quality check: Kokoro German voices vs. ElevenLabs — **[ASK]** user preference, then set default

**Acceptance:** spoken question answered aloud on the notebook, face on the agentbox performs in sync, warm latency under ~3 s.

## Phase 4 — Hardening & iteration

- [ ] T4.1 Keep both servers bound to 127.0.0.1; document the tunnel commands in a README for the notebook
- [ ] T4.2 Verify no real config/secrets are tracked in git (only `.example` files)
- [ ] T4.3 Test upstream sync: `git pull upstream main` on a scratch copy; resolve conflicts without losing Mistral changes
- [ ] T4.4 Add a "Mistral variant" section to TROUBLESHOOTING notes (API errors, quota, voice quirks)

---

## Status log

| Date | Entry |
|---|---|
| 2026-10-29 | Plan pushed; T0.1–T0.3 done |
