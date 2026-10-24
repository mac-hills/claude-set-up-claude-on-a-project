# Notes

## What's in CLAUDE.md, and what I left out

CLAUDE.md covers things Claude can't easily work out from the code alone. It lists the exact commands, including that tests use the built-in `node --test` runner and not Jest. It states the house rules: don't touch app code unless asked, new code must be TypeScript with no `any`, every endpoint gets a Supertest case, run lint and test before a commit, and never commit or push without asking. It also notes a few architecture traps: the `require.main === module` guard that lets tests import `app`, all data access going through `db/store.js`, and the in-memory store keeping state between tests.

I left out anything Claude can read from the repo itself, such as a file-by-file tour, the full endpoint list, dependency versions and generic advice like "write clean code". Those details go stale, cost context on every session and add nothing Claude couldn't find in a few seconds. I also left secrets and personal preferences out, because the file is committed and shared with the team. Personal settings belong in `.claude/settings.local.json`, which is gitignored.

## Permission rules, and why the deny rule matters

In `.claude/settings.json`:

- **allow:** `npm test`, `npm run lint` and `node --test`. These are safe, read-only checks that Claude runs all the time, so they shouldn't trigger a prompt.
- **ask:** `git commit` and `git push`. These enforce the "always ask first" rule from CLAUDE.md even if Claude forgets it.
- **deny:** `Read(./.env)`, `git push --force` and `git push -f`.

Without the `.env` deny rule, Claude could read real credentials, for example while debugging a config issue. Those secrets would then go to the model as part of the conversation and could end up in logs, a summary, a test fixture or a commit. Without the force-push denies, an approved push could still rewrite `main` and destroy teammates' history. A deny rule is a hard stop that holds even if the instructions are ignored or a prompt is approved by mistake.

I also fixed `.gitignore`. Its lines had leading spaces, and git treats those spaces as part of the pattern, so `.env` and `settings.local.json` weren't actually being ignored.
