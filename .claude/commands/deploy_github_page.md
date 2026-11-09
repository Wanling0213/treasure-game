Deploy this project to GitHub Pages. Check every prerequisite first and guide the user through anything that is missing, then deploy. Follow these steps in order, and do not skip a check:

1. **Git installed.** Run `git --version`. If it fails, tell the user to install Git (`winget install --id Git.Git -e` on Windows, or https://git-scm.com/downloads), and stop until they confirm it is done.

2. **GitHub CLI installed.** Run `gh --version`. If it fails, tell the user to install it (`winget install --id GitHub.cli -e` on Windows, or https://cli.github.com), reopen the terminal, and tell you when it is done. Stop until they do.

3. **GitHub login.** Run `gh auth status`. If not logged in, tell the user to type `! gh auth login` (choose GitHub.com → HTTPS → Login with a web browser) and wait for them to finish. Run `gh auth status` again to confirm. Then get the username with `gh api user --jq .login`.

4. **Git identity.** If `git config user.name` or `git config user.email` is empty, ask the user for the values and set them with `git config --global user.name "..."` and `git config --global user.email "..."`.

5. **Local repository.** If the folder is not a git repo (`git rev-parse --is-inside-work-tree` fails), run `git init -b main`. Make sure `.gitignore` contains `node_modules/`, `build/`, `.vercel/`, and `.env*`, adding any that are missing. Commit all files ("Initial commit").

6. **GitHub repository.** If `git remote get-url origin` fails:
   - Ask the user for the repo name (default `treasure-game`) and whether it should be public or private. Explain that on a free GitHub account, GitHub Pages only works for public repos.
   - If `gh repo view <username>/<name>` shows the repo already exists, ask whether to use it (`git remote add origin https://github.com/<username>/<name>.git`) or pick another name.
   - Otherwise create it with the chosen visibility: `gh repo create <name> --public --source=. --remote=origin` (or `--private`).

7. **Push the source.** Commit any pending changes, then run `git push -u origin main`. Confirm the files are on GitHub with `gh api repos/<username>/<repo>/contents --jq '.[].name'`.

8. **Deploy tool and scripts.** If `gh-pages` is not in devDependencies, run `npm install --save-dev gh-pages`. Make sure `package.json` has these scripts, with `<repo>` replaced by the real repo name:
   - `"build:gh": "vite build --base=/<repo>/"`
   - `"deploy:gh": "npm run build:gh && gh-pages -d build"`

   Do not change the regular `build` script or `vite.config.ts`, because the Vercel deploy needs the default base `/`. Commit and push these changes.

9. **Deploy.** Run `npm run deploy:gh`. If the build fails, fix the errors and run it again. On the first deploy, `gh-pages` starts from `main` and leaves its dotfiles (such as `.claude/` and `.gitignore`) on the `gh-pages` branch. Check with `gh api "repos/<username>/<repo>/git/trees/gh-pages?recursive=1" --jq '.tree[].path'`, and if any appear, run `npx gh-pages -d build --dotfiles --remove "{.claude/**,.gitignore}"` once to clean them out.

10. **Enable Pages.** If `gh api repos/<username>/<repo>/pages` returns 404, run `gh api -X POST repos/<username>/<repo>/pages -f "source[branch]=gh-pages" -f "source[path]=/"`.

11. **Report:**
    - Repo: `https://github.com/<username>/<repo>`
    - Git remote: `https://github.com/<username>/<repo>.git`
    - Live site: `https://<username>.github.io/<repo>/` (the first publish can take 1–2 minutes; check with `gh api repos/<username>/<repo>/pages --jq .status`)
