# AI CHANGELOG — `geekdadteach/unionclef` (NewEarth / Bob deployment notes)

Append one dated entry per logical change, newest first. Never erase prior entries. This log covers the NewEarth fork's own docs and deployment; upstream code history is in git and `docs/releases/`.

### 2026-11-13 ~11 PM ET — MineBobNE (Grok Bot) — Takeover handoff for Bob's deployment
- **Goal:** Randy's 13 Nov 2026 order: docs any AI can use to take over immediately.
- **Files:** `docs/AI_HANDOFF.md` (new), `docs/AI_CHANGELOG.md` (new), `AGENTS.md` (short NewEarth pointer block prepended; upstream content unchanged).
- **Code:** none. `main` mod code untouched.
- **Tests:** docs-only; gitleaks scan before push (public repo).
- **Deploy status:** none; no host changed. Bob is off.
- **Rollback:** revert the PR.
