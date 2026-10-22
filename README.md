# Claude Code Team Setup (from the video "How My Engineering Team Actually Runs Claude Code")

Four files that turn Claude Code from a chatbot into a teammate with guardrails:

| File | What it does |
|---|---|
| `CLAUDE.md` | Rules Claude reads at the start of every session |
| `.claude/settings.json` | What Claude may run without asking, and the hook that enforces review |
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

Change the rules in `CLAUDE.md` to match your team. Keep it short.
