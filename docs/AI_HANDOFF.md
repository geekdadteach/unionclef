# AI HANDOFF — `geekdadteach/unionclef` as Bob's body on NewEarth

**Snapshot:** 13 November 2026, about 11 PM America/New_York, written by **MineBobNE** (a Grok Bot agent) on Randy's order of 13 Nov 2026 that every repo carry docs letting any AI take over immediately "if grok did not exist".

**This is a PUBLIC repository.** No secrets, tokens, login sessions, Discord IDs, account UUIDs, kids' details or home public IPs go here. Host/ops detail beyond what is below lives in the **private** repo `geekdadteach/minebob` (`docs/AI_HANDOFF.md` — the master takeover document for Bob) and `geekdadteach/systems` (`bots/minebobne/HANDOFF.md`).

Status words: Proposed → Coded → Tested → Merged → Deployed → Live-verified. Items marked **UNVERIFIED** were not re-checked on a host when this was written.

## 1. What this fork is and why it exists

- `geekdadteach/unionclef` is Randy's public fork of **`3ndetz/unionclef`** — a Fabric client mod (altoclef + tungsten/baritone-derived navigation) with code-based survival and an **MCP tool server** (~60 tools).
- Since **1 Nov 2026** it is the "body" of **Bob** (Minecraft account **MineBobNE**), the autonomous AI player on **NewEarth**, Randy's private family-and-friends server (Paper Java 26.3) run as a homeschool project. Bob replaced his earlier Mindcraft/Mineflayer runtime (fork `geekdadteach/mindcraft-ce`, now history).
- The fork's `main` tracks upstream (last seen `6a457a8`, release 0.95.66 line, 27 Oct 2026). **No NewEarth-specific code changes are on `main`.** Bob-specific logic lives in a separate **driver loop** that is **not in this repo** (see §4).
- The rest of `AGENTS.md`, `CLAUDE.md`, `docs/CHECKLIST.md`, etc. are the **upstream author's** working rules ("operator" there means the upstream author). Follow them for code work in this tree. For the NewEarth deployment, the owner is Randy and the rules in §7 apply.

## 2. Current state (13 Nov 2026)

- **Bob is OFF** since 2 Nov 2026 (Randy: free resources for a dev project; keep off across reboots and a macOS upgrade). Startup jobs disabled, nothing deleted. **Stays off until Randy explicitly says to turn him on.**
- Ran on **Mac mini .20** (`192.168.1.20`, user `test`, under `~/BobRuntime/unionclef/`). Since 9 Nov Randy is **moving Bob to Leon** (`192.168.1.144`, user `dadmin`) with a local agent; the ViaProxy login was copied; the rest is **UNVERIFIED**. Leon needs `dadmin` logged in as the active console user for the Minecraft window (conflicts with that room's dark-screen rule) — Randy hasn't chosen yet.
- Which UnionClef build/jar is installed on .20 is **UNVERIFIED** (installed 1 Nov from this fork, Fabric **1.21.11**).
- Brain: driver default is `andy-4.2-air` via LM Studio on Leon (`192.168.1.144:1234`); Randy decided on 3 Nov to move to a **hosted API model** (key pending, model not chosen).

## 3. Runtime chain

```
NewEarth Paper 26.3 (192.168.1.63:25565)
   ▲
ViaProxy 127.0.0.1:25599 (version bridge 1.21.11 → 26.3; holds the account login — secret)
   ▲
UnionClef (this mod) in Minecraft 1.21.11 as MineBobNE — needs an active GUI session
   │ MCP: Streamable HTTP JSON-RPC, POST http://<host>:25350/mcp (docs/features/MCP_SERVER.md)
   ▼
driver.py (private, on the host) ──► OpenAI-compatible LLM API
```

- Settings used: `mcpEnabled` true, `mcpPort` 25350, binds **0.0.0.0** (LAN-reachable). Optional `mcpAuthToken` (Bearer) — whether it's set on the host is UNVERIFIED; if set it is a secret in the host's UnionClef settings. Open item: bind/firewall to localhost.
- **UnionClef does not auto-rejoin** after a server restart; an operator reconnects it (the driver never lets the model call `ConnectToServer`).

## 4. The driver (lives on the host, not here)

Path on .20: `~/BobRuntime/unionclef/driver/` (`driver.py`, `README.md`, `config.json`, `state/`). Full detail: minebob `docs/AI_HANDOFF.md` §6. Summary:

- Exposes **all 60 UnionClef MCP tools** (from live `tools/list`, falling back to a cached copy from the jar) plus the driver's own tools — 82 total, each in one group. Core group always shown (eat, stop, defend, wait, project/step/notes/memory tools, tool-group tools, getGameState, inventorySpace, getBlocksAround, canReach, runAwayPlayer, runAwayStop). Other groups (perception, movement, survival/combat/night/food, gathering, building, worldedit, protection, inventory/menus, commands, misc) are added deterministically by situation and task keywords; the model can request any group, and calling an unshown tool adds its group. **Nothing is out of reach** — Randy rejected an earlier trimmed 19-tool set on 3 Nov.
- Refused to the model: ConnectToServer, punk/punkAny, shootArrowAt, chat (unless enabled), slash commands, and @punk/@gamer/@test/@reload_settings/@set_gamma/@custom.
- Safety guards (all Randy-approved): eat-first (nothing blocks eating), hostile-within-8 stop guard, done-guard, stuck detector, respawn watchdog, home mode, bed rules, auto-sleep after 2 ignored prompts, stone→cobblestone, immediate re-prompt on failures, no-op collect/craft guards, honest-notes guard, verified chest food and toss_junk.
- Prompt ~2.6K–4.5K tokens typical, ~7K worst case; LLM context must be ≥12K.
- Tests: 82 offline tests passed on 3 Nov. Change pattern: backup to `~/BobRuntime/backups/unionclef-driver-<ts>` → test → restart only the driver. Latest backup: `unionclef-driver-20261103-074930`.
- **Gap:** the driver is not in any Git repo yet (Randy's rule: everything tracked in GitHub, no secrets). Planned home: private `minebob` (`ops/unionclef-driver/`) — needs the host owner (bot Randy MM 20) to export it.

## 5. Build, run, test, deploy, roll back (this mod)

- **Build/run from source:** see `docs/DEVELOP.md` (`gradlew compileJava`, `gradlew runClient`; everything builds from source). Release notes: `docs/releases/`. MCP doc and smoke test: `docs/features/MCP_SERVER.md`, `deploy/runner/mcp_test.py`.
- **Deploy to Bob's host:** install the built mod into the host's Fabric 1.21.11 instance under `~/BobRuntime/unionclef/` (exact layout UNVERIFIED — inspect the host first, read-only). Only with Randy's OK and coordinated with the host's owner bot. Back up the current jar/instance first.
- **Roll back:** restore the previous jar/instance from the backup and restart the client.
- **Upstream sync:** keep `main` as a clean mirror of upstream; never force-push; put any NewEarth-specific mod change on a branch with a PR.

## 6. Machines and owners

| Machine | Role | Owner bot |
|---|---|---|
| Mac mini .20 `192.168.1.20` (user `test`) | Bob's runtime until the move | Randy MM 20 |
| Leon `192.168.1.144` (user `dadmin`) | LM Studio; Bob's new runtime host (move in progress) | Leon Mac Mini 143 |
| linuxidk `192.168.1.63` | NewEarth server; all server-side changes | Minecraft Server 63 |
| LinuxGrok .60, olddell .109 | **Retired / gone** — never use | — |

Who to ask: Bob behaviour/driver → MineBobNE; .20 → Randy MM 20; Leon → Leon Mac Mini 143; server → Minecraft Server 63; GitHub → the GitHub bot; decisions → Randy.

## 7. Randy's rules for Bob (apply to any work touching this deployment)

- Tell Randy plainly and up front about any limit on Bob's abilities.
- No bot monitors Bob or relays status; the host posts an hourly report on its own.
- Bob stays off until Randy says so. Nothing blocks eating. Judge Bob on short-term survival. Don't micromanage.
- Everything running is recorded in GitHub, never secrets; reviewed PRs; test first; always a rollback; never merge/deploy/force-push without Randy's OK.
- Bob never gets op; server changes go through Minecraft Server 63.

## 8. Gotchas

- Needs an active GUI login session (macOS background sessions can't open the window).
- No auto-rejoin after NewEarth restarts.
- MCP listens on all interfaces by default.
- Don't run Bob on two hosts with the same login at once.
- Upstream AGENTS.md rules (video reports, Telegram, checkpoints) are the upstream author's workflow, not Randy's.
