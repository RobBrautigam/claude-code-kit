# claude-code-kit

An opinionated discipline and workflow layer for Claude Code. 8 skills, 15 rules, a global `CLAUDE.md` template, and a copy-paste install prompt.

This is an opinionated discipline and workflow layer for Claude Code. You get 8 skills (`project-scaffolder`, `project-manager`, `ship`, `session-handoff`, `daily-review`, `weekly-review`, `skill-creator`, `research`), 15 rules covering communication, git, testing, parallel sessions, autopilot scope checks, and the rest of the daily friction surface, plus a global `CLAUDE.md` template and a copy-paste install prompt. It sits on top of [Obra's Superpowers plugin](https://github.com/obra/superpowers), which provides the brainstorm-to-plan-to-execute-to-review chain. Superpowers is the engine. This kit is the operating system around it. The whole thing has been refined over roughly six months of daily use by one builder who works entirely through Claude Code conversations rather than typing code by hand, often running multiple parallel sessions across many repos.

---

## Install (the 30-second version)

```bash
git clone https://github.com/RobBrautigam/claude-code-kit.git
cd claude-code-kit
```

Then paste this prompt into a fresh Claude Code (or Codex, or any agentic CLI) conversation **inside the cloned directory**:

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

That's it. The agent will copy the files into `~/.claude/`, show you what landed, and tell you which placeholders to edit. Restart Claude Code and the rules auto-load into every conversation.

For the deep onboarding doc with more context, see [INSTRUCTIONS.md](./INSTRUCTIONS.md).

---

## Why it exists

Superpowers gives you the workflow chain. What it doesn't give you is the discipline layer that makes that chain survive contact with real daily use across multiple repos, parallel sessions, and long-running projects. Without opinionated defaults, every conversation is a fresh negotiation: how should Claude commit, when should it push back, what counts as "done", how should it hand off context, what should it do when it notices a scope drift. You can answer those questions over and over, or you can answer them once in a rules file and never think about them again.

This kit exists because the same failure modes kept showing up across hundreds of sessions. Two parallel Claude conversations on the same repo silently corrupt each other's working tree when one runs `git checkout` while the other has uncommitted work. A starter prompt drifts under context pressure until it covers 25% of the original project scope, and the next session ships the wrong thing without noticing. A vitest watch loop reruns 600 tests on every save and pegs CPU for three hours because nobody told Claude to scope to the changed files. A handoff prompt with an inner code fence breaks the outer wrapper and arrives as three disconnected code blocks instead of one copy-paste. A bash heredoc dies on an apostrophe in prose content. A session ends without a handoff and the next conversation reinvents decisions that were already made. Each of these is a one-line rule away from being prevented permanently.

Think of this as one person's refined config, made shareable. It is not a framework. It is not trying to be canonical. It is what works for one builder after six months of daily iteration. Take what fits, leave the rest, modify what's close but not quite right.

---

## Who it's for

Anyone running Claude Code as their daily development environment, especially people working across multiple repos, people running parallel Claude sessions, people who want consistent discipline without re-explaining the same five things every conversation, and people who build features through Claude conversations rather than by typing code by hand. If Claude Code is a tool you open occasionally to autocomplete a function, this is overkill. If it is the way you ship software, the compounding return on having opinionated defaults is hard to overstate.

---

## How to think about it

There are three ways to adopt this kit. The cleanest is to install everything globally as a baseline: drop the rules into `~/.claude/rules/`, the skills into `~/.claude/skills/`, point your `~/.claude/CLAUDE.md` at the template, and let every Claude Code session inherit the full discipline layer. If you trust the defaults, this is the highest-leverage move and takes about ten minutes.

The second pattern is rules globally, skills selectively. Rules auto-load into every conversation and shape behavior whether you remember them or not (how Claude commits, how it handles parallel sessions, how it asks questions during brainstorming, how it ends sessions). Skills only fire when their trigger phrases come up. Installing seven skills costs nothing if you only end up triggering two of them.

The third pattern is pluck what you want. Every file is standalone. Steal a single rule, a single skill, or a single section of the `CLAUDE.md` template into your own config. Nothing here depends on anything else.

If you want to start small, the highest-leverage starter set is five files: [`communication-style.md`](./rules/communication-style.md) (kills the corporate filler that makes Claude responses three times longer than they need to be), [`git-conventions.md`](./rules/git-conventions.md) (consistent commits, branches, identity safety), [`concurrent-sessions.md`](./rules/concurrent-sessions.md) (the single biggest source of cross-session corruption is two Claude conversations sharing one working tree, and this rule prevents it), the [`session-handoff`](./skills/session-handoff/) skill (so context survives `/clear`), and [`session-types.md`](./rules/session-types.md) (so Claude knows whether you're fixing a bug, building a feature, or thinking out loud, and adapts accordingly). Five files, immediate compounding returns. Add the rest as the patterns they describe start showing up in your own work.

---

## The skills (8)

Skills only fire when their trigger phrases appear in a conversation. You can install all 8 with zero overhead; the ones you don't trigger stay dormant. Each skill has its own folder with a `SKILL.md`, and some bundle scripts or references.

### [`project-scaffolder`](./skills/project-scaffolder/)

**Scaffolds a new project from minimal input: creates `projects/<slug>/` with a rich README (origin brief, definition of done, copy-paste starter prompt) plus empty `plan.md` and `report.md` stubs.**

The README a one-liner produces is useless six months later — the rich-context README this skill produces is the artifact that lets a fresh Claude Code session pick up the project cold without needing the original conversation. It also includes a `starter_prompt` block that's literally copy-paste-ready for the next session. Spin-off mode (the most common variant) captures ideas that surface mid-project: you invoke the scaffolder with the parent project's slug, it records the lineage and the verbatim trigger, and you return to the original work without losing the tangent. The cost of scaffolding is 30 seconds; the cost of forgetting a good idea is whatever the idea was worth.

- **When to use:** "start a new project", "create a project", "scaffold X", "add this as a project", any multi-step build request in a fresh conversation, OR mid-session when an unrelated idea surfaces that you want to capture without derailing the current work. The `session-types` rule routes ad-hoc builds here automatically.
- **When not to use:** Skip for one-sentence bug fixes (branch + fix + ship is faster), for strategy conversations with no buildable output, and for renaming or editing an existing project.

### [`project-manager`](./skills/project-manager/)

**Enforces a feature-branch + draft-PR + Superpowers-chain workflow at session start, with a scope reconciliation step that catches starter-prompt drift before you write code.**

Starter prompts go stale. You paste one into a fresh session, the model takes it at face value, and three hours later you discover the prompt silently dropped two of the four pages the project was supposed to ship, because the canonical scope lived in the README and a sibling project's dependency list. This skill makes scope reconciliation a hard step before brainstorming, so the divergence surfaces as a delta table instead of as a missed deliverable. It also gets the branch and draft PR opened immediately, which means parallel work is visible in Slack/GitHub from minute one rather than after the first push two hours in.

- **When to use:** Triggers on "let's work on X", "next project", "continue X", "what's next", or any paste of a starter prompt naming a project. Activates at the moment a multi-step build is about to begin, before any brainstorming or code. If a saved plan exists at `projects/<slug>/plan.md`, it resumes; otherwise it forces a brainstorm pass.
- **When not to use:** Skip for one-sentence bug fixes (branch, fix, push, ship is faster), strategy conversations with no implementation, and pure exploratory questions. You also do not need this if your projects are small enough that scope drift between sessions is not a real failure mode for you.

### [`ship`](./skills/ship/)

**A session-close checklist that handles version bump, progress log, patch or release report, README updates, commit, push, and PR merge in one orchestrated pass.**

Most sessions end with half the closing work done. The version got bumped but the progress log didn't, or the PR merged but the README still shows unchecked boxes, or the deploy "succeeded" but nobody hit the live URL to confirm the new affordance actually renders. Ship runs the same seven steps every time, in parallel where it can, and refuses to declare done until the change is verified live in production (not just merged). It also forces a preflight review checkpoint, which catches the kind of bug you'd otherwise discover in the next session when you're already three steps into something else.

- **When to use:** Triggers on `/ship`, "let's close out", "ship it", "wrap up this session", "done", or "that's it for today". Activates at the natural end of any session that produced code changes, after the work is committed but before you context-switch to something else.
- **When not to use:** Skip for pure research or planning sessions that produced no code, and for sessions whose only output is a plan file. You also don't need this if you genuinely enjoy hand-rolling release notes at 11pm.

### [`session-handoff`](./skills/session-handoff/)

**Generates a structured chat-only handoff so you can `/clear` a long conversation and pick up cleanly in a fresh one.**

Long Claude Code sessions hit context limits, and "summarize what we did" produces a wall of prose that misses the load-bearing details: background shell IDs you can no longer find, the plan file the new agent should read first, the database row you already mutated, the Playwright session name that will collide if reused. This skill enforces a fixed template covering decisions, files (with absolute paths), running state, verification commands, and deferrals, so the next agent reads one block and knows where to resume. It also draws a hard line against shipping behavior: no commits, no version bumps, no files written, just a paste-ready block. The failure mode it prevents is the new session redoing work, missing an in-flight background process, or stepping on a database write the previous session already committed.

- **When to use:** "session handoff", "hand off", "prep a handoff", "save context", "wrap up before I clear", or any moment you're about to `/clear` a long conversation and want continuity. Activates at the end of a working session when context is heavy but the work isn't done.
- **When not to use:** Skip if you're actually closing out a project (version bump, changelog, commit, push), that belongs in `ship`. Also skip for casual "what did we do today" recaps; just answer in chat.

### [`daily-review`](./skills/daily-review/)

**A 2-minute morning standup with yourself that surfaces today's scheduled work and gets you moving instead of re-planning the week.**

Most days start with the same failure mode: you open Claude Code, ask "what should I work on?", and end up re-litigating the whole backlog for 20 minutes before writing a line of code. This skill caps that ritual at three lists (today, overdue, in-progress) and a pick. It deliberately refuses to do strategic planning, reorder the queue, or auto-start a project session, so it stays cheap to invoke and you actually run it. The result is the difference between "I planned for 20 minutes and built for 40" and "I planned for 2 minutes and built for 58".

- **When to use:** Trigger phrases include `/daily-review`, "what should I work on", "what's on deck", "morning check", "start my day", and "today's schedule". The natural moment is the first message of a work session, before you've committed to a specific project. If you don't have a weekly review feeding it scheduled items, run that first or this skill will just point you back at it.
- **When not to use:** Skip it if you already know exactly what you're working on and just want to start, or if you're mid-project and continuing from a session-handoff prompt. It's also wrong for strategic replanning (use `weekly-review`) and pointless if your tracker has no concept of scheduled or in-progress work.

### [`weekly-review`](./skills/weekly-review/)

**A 15-25 minute structured weekly planning pass that audits stale work, promotes backlog items, and assigns target dates to your todo queue.**

Most project trackers rot in the same way: things drift into "in progress", sit there for three weeks, and quietly poison your trust in the system. This skill forces a weekly reconciliation. You audit anything stale, promote backlog items that actually deserve attention, and commit to a dated week plan instead of vibes-based prioritization. The result: when you look at your todo list on Tuesday, you believe it.

- **When to use:** Run it at the start of the week, or when you say "weekly review", "plan my week", "what should I work on this week", or `/weekly-review`. Also useful when your in-progress list has more than three items and you suspect half of them are zombies.
- **When not to use:** Skip it if you do not have a project pipeline with status fields (todo, in_progress, backlog, done) and target dates. Skip it for daily check-ins; that is what `daily-review` is for.

### [`skill-creator`](./skills/skill-creator/)

**Build and iteratively tune Claude Code skills so they trigger when you want them to and produce the output you actually need.**

Most people writing their first skill end up with a vague description that never fires, or a 400-line `SKILL.md` that triggers on everything and produces drift on each run. This skill captures the loop that actually works: pin down intent, draft, test with realistic prompts, watch what triggered (and what didn't), then tighten the description and output template. The concrete failure mode it prevents: you write a skill, walk away, then three weeks later realize Claude has been ignoring it every time you said the trigger phrase because the description was too polite. After adopting it, you stop guessing at trigger phrasing and start treating skills as something you measure and iterate on.

- **When to use:** Activates when you say "create a skill", "turn this into a skill", "make a skill that does X", "edit this skill", or "improve this skill". The natural moment is right after you notice a workflow has repeated three or more times across conversations, or when an existing skill is misfiring.
- **When not to use:** Skip it for one-off prompts you'll never reuse, or for behavior Claude already does by default without a skill. If the workflow is purely subjective (taste-driven writing, design vibes) and you don't care about consistent output, a regular system prompt is enough.

### [`research`](./skills/research/)

**Runs Perplexity-backed research that reads your project context first, queries from multiple angles, and writes a dated report to disk.**

Default Claude research tends to produce generic LLM slop: "best practices for SaaS pricing" answered with the same five bullets a junior consultant would write. This skill forces a different shape: read context first, break the topic into 2 to 4 angles, embed your actual numbers and positioning into every query, then synthesize against what you already know. The output is a dated markdown report under `research/` you can come back to, not a chat reply that scrolls away. If you've ever asked Claude to "look into X" and gotten a Wikipedia summary back, this is the fix.

- **When to use:** Triggers on "research X", "look into", "dig into", "investigate", "what do you know about", "find out about", or evaluating tools, pricing, competitors, or market landscape. Especially valuable on vague prompts, because the clarification + context-loading steps narrow scope before any API call burns tokens.
- **When not to use:** Skip for factual lookups Claude already knows (syntax, well-known APIs, library docs) and skip if you don't have a Perplexity API key and don't want one. Overkill for "what does this error mean".

---

## The rules (15)

Rules auto-load into every Claude Code conversation that uses your global config. They shape every response, every commit, every decision, without you having to remember them.

### Communication and decision-making

| Rule | What it enforces | Why it matters |
|---|---|---|
| [communication-style](./rules/communication-style.md) | No em dashes, no buzzwords, no preamble, no emojis; direct peer tone, lead with substance. | Prevents the default LLM register (throat-clearing, em-dash addiction, "just", "robust", "seamless", fake enthusiasm) from polluting every response. That register signals AI-generated content, wastes the reader's time, and erodes trust in the assistant's actual judgment. The ban list is concrete because vague style guidance gets ignored. |
| [brainstorming-question-filter](./rules/brainstorming-question-filter.md) | Every brainstorming question leads with a recommendation; pure-options menus and `AskUserQuestion` popup spam are banned. | Stops the failure mode where brainstorming devolves into Claude listing three options and the user doing the synthesis, which delegates the expensive thinking back to the user. Also stops the six-popup-per-session `AskUserQuestion` pattern that destroys conversational rhythm. Forces Claude to make the engineering call and surface it for redirect rather than punting. |
| [pushback-on-request](./rules/pushback-on-request.md) | When the user asks to be challenged, deliver the strongest counter-argument, riskiest assumption, and one concrete failure mode; no validation dressed as critique. | Stops the default LLM tendency to perform skepticism while actually agreeing, which makes "push back on this" useless. Also caps the failure mode in the other direction: manufactured contrarianism that turns into a four-message debate after the user has already decided. One sharp pushback, user decides, execute. |
| [best-of-best-modeling](./rules/best-of-best-modeling.md) | Benchmark every architecture, UX, and API choice against Linear, Stripe, Vercel, Anthropic, Notion, and the rest of the actual bar-setters. | Blocks cargo-culted, dated, or "good enough" patterns from sneaking into premium products. Without an explicit reference set, recommendations regress to whatever the model saw most often in training, which is usually a decade out of date. The rule forces the comparison to be named in the recommendation itself, so weak suggestions get caught before they ship. |

### Git, shipping, and handoffs

| Rule | What it enforces | Why it matters |
|---|---|---|
| [git-conventions](./rules/git-conventions.md) | Lowercase commits under 72 chars, push immediately after commit, feature branches and draft PRs by default, no force-push to main. | Catches the unpushed-commits trap (work sits local for hours because "deploy succeeded" creates false confidence it's pushed), the silent direct-to-main pattern that hides activity from collaborators, and the local `user.email` override that misroutes commits to a different GitHub account. Branch-by-default is one decision made once instead of one decision made wrong sometimes. |
| [concurrent-sessions](./rules/concurrent-sessions.md) | Two Claude conversations on the same repo MUST run in separate git worktrees; same checkout is forbidden. | Prevents the cross-session corruption disaster: conversation A runs `git stash` or `git checkout` and atomically rewrites the working tree out from under conversation B mid-commit, producing unmerged-paths walls, lost untracked files, and broken ship workflows. Also covers the secondary leaks (12 GB Next dev servers, accumulated VS Code windows, orphaned MCP servers) that compound over multi-day sessions. |
| [session-handoffs-required](./rules/session-handoffs-required.md) | Ending a session with work remaining requires starter prompt first (inline code block), then ship, then handoff summary; the prompt commits to one path with no embedded decisions. | Closes four documented handoff failure modes: handing off mid-task while review is still owed, embedding "PATH A vs PATH B" menus in the prompt body so the next session opens with another question, delivering the prompt as a file path the user has to chase, and omitting the parallel-session check so the new conversation steps on the old one's working tree. The handoff is the artifact that makes the next session productive instead of re-litigating decisions. |
| [starter-prompt-code-block-integrity](./rules/starter-prompt-code-block-integrity.md) | Wrap starter prompts in 4-backtick fences so inner triple-backtick code samples don't break the outer block. | Prevents the rendering bug where a starter prompt containing an inner ```` ```python ```` block causes the outer triple-backtick fence to close early, splitting the prompt into 2-3 visible code blocks with prose seams. The user then can't copy-paste it cleanly and has to ask for a redelivery. Defaulting to 4-tick fences costs nothing when there's no inner fence and eliminates the failure class entirely. |

### Code, testing, and dev workflow

| Rule | What it enforces | Why it matters |
|---|---|---|
| [coding-conventions](./rules/coding-conventions.md) | `async`/`await` over `.then()`, wrapped `try`/`catch`, no committed `.env`, tests for critical paths, structured logging. | Stops the mundane regressions that quietly degrade a codebase: silent promise rejections, swallowed exceptions, leaked secrets in git history, untraceable production errors. Each item is a known repeated mistake that costs a debugging session or a credential rotation when it lands. |
| [development-workflow](./rules/development-workflow.md) | Localhost-first, shadcn/ui default, no auto-starting backend pollers that hit production. | Stops two specific failure modes: shipping unverified code to production because "I'll just deploy and check", and orphaned Python pollers that survive VS Code restarts and quietly hammer a production database. Auto-starting a Telegram bot or scheduled job from a Claude session has caused real database load spikes; the rule names which processes are safe to auto-start and which require explicit approval. |
| [tdd-loop-discipline](./rules/tdd-loop-discipline.md) | During iteration run only the affected test file; reserve the full suite for the preflight gate. | Stops the 3-hour, ~97% CPU pegging pattern where every save triggers a 621-test vitest run (14-16 workers, ~5,500% of one core, 20-90 seconds per cycle). Sub-second targeted runs give the same correctness signal during the actual TDD loop. The full suite stays mandatory at the commit boundary, not on every save. |
| [use-gha-not-local-ci](./rules/use-gha-not-local-ci.md) | When CI is configured, push and check `gh pr checks`; full local `vitest`, `tsc`, and production builds are forbidden. | Prevents subagents from running 30+ minutes of local CPU-pegging verification (full `vitest` + `next build` + `tsc` per task) when GitHub Actions does the same job in parallel on remote hardware in 4-5 minutes. Also catches build-time regressions that scoped local typechecks miss. Local full-suite runs are the slower path almost every time once CI exists. |

### Session shape and safety

| Rule | What it enforces | Why it matters |
|---|---|---|
| [session-types](./rules/session-types.md) | Classify every opening message as project / quick fix / strategy / ad-hoc build and route to the matching workflow. | Prevents the two ends of the misclassification spectrum: treating a 30-second typo fix as a full project (brainstorm, plan, branch, PR ceremony for one comma) and treating a multi-step build as a quick fix (no plan, no scaffold, scope balloons mid-session with no record). Forces an explicit one-question check when ambiguous instead of guessing. |
| [autopilot-and-scope-checks](./rules/autopilot-and-scope-checks.md) | Define autopilot, scope, and emergency so "no further questions" never silently authorizes the wrong project. | Prevents the canonical 180-degree scope pivot bug: a pronoun or stray file path in an autopilot instruction gets read as a new assignment, and Claude burns 90+ minutes building the wrong thing. By naming scope anomalies, precondition violations, and credential mismatches as emergencies that override autopilot, it makes a one-sentence confirmation mandatory at exactly the moments silence is most expensive. |
| [no-bash-heredocs](./rules/no-bash-heredocs.md) | Never use bash heredocs through the Bash tool, even quoted ones; use the Write tool then execute the file. | Prevents a verified transport-layer bug: the Bash tool wraps heredoc bodies in a way that any apostrophe inside the body (`Pareto's`, `Murphy's`, `it's`, common in prose and Python single-quoted strings) terminates the wrapper and causes "unexpected EOF" failures dozens of lines later. The `'EOF'` delimiter does not protect you because the failure is upstream of the shell. Cost of compliance is one extra tool call; cost of violation is a recovery cascade. |

---

## Repo layout

```
claude-code-kit/
├── README.md                 ← you are here
├── INSTRUCTIONS.md           ← deep onboarding doc with the same install prompt + extra context
├── CLAUDE.md.example         ← template for your ~/.claude/CLAUDE.md
├── skills/                   ← 8 skills, each in its own folder with SKILL.md
│   ├── project-scaffolder/
│   ├── project-manager/
│   ├── ship/
│   ├── session-handoff/
│   ├── daily-review/
│   ├── weekly-review/
│   ├── skill-creator/
│   └── research/
│       └── scripts/perplexity_research.py
└── rules/                    ← 15 rules, auto-load into every conversation
    ├── communication-style.md
    ├── brainstorming-question-filter.md
    ├── pushback-on-request.md
    ├── best-of-best-modeling.md
    ├── git-conventions.md
    ├── concurrent-sessions.md
    ├── session-handoffs-required.md
    ├── starter-prompt-code-block-integrity.md
    ├── coding-conventions.md
    ├── development-workflow.md
    ├── tdd-loop-discipline.md
    ├── use-gha-not-local-ci.md
    ├── session-types.md
    ├── autopilot-and-scope-checks.md
    └── no-bash-heredocs.md
```

---

## Required dependencies

- **[Claude Code](https://claude.com/claude-code)** — the CLI / VS Code extension this is built for. The kit also works inside any agentic CLI that respects markdown skills and rules (Codex, etc.).
- **[Superpowers](https://github.com/obra/superpowers)** by Obra — **install this first.** Provides the `superpowers:brainstorming`, `superpowers:writing-plans`, `superpowers:subagent-driven-development`, and related skills that `project-manager` and `ship` invoke. Without Superpowers, the workflow chain degrades gracefully (Claude will narrate the steps) but you lose most of the value.

Optional:

- **[Perplexity API key](https://www.perplexity.ai/settings/api)** — required only for the `research` skill. Add to `~/.claude/.secrets.env` as `PERPLEXITY_API_KEY=...` or export it.
- **[GitHub CLI](https://cli.github.com/)** — used by `ship` and `project-manager` for PR creation, status checks, and merges. `gh auth login` once.

---

## Customizing

These are markdown files. No compile step, no rebuild, no plugin manifest. Edit any file at `~/.claude/skills/<skill>/SKILL.md` or `~/.claude/rules/<rule>.md`, restart Claude Code (or start a fresh conversation), and your changes take effect.

If you find yourself fighting a default, change the default. The kit is a starting point, not a contract.

The fastest way to make these your own:

1. Install as-is using the prompt above.
2. Use them for a week.
3. Notice anything that doesn't fit your workflow.
4. Edit the file directly.
5. Start a fresh Claude Code conversation. Your edits load on next session.

---

## Troubleshooting

**"The skill isn't triggering when I expect."** Read the skill's frontmatter `description` field. The triggering language is in there. If your wording is too far from what the description names, the skill won't fire. Either rephrase to match, or edit the description to include your phrasing.

**"The skill triggered but produced something weird."** Read the `SKILL.md` body. It's a markdown spec — Claude follows it. If the spec has gaps, fill them. If the spec is wrong for your case, edit it.

**"Two skills are fighting for the same trigger."** Edit one of the descriptions to be narrower. Skills cooperate via specificity; the one with the more specific match wins.

**"Restarting Claude Code didn't pick up my edit."** On macOS / Linux: fully quit (`Cmd+Q` or `pkill -f claude-code`) and reopen. On Windows: close the VS Code window AND the Claude Code process from Task Manager.

---

## License

Use it, fork it, change anything you want. No attribution required.
