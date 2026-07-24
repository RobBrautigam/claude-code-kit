# <Repo Name>

> This file lives at the repo root as `CLAUDE.md` and auto-loads into every Claude Code conversation in this repo. Keep it under a screen or two - it costs context in EVERY session, so earn every line. Global/personal config belongs in `~/.claude/CLAUDE.md`, not here.

<What this project is, in 2-3 sentences: what it does, who it's for, what "production" means for it (a deployed URL, a published package, an internal tool).>

## Stack

- <Language + framework, e.g. TypeScript / Next.js 15 / Tailwind>
- <Database / services, e.g. Postgres via Supabase>
- <Hosting / deploy, e.g. auto-deploys to X on push to main>

## Commands

```bash
<install command>        # e.g. pnpm install
<dev command>            # e.g. pnpm dev  (runs at localhost:3000)
<test command - scoped>  # e.g. pnpm test:file <path>  (prefer scoped over the full suite)
<deploy / release>       # e.g. push to main -> auto-deploy, or the release script
```

## Conventions

- <Branching: e.g. feature branches feat/<slug>, merge via PR, keep main deployable>
- <Code style notes that are NOT obvious from linters, e.g. "server components by default">
- <Where things live: e.g. routes in src/app/, shared UI in src/components/>

## Sharp edges (the foot-guns - keep this to the top 3-5)

- <e.g. "The seed script WIPES the local database - never run against prod vars.">
- <e.g. "ENV_X must be set in the platform first, then pulled - local .env is not source of truth.">
- <e.g. "The build silently succeeds without ENV_Y but the deployed app crashes at runtime.">

## Structure of `.claude/` in this repo

- `.claude/rules/` - repo-specific rules that auto-load here (commit these).
- `.claude/skills/` - repo-local skills, only if this repo needs its own (commit these).
- `.claude/settings.local.json` - personal/machine-local; keep it in `.gitignore`.
