# Claude Code Team Setup

[![Watch the walkthrough video on YouTube](https://img.youtube.com/vi/FKkVsDQsNbg/maxresdefault.jpg)](https://www.youtube.com/watch?v=FKkVsDQsNbg)

Video walkthrough: https://www.youtube.com/watch?v=FKkVsDQsNbg

Five files that give Claude Code rules, a deny list, and a review step it can't skip by forgetting:

| File | What it does |
|---|---|
| `CLAUDE.md` | Rules Claude reads at the start of every session |
| `.claude/settings.json` | What Claude may run without asking, what it may never run, and the hook that enforces review |
| `.claude/skills/pre-commit-branch-check/SKILL.md` | A Skill Claude loads on its own before any commit or push |
| `.claude/skills/pre-commit-review/SKILL.md` | A Skill that reviews the staged diff and leaves a marker |
| `.claude/hooks/require-review.sh` | A Hook that blocks `git commit` until the review marker exists |

## Try it

```bash
git clone https://github.com/guardraildev/claude-code-team-setup
cd claude-code-team-setup
chmod +x .claude/hooks/require-review.sh
claude
```

Then ask Claude to change something and commit. Watch it check the branch, run the review, and only then commit.

Writing the marker touches `.claude/`, a protected folder, so Claude Code asks before it writes. Approve it; the marker is a 12-character hash of the staged diff, nothing else. That approval is also your chance to read the review.

## What the Hook does not stop

It is a guardrail against forgetting, not a security boundary.

- It matches the text `git commit`. `git -C . commit`, `git -c key=value commit` and aliases are not recognised. A command that only mentions the text, such as `grep -r "git commit" docs`, is blocked.
- The marker proves that a file with the right hash exists, not that a review ran.
- The hash covers the staged diff when the command starts. `git commit -a` or `git add x && git commit` can include changes the review never saw.
- If the script cannot start, Claude Code shows a `hook error` notice and the commit goes through. On Claude Code v2.1.295 or later, add `"onFailure": "block"` to the hook entry to block instead.
- The deny rules match command text too. They stop the spellings listed, not every way to force-push or delete.

## Changed after the video (2026-11-02)

- The hook path in `settings.json` is quoted. Before, a project path with a space stopped the Hook from starting, and commits went through.
- The Hook no longer needs python3. Before, a machine without python3 let every commit through.
- `Bash(git branch*)` and `Bash(rg *)` are no longer allowed without asking. They also matched `git branch -D` and `rg --pre`, which runs a program. Read-only commands such as `ls`, `cat`, `grep` and read-only `git` run without a prompt anyway.
- More spellings of force-push and `rm -rf` are denied.

Change the rules in `CLAUDE.md` to match your team. Keep it short.
