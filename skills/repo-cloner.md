---
name: repo-cloner
description: Clone a Wise repo, install dependencies, start the dev server, and open browser tabs. Use when the user wants to clone a repo, set up a project, or says "clone this repo", provides a GitHub SSH URL, or mentions setting up a local dev environment for a Wise repo.
---

First, ask the user: **Which repo should I clone?** Ask for the GitHub SSH URL (e.g. `git@github.com:transferwise/balance-flows-web.git`).

If the user provided the repo as an argument already, use that: $ARGUMENTS

Once you have the SSH URL, execute these steps:

0. **Determine the working directory.** Use the current working directory (`pwd`) as the clone destination. Store the full path as `$WORK_DIR` for all subsequent steps.

1. **Clone the repo** into `$WORK_DIR`:
   ```
   git clone <SSH_URL>
   ```
   Derive the folder name from the SSH URL (e.g. `balance-flows-web.git` -> `balance-flows-web`). The full repo path is `$WORK_DIR/<repo-folder>` — store this as `$REPO_PATH`.

2. **cd into the cloned repo** for all subsequent commands.

3. **Install dependencies**:
   ```
   pnpm install
   ```

4. **Start the dev server in a new Terminal tab** using `osascript`.

   **⚠️ MANDATORY: The `cd` MUST be inside the `do script` string.** New Terminal tabs ALWAYS open in `~`, NOT in the current working directory. If you omit `cd` from the `do script` string, the command WILL fail with `ERR_PNPM_NO_IMPORTER_MANIFEST_FOUND`. This is the #1 most common mistake with this skill.

   **Before running osascript, determine the correct dev command:**
   - First, check the **root** `package.json` for a `dev:prod` script. If it exists, use it from `$REPO_PATH`.
   - If the root does NOT have `dev:prod`, search `apps/*/package.json` for it. If found in a sub-package (e.g. `apps/request-payer-page`), use `pnpm --filter <package-name> dev:prod` from `$REPO_PATH`.
   - If no `dev:prod` exists anywhere, fall back to `pnpm dev`.

   **The osascript command MUST look exactly like this (with `cd` INSIDE the quoted string):**
   ```
   osascript -e 'tell application "Terminal" to do script "cd $REPO_PATH && $DEV_COMMAND"'
   ```

   **WRONG (will fail):**
   ```
   osascript -e 'tell application "Terminal" to do script "$DEV_COMMAND"'
   osascript -e 'tell application "Terminal" to do script "pnpm dev:prod"'
   ```

5. **Detect the localhost port** — Wait ~10 seconds, then find the port. Use `lsof -iTCP -sTCP:LISTEN -P -n` filtered to node processes in the repo directory to find the port.

6. **Open browser tabs**:
   ```
   open http://localhost:<detected-port>
   open https://wise.com
   ```
