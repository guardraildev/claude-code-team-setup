---
name: pre-commit-review
description: Run before any git commit. Reviews the staged diff for secrets, scope creep, and obvious bugs, reports findings, and writes the review marker the commit hook requires.
---

1. Run `git diff --staged`. If it is empty, say so and stop.
2. Check the diff for:
   - credentials, tokens, emails, connection strings
   - changes outside what the user asked for
   - obvious bugs (unused variables, wrong conditions, missing error paths the surrounding code handles)
3. Report findings as a short list. If anything must be fixed, stop and wait for the user.
4. If the diff is clean, write the marker:
   ```bash
   git diff --staged | shasum | cut -c1-12 > .claude/.reviewed
   ```
5. Tell the user the review passed and the commit is unblocked.
