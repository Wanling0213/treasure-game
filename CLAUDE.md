# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-page treasure chest game built with React 18 and Vite 6, exported from Figma Make. The player opens chests to find the one with treasure (+$150) while skeletons cost $50. The game ends when the treasure is found or all chests are open.

`README.md` is the script for a Claude Code course, not project documentation. Its prompts (sound effects, cursor change, SQLite auth, deployment commands) are exercises to run when asked, and they don't describe existing features or standing rules.

## Commands

```bash
npm install
npm run dev     # Vite dev server on port 3000; opens a browser automatically
npm run build   # Production build to build/ (not dist/)
```

The project has no test runner, linter, or `tsconfig.json`, so there is no way to run a test or a lint. TypeScript is not installed either: SWC strips types without checking them, so `npm run build` succeeding does not mean the types are correct.

## Architecture

All game logic and UI live in the `App` component in `src/App.tsx`. State is three `useState` hooks (`boxes`, `score`, `gameEnded`), with no router, store, or backend. `openBox` calls `setScore` and `setGameEnded` from inside the `setBoxes` updater, and it reads `score` from the render closure. `src/main.tsx` doesn't use `StrictMode`, which would run that updater twice and double-apply the score.

`src/components/ui/` is a generated shadcn/ui kit, and `App.tsx` uses only `Button` from it. `src/components/figma/` holds a Figma Make helper that nothing imports yet.

Some assets are present but not wired in yet: `chest_open.mp3` and `chest_open_with_evil_laugh.mp3` are imported in `App.tsx` but never played, and `src/assets/key.png` is unused.

## Gotchas

**Tailwind classes are precompiled.** `src/index.css` is the generated output of Tailwind v4.1.3, and `tailwindcss` is not a dependency, so nothing regenerates it. Only classes already in that file have any effect. For example, `bg-amber-600` works, but `bg-amber-500`, `bg-blue-500`, and `cursor-none` silently do nothing. Before using a class that isn't already in the code, search for it in `src/index.css`. If it's missing, use an inline `style` or add a rule by hand.

**`src/styles/globals.css` is not loaded.** Nothing imports it, so editing its theme tokens has no effect. The active tokens are in `src/index.css`.

**UI kit imports include versions.** Files in `src/components/ui/` import packages with a version in the name (such as `@radix-ui/react-slot@1.1.2`), which resolve through the aliases in `vite.config.ts`. If you upgrade one of these packages or add a versioned import, update the matching alias. The same config also defines `@` as an alias for `src/`.
