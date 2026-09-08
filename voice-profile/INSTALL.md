# Install the voice profile

Three steps. Ten minutes including the test. Nothing to compile.

## Step 1: copy the drop-in for your agent

Pick the row that matches your tool. Copy the file. That is the whole install.

| Agent | What to copy | Where it goes |
|---|---|---|
| Claude Code | `drop-ins/rules/communication-style.md` | `~/.claude/rules/communication-style.md` (create the folder if it does not exist) |
| Claude Code, belt and braces | the block in `drop-ins/CLAUDE.md-snippet.md` | appended to `~/.claude/CLAUDE.md` |
| Codex | the block in `drop-ins/AGENTS.md-snippet.md` | appended to `~/.codex/AGENTS.md` (global) or `AGENTS.md` at the repo root |
| Cursor, Gemini CLI, Copilot CLI, Amp, OpenCode, any AGENTS.md reader | the block in `drop-ins/AGENTS.md-snippet.md` | `AGENTS.md` at the repo root, or the tool's global rules file |
| Grok, a custom GPT, Ollama, LM Studio, an API call, any chat app with custom instructions | the block in `drop-ins/system-prompt.md` | the system prompt, or the custom-instructions box |

If you already use the claude-code-starter-kit, `rules/communication-style.md` in the kit is the short form of this profile. Replacing it with the drop-in is intended.

Or let the agent do it. Paste this into a conversation opened inside the cloned repo:

```
Install the voice profile from voice-profile/ for the agent I am using right now.
For Claude Code: copy voice-profile/drop-ins/rules/communication-style.md to
~/.claude/rules/ and append the block from voice-profile/drop-ins/CLAUDE.md-snippet.md
to ~/.claude/CLAUDE.md (show me the diff first if that file exists).
For Codex or any AGENTS.md reader: append the block from
voice-profile/drop-ins/AGENTS.md-snippet.md to my global AGENTS.md.
Then list what landed.
```

## Step 2: make it yours

Open the file you just installed and change two things:

1. **Your English.** The default is American. Change the line if you write British, Australian, or anything else. The rule is that it never drifts, not that it is American.
2. **Your name and your carve-outs.** The system-prompt block has a `<your name>` placeholder. If you draft messages for other people through the agent, keep the carve-out that says those follow their own voice.

Everything else works as shipped. Edit any rule that fights you; this is a starting point, not a contract.

## Step 3: start fresh and test

Rules load when a conversation starts, so open a new one. Then paste the test:

```
Look at the current repo and tell me the three riskiest things about shipping it this week.
```

You should get: the top risk in bold on line one, a numbered list of three, plain English for any jargon, one next action under two minutes, and at most one question. The longer version of this check, with a full before-and-after, is in `EXAMPLE-before-and-after.md`.

**If the reply still opens with "Great question!"**

- Claude Code: confirm the file is at `~/.claude/rules/communication-style.md` and that the conversation is new. Run `/memory` (or check your tool's equivalent) to see which files loaded.
- Codex: confirm the block is in `~/.codex/AGENTS.md` or the repo-root `AGENTS.md`, not in a subfolder.
- System-prompt agents: confirm the block is the system prompt and not a user message; some apps silently truncate long system prompts, so check the tail survived.
- Still drifting mid-session? Say "that reply was hard to read" and the agent will fix the shape. If it keeps happening, the drop-in is not loading; it is not a matter of asking more nicely.

## Keeping it sharp

When the agent gets a reply wrong, tell it and ask it to fold the correction into the rule file. That is how this profile was built: one correction at a time, in the file, the same day. Six months of that and the agent talks the way you think.
