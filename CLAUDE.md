# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

Node `^22.18.0 || >=24.12.0` is required.

```sh
npm install
npm run dev       # Vite dev server, bound to 127.0.0.1 (see vite.config.js)
npm run build     # production build to dist/
npm run preview   # serve the built dist/
```

There is no test runner, linter or formatter set up yet. If you add one (e.g. Vitest, ESLint), add its commands here.

## Architecture

- Vue 3 single-page app built with Vite. It uses plain JavaScript (no TypeScript) and `<script setup>` single-file components.
- Entry point: `index.html` → `src/main.js` (imports `src/assets/main.css` globally and mounts `App.vue` on `#app`).
- `@` resolves to `src/` (set in `vite.config.js`, mirrored in `jsconfig.json` for the editor).
- `vite-plugin-vue-devtools` is enabled in dev.
- There is no router, state store or HTTP client yet. Everything under `src/components/` and the `src/assets/` CSS is the default `create-vue` starter content (HelloWorld, TheWelcome, icons). Replace it as you build Stocky's own features.
