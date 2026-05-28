# Claude Code Skills + Rules Package

A curated set of Claude Code skills, rules, and config refined over ~6 months of daily use.

## What's here

```
claude-config-share/
├── README.md                 ← you are here
├── INSTRUCTIONS.md           ← copy-paste prompt to install everything
├── CLAUDE.md.example         ← template for your ~/.claude/CLAUDE.md
├── skills/                   ← 7 skills, each in its own folder with SKILL.md
│   ├── project-manager/
│   ├── ship/
│   ├── session-handoff/
│   ├── daily-review/
│   ├── weekly-review/
│   ├── skill-creator/
│   └── research/
│       └── scripts/perplexity_research.py
└── rules/                    ← 15 discipline + workflow rules, auto-load globally
    ├── communication-style.md
    ├── coding-conventions.md
    ├── git-conventions.md
    ├── development-workflow.md
    ├── concurrent-sessions.md
    ├── session-types.md
    ├── best-of-best-modeling.md
    ├── brainstorming-question-filter.md
    ├── pushback-on-request.md
    ├── tdd-loop-discipline.md
    ├── use-gha-not-local-ci.md
    ├── no-bash-heredocs.md
    ├── autopilot-and-scope-checks.md
    ├── starter-prompt-code-block-integrity.md
    └── session-handoffs-required.md
```

## Quick start

Open `INSTRUCTIONS.md`. It has a copy-paste prompt that installs everything in 30 seconds.

## What's NOT here

These are the workflow + discipline layer. They sit on top of:

- **Claude Code** — the CLI / VS Code extension (https://claude.com/claude-code)
- **Superpowers** (Obra) — the brainstorm → plan → execute → review chain (https://github.com/obra/superpowers) — install this BEFORE using these skills
- Optional: **Perplexity API** — required only for the `research` skill
- Optional: **GitHub CLI** — used by `ship` and `project-manager` for PR ops

## License

Use it, fork it, change anything you want. No attribution required.
