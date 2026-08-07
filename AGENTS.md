# AGENTS.md

## Cursor Cloud specific instructions

delphitools is a **single, fully client-side static web app** (Next.js 16 App Router with `output: "export"`). There is no backend, database, API, auth, or secrets — every tool runs entirely in the browser. See `README.md` for the tool list and `CONTRIBUTING.md` for the "local, private, static by design" constraints.

### Package manager: use npm

- The repo contains **both** `bun.lock` and a committed `package-lock.json`. The `package-lock.json` is tracked on purpose (Cloudflare Pages can't read the text-format `bun.lock`), and `CONTRIBUTING.md` documents the `npm run …` workflow. **Bun is not installed in the cloud VM**, so use `npm`. The `Dockerfile`/`docker-compose.yml` (Bun-based) are for self-hosting only and are not needed for development.

### Commands (all from repo root)

- `npm run dev` — dev server on `http://localhost:3000` (Turbopack).
- `npm run build` — production static export into `out/` (prerenders ~117 pages). This is the CI/deploy gate.
- `npm run lint` — ESLint.

### Non-obvious gotchas

- `npm run lint` currently reports pre-existing errors (react-compiler rules, `no-this-alias` in vendored `public/pdf.worker.min.mjs`, etc.). These are in the committed code, not an environment problem — `npm run build` is the true correctness gate and passes.
- There is **no automated test framework** (no jest/vitest, no `*.test.*`/`*.spec.*` files). The `scripts/verify/*.mjs` files are one-off puppeteer-core debug rigs with a **hardcoded macOS Chrome path** (`/Applications/Google Chrome.app/...`); they are not wired to any npm script and won't run as-is in the Linux VM. Do not treat them as a test suite.
- To verify a change, run `npm run build` and exercise the affected tool manually in the browser via `npm run dev`.
