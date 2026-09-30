# Base44 Development Environment

## Project Type

This is a **Microsoft MakeCode (PXT) project** for the micro:bit — a "dash bird" game
(Flappy Bird clone). The source is `main.ts` (TypeScript) and `main.blocks` (Blockly XML).
It is not a traditional web app; it runs in the MakeCode simulator.

## Running the Project

The app runs via `docker compose -f docker-compose.base44.yml up -d`.

The container uses the `makecode` npm CLI (`mkc`), the modern replacement for the
legacy `pxt` CLI:

1. `npm install -g makecode` — installs the CLI
2. `sed` patches `simserver.js` to bind `0.0.0.0` instead of `127.0.0.1` (needed for Docker)
3. `mkc install` — downloads pxt dependencies (core, radio, microphone) to `pxt_modules/`
4. `mkc serve --port 3000` — starts the local simulator web server with file watching

The simulator page is served at `http://localhost:3000/`. It loads `binary.js`
(the compiled project) and runs it in a MakeCode simulator iframe.

## Live Reload

`mkc serve` watches `.ts`, `.json`, and other source files. When a file changes, it
recompiles `built/binary.js`. The browser must be refreshed to pick up the new build
(the auto-reload feature only fires on `localhost`/`127.0.0.1`, not the preview hostname).

## Key Paths

- `pxt.json` — project manifest (target, dependencies, files)
- `main.ts` — game source code
- `main.blocks` — Blockly blocks representation
- `pxt_modules/` — downloaded dependencies (gitignored, created by `mkc install`)
- `built/binary.js` — compiled output (gitignored, created by `mkc serve`)
- `/root/.pxt` — MakeCode CLI cache (Docker volume `pxt_cache`)

## No External Credentials Required

The MakeCode CLI downloads from the public CDN (`cdn.makecode.com`,
`makecode.microbit.org`). No API keys or secrets are needed.

## Verifying the App

```sh
docker compose -f docker-compose.base44.yml ps          # container should be "healthy"
curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/  # should return 200
curl -s http://localhost:3000/binary.js | head -1       # should show compiled JS
```
