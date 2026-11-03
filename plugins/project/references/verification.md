# Verification

Verification proves a change works by driving the real app as a user would and capturing evidence; passing tests and typechecks alone don't count. Claude Code's bundled `/run` and `/verify` infer a launch from the project type, which breaks once the app needs a database, an env file, a graphical session, or a multi-step build. Recorded skills close that gap. Before writing one, fetch https://code.claude.com/docs/en/skills.md#run-and-verify-your-app for where the bundled skills look for them.

## Layout

Project skills in `.claude/skills/`, one set per app (per independently runnable package in a monorepo):

- **`run-<app>`** starts and stops the app; every verify skill builds on it. Setup writes it.
- **`verify-<feature>`** proves one feature works on an app `run-<app>` launched. Write one when a feature is done and you've just proved it by hand, so the recipe comes from a run that worked. Name the feature in its description so the agent reaches for it after touching that code.

## Status

Compare the recorded skills with the build config. The run skill is one of: current; stale (names commands, paths, or ports that are gone); absent, bundled inference enough (a standard CLI or library); absent, worth adding. List the verify skills and flag stale ones. A feature without a verify skill is not a finding.

## Writing the run skill

1. **Study the repo**: what a user touches, how it starts locally, what can drive it, and whether two instances can run side by side. Prefer the repo's own harness (e2e specs, scripts, a debug endpoint) over a generic one.
2. **Get it running from a clean state.** If it won't start, fix or report that before writing anything; a recipe recorded against a broken checkout teaches the wrong steps.
3. **Write it** from the commands that worked:
   - **Launch**: the command and the *ready* signal. Ready means usable (migrations run, data seeded), not just a port answering. For a short-lived CLI, launch means build.
   - **Check**: a fast, read-only test that the instance is the one this run started, on the current build, with valid auth. Run it first whenever something looks off.
   - **Drive**: the harness and how to connect it.
   - **Cleanup**: stop only what this run started, by PID or handle, so the user's own instances survive. Keep the evidence.
   - **Adding a verify skill**: [Writing a verify skill](#writing-a-verify-skill), adapted to this app's harness and evidence. Verify skills get written after setup, so this is how the agent finds the guidance.
4. **Prove it cold**: launch, check, clean up, cleaning up after failed attempts too, until a full pass succeeds. If you proved it somewhere other than the real target (a container standing in for the user's machine), list what stays unverified.
5. **Allow** the commands it runs with permission rules.

## Writing a verify skill

Record how to reach and drive the feature using this repo's real commands, routes, and stable selectors, plus what evidence proves it and where it's kept. Proof goes through the real user path, captures the action and the resulting state, and checks side effects (files written, rows inserted, messages sent). Judge a dry run or test mode by what you observe it skip, not by its name.

Run it once from launch to cleanup and confirm the evidence survived cleanup.

Edit a recorded skill only when it steered a run wrong, so the committed file changes only when it must.
