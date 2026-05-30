# Architecture

How the pieces of this kit fit together, and how a session flows through them. If the README answers *what's in the box* and INSTRUCTIONS answers *how to install it*, this answers *how it works*.

## The layers

```
┌─────────────────────────────────────────────────────────────┐
│  ~/.claude/CLAUDE.md          who you are, what you build,    │
│                               which rules + skills to honor   │
├─────────────────────────────────────────────────────────────┤
│  ~/.claude/rules/*.md         DISCIPLINE — auto-loaded into   │
│  (18 rules)                   every conversation, always on   │
├─────────────────────────────────────────────────────────────┤
│  ~/.claude/skills/<name>/     WORKFLOWS — dormant until a      │
│  (13 skills)                  trigger phrase fires them        │
├─────────────────────────────────────────────────────────────┤
│  Superpowers (Obra)           the brainstorm → plan → execute  │
│  [required dependency]        → verify → review ENGINE          │
├─────────────────────────────────────────────────────────────┤
│  MCP servers (optional)       code-review-graph (code nav),    │
│                               others you register yourself      │
└─────────────────────────────────────────────────────────────┘
```

Three things make this work as a system rather than a pile of files:

1. **Rules are always on; skills are on-demand.** A rule shapes behavior whether or not you remember it exists (how commits are written, how parallel sessions stay safe, how questions are framed). A skill is a workflow that only runs when its `description` trigger phrases appear. This split is deliberate: discipline you can't forget to apply, workflows you invoke when you need them.

2. **Skills compose with Superpowers, they don't replace it.** Superpowers provides the generic chain — `brainstorming`, `writing-plans`, `subagent-driven-development`, `verification-before-completion`, `requesting-code-review`. The skills here are the opinionated layer on top: `project-manager` orchestrates that chain with scope checks and branch hygiene; `ship` closes the loop with version/changelog/deploy verification. Install Superpowers first or the chain degrades to narration.

3. **The MCP layer is optional and additive.** The four code-graph skills drive the `code-review-graph` MCP server. Skip the server and those four skills simply have nothing to act on — every other skill and rule is unaffected. Nothing in the core kit hard-depends on an MCP server.

## What loads when

| Moment | What happens |
|---|---|
| Session start | `~/.claude/CLAUDE.md` + every file in `~/.claude/rules/` loads into context. Skills register by `name` + `description` (bodies stay unloaded). |
| You type a trigger phrase | The matching skill's `SKILL.md` body loads and the agent follows it. |
| Skill references `superpowers:*` | That Superpowers skill loads and runs as a sub-step. |
| A code-graph skill runs | It calls `code-review-graph` MCP tools (after a freshness check). |
| Session end | `ship` or `session-handoff` runs the close-out; `session-loose-ends-audit` accounts for every idea raised. |

## A session, end to end

1. **Morning** — "morning check" fires `daily-review`, which surfaces today's work without re-planning the week.
2. **Start a project** — "let's work on X" fires `project-manager`. It reconciles the starter prompt against the canonical scope (catching drift), opens a feature branch + draft PR, then runs Superpowers `brainstorming` → `writing-plans`.
3. **Build** — `subagent-driven-development` (or inline execution) implements the plan task by task, each with TDD and a review pass. On a large repo, `explore-codebase` / `debug-issue` / `refactor-safely` answer structural questions via the code graph instead of blind greps.
4. **Review** — `review-changes` scores the diff by blast radius and test coverage; pair it with an adversarial pass on risky surfaces.
5. **Close** — "ship it" fires `ship`: version bump, progress log, report, commit, push, PR merge, and a live-URL verification before declaring done. `session-loose-ends-audit` ensures no idea raised mid-session is silently dropped.
6. **Out of context** — if the conversation got long before the work is done, `session-handoff` emits a paste-ready block so a fresh conversation resumes cleanly.

## Dependency graph

```
claude-code-starter-kit
├── requires:  Claude Code (or any agentic CLI honoring markdown skills/rules)
├── requires:  Superpowers (Obra)            ← the workflow engine the skills call
├── optional:  Perplexity API key            ← only the `research` skill
├── optional:  GitHub CLI (gh)               ← `ship`, `project-manager` PR ops
├── optional:  code-review-graph MCP         ← the four code-graph skills
└── optional:  Codex plugin                  ← `/codex:*` adversarial review refs
```

Everything optional fails soft: the dependent skill degrades or sits idle, and the rest of the kit keeps working.

## Design principles baked into the kit

- **Mechanical gates over prose.** Where a rule can be enforced by a CI grep or a lint check, build the gate — documentation alone doesn't change behavior (see `rules/sharp-edges-convention.md`).
- **One decision made once.** Branch-by-default, single-file test iteration, push-to-CI — each is a default set once in a rule instead of re-negotiated every session.
- **Fail toward pausing.** Scope anomalies, ambiguous destructive actions, and precondition violations interrupt even "full autopilot" (see `rules/autopilot-and-scope-checks.md`).
- **Nothing is canonical.** Every file is standalone markdown. Pluck one rule, edit any skill, ignore the rest. The kit is a starting point, not a contract.
