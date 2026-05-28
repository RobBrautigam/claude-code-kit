# No Bash Heredocs Through The Bash Tool

**Never use bash heredocs through the Bash tool.** No exceptions, no content-type carve-outs, no platform carve-outs. This applies to `<<`, `<<-`, `<<EOF`, `<<'EOF'`, `<<"EOF"`, `cat > file <<…`, `cat << … >> file`, and every variant.

The delimiter being quoted (`<< 'EOF'`) does NOT protect you. The failure is upstream of the shell at the Bash tool's command-wrapping layer, not at the shell parser.

## Why (root cause)

The Bash tool wraps the command string before passing it to the underlying shell process. When the heredoc body contains a single apostrophe — extremely common in natural-language prose, project descriptions, and Python code with single-quoted strings — the apostrophe terminates the outer wrapper. The shell then parses the remainder as regular shell code and dies on an unmatched quote dozens of lines later.

Verified failure modes:
- `python << 'PY' … PY` with prose containing `Pareto's`, `Murphy's`, `it's` → `unexpected EOF while looking for matching `'``.
- Markdown bodies with code fences (`` ``` ``) and `$` template variables.
- Python scripts with triple-quoted strings containing `"`.
- Reproduced on Windows git-bash AND Unix shells. This is a Claude Code transport-layer issue, not a shell issue.

The `'EOF'` delimiter cannot save you. By the time the shell parses the delimiter, the outer wrapper has already terminated.

## Decision tree (apply at the moment of choice)

When about to run code via the Bash tool, ask exactly these questions in order:

1. **Single shell command, no prose arguments?** (`git status`, `npx supabase db reset`, `railway up`, `pytest tests/`, `gh pr list`, etc.)
   → Bash tool, direct. **Safe.**

2. **`python -c "…"` or `bash -c "…"` on a single short line (< ~100 chars) with NO apostrophes, backticks, `$`, or unescaped double quotes in the body?**
   → Bash tool, direct. **Safe.**

3. **Anything else** — any multi-line script, any heredoc, any content with prose, any content with apostrophes / backticks / `$` / quotes?
   → **Write the script to a file via the `Write` tool, then execute it via Bash with `python path/to/file.py` or `bash path/to/file.sh`.** Always. No exceptions.

Set the bar LOW. A 3-line Python snippet with a single apostrophe is unsafe via heredoc. Defaulting to Write+execute costs 1 extra tool call in the happy case but eliminates the entire failure class.

## Two distinct cases — use the right tool for each

| Goal | Right pattern | Wrong pattern |
|---|---|---|
| Create a file with content (markdown, JSON, config, README, progress log) | `Write` tool directly to the destination path | A Python script that calls `path.write_text(...)` to do the same write; or `cat > file <<EOF` |
| Run multi-line logic (DB ops + filesystem + verification + branching) | `Write` tool to create a `.py` script (canonical path: `tmp/<purpose>.py`), then `python tmp/<purpose>.py` | `python <<'PY' … PY` heredoc; or `cat > /tmp/x.py <<'PY' … PY` then `python /tmp/x.py` |

Don't write a Python script just to write a markdown file. Write the markdown file directly.

## Banned anti-pattern: "heredoc-then-fallback"

The cycle that wastes time:
1. Try `cat > file <<'EOF' … EOF`
2. Heredoc fails on an apostrophe
3. Recover by switching to `Write` tool

**Skip step 1.** The recovery is the right answer; trying the heredoc first is the bug. If the content has prose or could have prose, go straight to `Write`+execute.

## Canonical patterns (copy these)

### Multi-line Python with prose

```
Write tool:
  file_path: <repo>/tmp/scaffold_thing.py
  content: |
    title = "Murphy's Law"   # apostrophes are fine inside Python source
    ...

Bash tool:
  command: python <repo>/tmp/scaffold_thing.py
```

### Multi-line markdown (e.g. progress log entry, README)

```
Write tool:
  file_path: <repo>/projects/<slug>/README.md
  content: |
    # Project Title
    ...
```

(No Python intermediate. No heredoc.)

### Short, safe one-liner

```
Bash tool:
  command: python -c "from mymodule import foo; print(foo())"
```

(No apostrophes, no backticks, no `$`, no unescaped quotes, single line, well under 100 chars.)

## Where to put temp scripts

- Canonical location: `tmp/` inside the repo (add to `.gitignore` if not already).
- Use a descriptive filename: `tmp/scaffold_seo.py`, `tmp/audit_pipeline.py`, not `tmp/x.py`.
- Do NOT write to `/tmp/` (Unix tmp). It's git-bash-specific on Windows and not consistent across environments.
- Do NOT promote ad-hoc scripts into permanent paths like `scripts/`. Those are for committed, reused tooling — not single-use scaffolding.
