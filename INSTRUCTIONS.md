# Onboarding — Claude Code skills + rules

This package contains a curated set of Claude Code skills and rules I've been refining for ~6 months. They sit on top of two open frameworks:

- **Superpowers** by Obra (https://github.com/obra/superpowers) — the brainstorm → plan → execute → review chain referenced throughout. Install that first.
- The rules and skills in this package — discipline + workflow on top of Superpowers.

## Quick install (the 30-second version)

Paste this prompt into a fresh Claude Code conversation **inside the directory where you unzipped this package**:

````
I have a folder of Claude Code skills and rules I want to install. The folder structure is:

  skills/        — each subfolder is a skill (SKILL.md inside)
  rules/         — each file is a discipline / workflow rule
  CLAUDE.md.example — example global config

Please install them at the global Claude Code level (~/.claude/) so they apply to every repo. Specifically:

1. Copy every subdirectory under skills/ to ~/.claude/skills/ (preserving the SKILL.md and any bundled files like scripts/).
2. Copy every file under rules/ to ~/.claude/rules/.
3. Show me my current ~/.claude/CLAUDE.md (if it exists). If it doesn't exist, copy CLAUDE.md.example to ~/.claude/CLAUDE.md and tell me to edit the placeholders. If it exists, do NOT overwrite — show me the diff against CLAUDE.md.example and ask whether I want to merge, replace, or leave it alone.
4. Verify the install: list ~/.claude/skills/ and ~/.claude/rules/ so I can see what landed.
5. Tell me to restart Claude Code (or start a fresh conversation) so the new skills auto-register.

Use the Bash tool for file copies. On Windows use PowerShell syntax; on macOS / Linux use cp -R.
````

That's it. Claude Code will copy the files, show you what got installed, and tell you what to edit.

---

## What's in this package

### Skills (8)

| Skill | What it does | When to use |
|---|---|---|
| `project-scaffolder` | Scaffolds a new project from minimal input: `projects/<slug>/` with rich README (origin brief, definition of done, copy-paste starter prompt) plus empty `plan.md` and `report.md` stubs. Supports spin-off mode for ideas that surface mid-project. | Any time you start a new project, OR mid-session when an unrelated idea comes up that you want to capture. Trigger: "start a new project", "scaffold X", "spin off Y". |
| `project-manager` | Session guardrails. Runs the full Superpowers chain (brainstorm → plan → execute → verify → review → ship), creates feature branches + draft PRs at session start, enforces scope discipline. | Any project session. Trigger: "let's work on X", "next project", or just paste a starter prompt. |
| `ship` | Session-close workflow. Version bump, progress log, patch / release report, commit, push, PR merge, deploy verification. | At the end of every session that produced meaningful changes. Trigger: `/ship`, "ship it", "wrap up". |
| `session-handoff` | Structured end-of-session summary for `/clear` + fresh conversation. NOT a project close — this is for context continuity when the conversation is getting long. | When context is getting heavy and you want to start fresh without losing the thread. Trigger: "session handoff", "save context", "prep a handoff". |
| `daily-review` | Lightweight 2-minute morning check-in. Surfaces today's scheduled projects, asks for quick adjustments. | First thing in the morning. Trigger: `/daily-review`, "what should I work on today", "morning check". |
| `weekly-review` | 15-25 minute weekly planning. Curates the todo queue, assigns target dates, audits stuck-in-progress items. | Once a week, ideally Sunday evening or Monday morning. Trigger: `/weekly-review`, "plan my week". |
| `skill-creator` | Create new skills and iteratively improve existing ones. Includes the philosophy + structure of a good skill. | When you notice a workflow repeating 3+ times. Trigger: "create a skill", "turn this into a skill". |
| `research` | Deep, context-aware research using the Perplexity API. Runs multi-angle queries, synthesizes through your business lens, saves a full report. | When you need real research, not just a websearch. Requires a Perplexity API key. Trigger: "research X", "look into Y", "dig into Z". |

### Rules (15)

All rules auto-load in every conversation. They cover:

| Rule | What it enforces |
|---|---|
| `communication-style.md` | Tone, formatting, no buzzwords, lead with recommendations |
| `coding-conventions.md` | Language defaults, testing, linting, security baseline |
| `git-conventions.md` | Lowercase commits under 72 chars, always branch + draft PR |
| `development-workflow.md` | Localhost-first, shadcn/ui, design systems, no orphaned processes |
| `concurrent-sessions.md` | Multi-conversation safety — worktrees mandatory for parallel work |
| `session-types.md` | How to identify quick fix vs project vs strategy session at the start |
| `best-of-best-modeling.md` | Model Linear / Stripe / Anthropic / Vercel in every decision |
| `brainstorming-question-filter.md` | Lead with a recommendation, never pure-options questions |
| `pushback-on-request.md` | Genuine challenge when asked, not validation in disguise |
| `tdd-loop-discipline.md` | Single-file tests during iteration, full suite at preflight only |
| `use-gha-not-local-ci.md` | Push to CI, don't burn local CPU on full builds |
| `no-bash-heredocs.md` | Never heredocs through the Bash tool — use the Write tool instead |
| `autopilot-and-scope-checks.md` | Scope anomalies pause autopilot, even when "full autopilot" was authorized |
| `starter-prompt-code-block-integrity.md` | Use 4-backtick fences for any prompt containing inner code blocks |
| `session-handoffs-required.md` | Always deliver starter prompt + ship + summary when ending a session |

### CLAUDE.md.example

A template for your global `~/.claude/CLAUDE.md` file. This is what tells Claude Code who you are, what you're building, and which rules / skills to honor by default. Edit the placeholders (`<Your Name>`, `<Role>`, tech stack) before installing.

---

## How the pieces fit together

The mental model:

```
~/.claude/CLAUDE.md            ← who you are, what you're building, points at rules
~/.claude/rules/*.md           ← discipline, auto-loaded into every conversation
~/.claude/skills/<name>/       ← workflows, invoked when their trigger phrases hit
```

A typical session:

1. **Morning:** "morning check" → invokes `daily-review` → surfaces today's projects.
2. **Start a project:** "let's work on the auth migration" → invokes `project-manager` → reads README, scope-checks, creates feature branch, runs Superpowers brainstorming → writes a plan → executes → verifies.
3. **End session:** "let's ship" → invokes `ship` → bumps version, writes progress log + report, commits, pushes, merges PR, verifies deploy.
4. **Context heavy mid-project:** "session handoff" → invokes `session-handoff` → produces a chat-only summary you can paste into a fresh conversation.
5. **Weekly:** "weekly review" → invokes `weekly-review` → curates the queue for the coming week.
6. **Research:** "research the AI agency pricing landscape" → invokes `research` → 3-5 Perplexity calls + synthesis + saved report.
7. **Notice a pattern:** "this workflow keeps repeating, make it a skill" → invokes `skill-creator` → drafts + tests a new skill.

---

## Required dependencies

### Superpowers (mandatory)

Many of these skills reference `superpowers:brainstorming`, `superpowers:writing-plans`, `superpowers:subagent-driven-development`, etc. These come from the Superpowers plugin by Obra.

Install: https://github.com/obra/superpowers

Until Superpowers is installed, the `project-manager` and `ship` skills will reference skills that don't exist yet — the workflow degrades gracefully (Claude will narrate the steps instead of invoking the skill) but the full value comes from having Superpowers installed.

### Perplexity API key (only for the research skill)

Get one at https://www.perplexity.ai/settings/api. Then either:

```bash
export PERPLEXITY_API_KEY=pplx-...
```

Or add to `~/.claude/.secrets.env`:

```
PERPLEXITY_API_KEY=pplx-...
```

The research script checks env var first, then standard secret locations.

### GitHub CLI (recommended)

Used by the `ship` and `project-manager` skills for PR creation, status checks, and merges:

```bash
# Install: https://cli.github.com/
gh auth login
```

---

## Adapting to your stack

These skills are written to be tracker-agnostic. Where the original versions queried a Supabase database for project records, the white-labeled versions say "your task tracker — substitute filesystem READMEs, ClickUp, Linear, GitHub issues, or whatever you actually use."

The recommended starting point: filesystem-only. Every project gets a `projects/<slug>/` folder with `README.md` + `plan.md` + `report.md`. The skills assume this layout by default. Upgrade to a database / Linear / etc. later if you outgrow files.

---

## Customizing further

The fastest way to make these your own:

1. Install as-is using the prompt above.
2. Use them for a week.
3. Notice anything that doesn't fit your workflow.
4. Edit the rule or skill file directly at `~/.claude/rules/<file>.md` or `~/.claude/skills/<skill>/SKILL.md`.
5. Restart Claude Code (or start a fresh conversation) — your edits load on next session.

Skills are just markdown files. There's no compile step, no rebuild, no plugin manifest. Edit and reload.

---

## Troubleshooting

**"The skill isn't triggering when I expect."**
Read the skill's frontmatter `description` field. The triggering language is in there. If your wording is too far from what the description names, the skill won't fire. Either rephrase to match, or edit the description to include your phrasing.

**"The skill triggered but produced something weird."**
Read the SKILL.md body. It's a markdown spec — Claude follows it. If the spec has gaps, fill them. If the spec is wrong for your case, edit it.

**"Two skills are fighting for the same trigger."**
Edit one of the descriptions to be narrower. Skills cooperate via specificity — the one with the more specific match wins.

**"Restarting Claude Code didn't pick up my edit."**
On macOS / Linux: fully quit (`Cmd+Q` or `pkill -f claude-code`) and reopen. On Windows: close the VS Code window AND the Claude Code process from Task Manager.

---

That's the whole package. Use it, fork it, change anything you want.
