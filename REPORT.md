# Boots Report — Do hackathon-winning repos run from their README?

**Question:** If a judge clones a recent hackathon-winning repo and follows only its README, does it run?

**Method:** 10 projects that won a prize (finalist or sponsor/track prize) at Devpost or ETHGlobal events in 2025–2026, each with a public JavaScript/TypeScript or Python repo and setup instructions in its README. Each repo was cloned fresh (commit SHA recorded) into a clean directory and set up using *only* the README's documented steps, in order, with a 10-minute timebox. No real credentials were used; placeholder values were used only where the README provides an example env file. Full detail per repo: `results.json` + `logs/<repo>.txt`.

**Test environment:** Linux x86_64 container, Node 24, Python 3.12, pnpm 12, bun 1.3, Foundry 1.8, Docker 20.10 + Compose v2.

## Results

| # | Project | Event | Prize | Lang | Status |
|---|---------|-------|-------|------|--------|
| 1 | Nomadia | ETHGlobal Taipei 2025 | Finalist | TS | ✅ RUNS |
| 2 | (EF) EthereumFighter | ETHGlobal Taipei 2025 | Finalist + 1inch Fusion+ 3rd + Celo pool | TS | 🔧 RUNS WITH FIX |
| 3 | Decycle (garbage-detector) | ETHGlobal Prague 2025 | Finalist + Blockscout pool | Py | ❌ BROKEN |
| 4 | Primer | ETHGlobal New York 2025 | Finalist + Coinbase CDP prize | TS | 🔧 RUNS WITH FIX |
| 5 | Pumpkin Spice Latte | ETHGlobal New York 2025 | Finalist + Flare 1st + Flow pool + katana | TS | 🔧 RUNS WITH FIX |
| 6 | Hashlocked CLI | ETHGlobal Cannes 2025 | Finalist + 1inch Non-EVM Fusion+ 1st | TS | ❌ BROKEN |
| 7 | livestakes | ETHGlobal Cannes 2025 | Finalist | TS | ❌ BROKEN |
| 8 | Proprio | UCSC AI Hackathon 2026 (Devpost) | 2nd Place, ASUS Local AI | Py | ✅ RUNS |
| 9 | Deepsint | Hack the North 2025 (Devpost) | 2nd Place, Cohere track | Py | 🔧 RUNS WITH FIX |
| 10 | FaceTimeOS | Cal Hacks 12.0, 2025 (Devpost) | 1st Place Grand Prize | Py+TS | ❌ BROKEN |

## Overall counts

| Status | Count |
|---|---|
| RUNS | 2 |
| RUNS WITH FIX | 4 |
| NEEDS SECRETS | 0 |
| BROKEN | 4 |

**6 of 10 winning repos could be gotten running; only 2 of 10 ran exactly as their README said.** No repo landed in NEEDS SECRETS as its *first* blocker — in the one repo that truly needs private keys to function (Hashlocked), an undocumented `npm install` step fails first.

## The 3 most common failure causes

1. **README steps don't match the actual repo layout (5 repos).** Commands documented for the repo root while the app lives in a subdirectory (EthereumFighter), paths that no longer exist (`packages/nextjs` in Pumpkin Spice Latte, `apps/extension/package.json` in Primer), a README describing a different program than the file it says to run (Decycle), or a missing install step entirely (Hashlocked).
2. **Unpinned dependencies + toolchain drift (4 repos).** `@openzeppelin/contracts` v4 pinned where the code needs v5 (Pumpkin Spice Latte); pnpm ≥ 12's new `ERR_PNPM_IGNORED_BUILDS` default breaking `pnpm install`/`make setup` (Nomadia — cosmetically, livestakes — fatally); `docker-compose` v1 syntax in a Makefile whose compose file needs v2 (livestakes); a documented PyPI package that doesn't exist (Deepsint's `blackbird-osint`).
3. **Undocumented environment requirements (4 repos).** A required `.env` that no step tells you to create (FaceTimeOS's `Agent-S/.env`, livestakes' server DB config), undocumented system packages (PortAudio), hard OS/hardware assumptions (macOS iMessage DB, Jetson camera) that only surface at runtime.

## How to make your hackathon repo run for judges

Derived only from what we observed:

1. **Before submitting, clone your own repo into a fresh directory and follow your README verbatim.** Every BROKEN/RUNS-WITH-FIX repo would have been caught by this single step.
2. **Include the install command.** One repo's README jumped straight from "create .env" to `npm run …` without `npm install`.
3. **Give the working directory for every command** (`cd app && …`) — or add a root-level script/workspace so commands work from the root.
4. **Commit a `.env.example` with placeholder values and say which vars are required vs optional.** The two RUNS repos both did this; it let us run them with zero real credentials.
5. **Pin your dependencies** (lockfile, exact versions, git submodules) — especially fast-moving ones like OpenZeppelin, and note which package-manager major version you used.
6. **State hard prerequisites up front**: OS (e.g. "macOS only"), system packages, Docker, hardware. Judges should learn this from the README, not from a stack trace.
7. **Provide a no-secrets, no-hardware smoke path** — a demo/fake mode or bundled sample input. Proprio did this well (`python main.py` runs entirely on fakes) and was one of the two clean runs.
8. **Keep docs in sync when you move code.** Most failures were stale paths/script names, not bad code — the code usually worked once we found it.

## Reproduce

See `README.md` for how to re-run. Raw data: `results.json`; per-repo logs: `logs/`.

**Live re-run verification (2026-10-26):** Deepsint was re-tested end-to-end from a fresh clone in a clean venv — same commit (`4966afa`), same first failing command (`pip install blackbird-osint`, package still absent from PyPI), same documented source-install fix, app served HTTP 200, end-to-end search executed. Status reconfirmed: `RUNS WITH FIX`. Full command trail appended to `logs/deepsint.txt`.
