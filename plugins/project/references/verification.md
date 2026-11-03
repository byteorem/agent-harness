# Verification

Verification is how Claude proves that a change works: it drives the real app the way a user would and captures evidence. Passing tests and typechecks aren't enough on their own. Claude Code's bundled `/run` and `/verify` guess how to launch the app from the project type, and the guess breaks once the app needs a database, an env file, a graphical session, or a multi-step build. Fetch https://code.claude.com/docs/en/skills.md#run-and-verify-your-app before writing these skills, because it says where the bundled skills look for recorded ones.

## Setup

Record verification as project skills in `.claude/skills/`, one per app (in a monorepo, one per package that runs on its own):

- **`run-<app>`** gets the app running and stops it again. Every verify skill builds on it. Setup writes this one.
- **`verify-<feature>`** proves that one feature works, starting from an app the run skill launched. Write one when work on a feature is complete and you've just proved it by hand, so the recipe comes from a run that worked. Its description names the feature, so the agent reaches for it after changing that code.

## Status

When auditing, compare the recorded skills with the build config, but don't run them. Give the run skill one of these statuses: current, stale (names commands, paths, or ports that are gone), absent but the bundled inference is enough (a standard CLI or a library), or absent and worth adding. List the verify skills, and flag any that are stale. A feature without a verify skill is not a finding.

## Writing the run skill

1. **Study the repo.** Work out from the code what a user touches (web UI, CLI, TUI, API, desktop, library), how the app starts locally, what can drive it, and whether two instances can run side by side. Prefer a harness the repo already has, such as e2e specs, scripts, or a debug endpoint, over a generic one. Ask the user only for what the repo can't tell you, such as a seed account.
2. **Get it running from a clean state.** If it won't start, fix the problem or report it before writing anything. A recipe recorded against a broken checkout teaches the wrong steps.
3. **Write it** from the commands that actually worked:
   - **Launch**: the command, plus the signal that the app is *ready*. Ready means usable, with migrations run and data seeded, which is more than a process answering on a port. For a short-lived CLI, launching means building it.
   - **Check**: a fast, read-only test that the running instance is the one to drive: the process this run started, the current build, and valid auth. Run it first whenever something looks off.
   - **Drive**: which harness to use and how to connect it.
   - **Cleanup**: stop exactly what this run started, by PID or handle, so the user's own instances survive. Keep any evidence.
   - **Adding a verify skill**: when to write one and how, adapted from [Writing a verify skill](#writing-a-verify-skill) to this app's harness and evidence. Verify skills get written after setup is over, so this section is how the agent finds the guidance.
4. **Prove it** by following it cold: launch, check, clean up. Clean up after every failed attempt too, and rerun until a full pass succeeds. If you proved it somewhere that differs from the real target, such as a container standing in for the user's machine, list what stays unverified.
5. **Allow it.** Add permission allow rules for the commands it runs.

## Writing a verify skill

Cover how to reach the feature and how to drive it, with real commands, routes, and selectors from this repo. Prefer stable handles, such as labels, test IDs, and prompt text, over coordinates. Then cover the evidence that proves it works and where it's kept. A proof goes through the real user path, records both the action and the resulting state, and checks side effects such as files written, rows inserted, or messages sent. Judge a dry run or test mode by what you observe it skipping, not by its name.

Before handing it over, run it once from launch to cleanup, and confirm that the evidence survived the cleanup.

Edit a recorded skill only when it steered a run wrong, so the committed file changes only when it needs to.
