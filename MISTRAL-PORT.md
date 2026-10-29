# MISTRAL-PORT.md — Running this stack on Mistral

This fork adapts the jaredrhod fullstack-agent stack to run with Mistral as the brain instead of Claude Code. The upstream pieces are deliberately agent-agnostic (file + shell contracts); only the installer and backtalk assume Claude.

## Target architecture

- **Agentbox (Linux, headless):** Vibe CLI (browser auth) as the brain + face server (ai-visualizer) + hands server (barehands).
- **Notebook (mic + webcam):** browser tabs for face and hands (MediaPipe hand tracking runs client-side, so the notebook's camera is used) + the ported voice loop.

Notebook reaches the agentbox servers via SSH tunnel (servers bind 127.0.0.1). An SSHFS mount of the agent folder + visualizer folder onto the notebook carries the signal files (.voice_state etc.) between voice and face.

## Adaptation decisions

| Upstream assumption | This fork |
|---|---|
| Installer is a Claude Code conversation | Point your agent at fullstack-agent.md and run the same steps; adapt file names on the fly |
| Boot config is CLAUDE.md | Use Vibe's AGENTS.md convention for agent identity/rules |
| Claude Code native memory (MEMORY.md redirect) | Skipped — existing Vibe Personal Knowledge is the memory layer; ai-memory-vault is NOT installed |
| ai-memory-vault | Skipped (see above); borrow the "Jobs"/priming concept as Vibe skills if useful |
| backtalk built on Claude Agent SDK | Port: keep local STT, webrtcvad, Kokoro TTS, push-to-talk key; replace the SDK session with a Mistral API loop (Devstral / Agents API; Voxtral optional for transcription) |
| barehands ring mirrored via Claude Code hooks in settings.json | Vibe hooks in .vibe/hooks.toml (pre_tool etc.) write state/state and call bin/board.sh |
| Claude usage shown on faces | Not available; ignore that HUD element |

## Porting status

- [x] Phase 0: forked, branch mistral-port
- [ ] Phase 1: brain on agentbox (AGENTS.md, Vibe CLI)
- [ ] Phase 2: face + hands servers on agentbox, hooks wired, SSH tunnels
- [ ] Phase 3: backtalk port on notebook (the real coding work)
- [ ] Phase 4: German voice quality, permission flow, hardening

## Conventions for this fork

- Keep upstream's .json.example pattern: local config never tracked in git.
- Pull upstream with git pull from jaredrhod/main; keep Mistral changes on mistral-port.
- The face (ai-visualizer) and hands (barehands) repos stay unmodified — they work with any AI. Only backtalk needs its own fork with the Mistral session layer.
