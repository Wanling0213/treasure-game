Deploy this project to Vercel by following these steps:

1. Run `npm run build` to produce a production build in the `build/` directory. If the build fails, fix any errors before continuing.

2. Check whether the Vercel CLI is installed by running `vercel --version`. If it is not installed, install it globally with `npm install -g vercel`.

3. Check whether a `vercel.json` file exists at the project root. If it does not, create one with the following content so Vercel serves the Vite output correctly:
   ```json
   {
     "buildCommand": "npm run build",
     "outputDirectory": "build",
     "framework": "vite"
   }
   ```

4. Run `vercel` to deploy. When prompted:
   - Confirm the project directory is correct.
   - Choose to link to an existing project or create a new one named `treasure-game`.
   - Accept the detected settings (Vite framework, `build/` output).

5. After the deployment succeeds, report the production URL returned by Vercel.
