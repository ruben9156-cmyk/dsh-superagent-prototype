# AGENTS.md

`dsh-superagent` is a proactive coding agent for one GitHub repository, packaged as a bundle for [DeepSeek Harness](https://www.deepseek.com/en/harness/). It listens to GitHub and optionally Feishu, turns authorized requests into Tickets, and executes each in its own DSH Session. Research prototype; see `README.md`.

## Stack

- Node.js `^22.19.0 || >=24.0.0`; package manager pnpm `11.7.0` (pinned in `package.json`).
- TypeScript `6.0.3`, strict, ESM (`NodeNext`), target `ES2024`, `noUncheckedIndexedAccess`, `verbatimModuleSyntax`.
- Tests: vitest `4.1.8`; build: tsdown `0.22.14` (output `lib/`, gitignored).
- Peer deps: `@deepseek-ai/*` (`cordis 4.0.5-alpha.1`, `dsh-* 0.2.1-alpha.2`).

## Commands

- `pnpm install` — install dependencies.
- `pnpm typecheck` — `tsc --noEmit`.
- `pnpm test` — `vitest run` (files `tests/**/*.spec.{ts,tsx}`).
- `pnpm build` — tsdown build to `lib/`.
- `pnpm check` — typecheck + test + build.
- `scripts/dsh-superagent {start|status|restart|stop}` — manage the running service (`--profile`, `--url`).

## Layout

- `src/core/` — domain model, ports, Engine and in-memory repositories.
- `src/app/` — Runtime (composition root), passes, outbox, notices, settings, setup, views.
- `src/sources/` — Source kinds (`github`, `feishu`) and their plugins.
- `src/executor/` — DSH executor and background handling.
- `src/client/` — React management panel (browser bundle).
- `src/infra/` — commands, environment, health, paths, text.
- `tests/` — 60 spec files mirroring `src/`.
- `docs/` — numbered docs `000`–`008`; read in order; `007-architecture.md` is the code index.
- `prompts/`, `skills/`, `locale/`, `scripts/`, `examples/`.

## Conventions

- No lint or formatter is configured (no eslint/prettier). Match existing style.
- Relative imports keep `.ts` extensions (`rewriteRelativeImportExtensions`).
- React tests dedupe `react`/`react-dom` (see `vitest.config.ts`).
- `docs.spec.ts` requires `docs/007-architecture.md` module and test tables to list exactly the files in `src/` and `tests/`. Update that doc when adding or removing files.
- New Source kinds follow `docs/008-cookbook.md`.

## Gotchas

- `lib/` and `node_modules/` are gitignored; run `pnpm build` before deploying.
- The plugin refuses to start without `stateDir` set in the profile's `cordis.patch.yml` (see `docs/006-deployment.md`).
- Re-running `dsh superagent --from-default-profile` on an existing profile replaces it and deletes its `cordis.patch.yml`; edit instead.
- Agent presets (`dsh-superagent-worker`, `dsh-superagent-router`) must stay in `cordis.patch.yml` while their Sessions are in use.