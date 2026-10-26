# Boots

**Boots** is a reproducibility report: do recent hackathon-*winning* repos actually run from their README?

We picked 10 projects that won a prize (finalist or sponsor/track prize) at Devpost or ETHGlobal events in 2025–2026, cloned each one fresh, and followed **only** the setup steps in its README — then recorded exactly what happened.

**Read the findings in [`REPORT.md`](REPORT.md).** Machine-readable data: [`results.json`](results.json). Per-repo command logs: [`logs/`](logs/).

## Method

1. **Selection** — 10 prize winners across ETHGlobal Taipei / Prague / New York / Cannes 2025, Hack the North 2025, Cal Hacks 12.0 (2025), and UCSC AI Hackathon 2026. Each has a public GitHub repo in JavaScript/TypeScript or Python with setup instructions in its README. App types are mixed: games, DeFi, payments, AI agents, dev tools, OSINT, accessibility, IoT.
2. **Testing** — each repo got a fresh directory and a 10-minute timebox. We ran the README's documented steps **in order**: clone → install → build/test/start. No real credentials; placeholder values only where the README ships an example env file. The commit SHA tested is recorded per repo.
3. **Classification** — exactly one status per repo:
   - `RUNS` — works exactly as documented
   - `RUNS WITH FIX` — works after a small change (the exact change is recorded)
   - `NEEDS SECRETS` — stops *only* because it needs an API key, wallet, or paid service (not counted as broken)
   - `BROKEN` — fails for any other reason

   For non-running repos we record the first failing command and the key error lines.

## Repo layout

```
boots/
├── README.md          # this file
├── REPORT.md          # summary table, counts, failure causes, checklist
├── results.json       # one object per repo: all fields + status + errors + suggested fix
├── logs/<repo>.txt    # trimmed per-repo command logs
├── repos/             # the cloned repos at the tested commit (not pushed to GitHub)
└── LICENSE            # MIT
```

## How to re-run

Prerequisites: Linux with git, Node ≥ 18, Python 3.12, pnpm, bun, Foundry, Docker + Compose v2 (matching what the READMEs require).

```bash
# pick any repo from results.json, e.g.:
git clone <repo_url> && cd <repo>
git checkout <commit_sha from results.json>
# then follow the repo's README exactly, in order — that's the whole test.
```

To re-run our exact harness: each `logs/<repo>.txt` lists, per repo, the precise commands executed (each line starts with `--- cmd:`), in the order executed, followed by their output and exit codes.

## Notes & limitations

- Results reflect the pinned commit SHA on the test date (2026-10-26), in a Linux x86_64 container. Repos may have been fixed since; one repo (FaceTimeOS) is documented as a Mac product and was tested on Linux, which its README does not claim to support.
- A 10-minute timebox and a 30 GB sandbox disk are part of the test; where those constraints bit (multi-GB GPU dependency stacks), it is noted in the repo's log and notes rather than silently counted.

## License

MIT — see [LICENSE](LICENSE). All tested projects remain the property of their authors.
