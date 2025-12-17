# Repository Guidelines

## Project Structure & Module Organization

- Next.js App Router lives in `src/app` (routes, layouts, API handlers). Reusable UI stays in `src/components`; hooks in `src/hooks`; utilities in `src/lib`; styling in `src/styles`. `public/` holds static assets (logos, screenshots, generated `VERSION.txt`).
- Automation scripts in `scripts/` (e.g., `generate-manifest.js`). Copy `.env.example` to `.env.local`; Node version pinned in `.nvmrc`.
- Path aliases: `@/` -> `src`, `~/` -> `public`. Group new modules by feature; add barrel files only when they reduce import noise.

## Build, Test, and Development Commands

- Install: `pnpm install` (pnpm 10, Node 20.10).
- Local dev: `pnpm dev` (runs manifest generation) on port 3000.
- Build/serve: `pnpm build` then `pnpm start` for production output.
- Quality: `pnpm lint`, `pnpm lint:fix`, `pnpm format:check` / `pnpm format`, `pnpm typecheck`.
- Tests: `pnpm test` or `pnpm test:watch`; append `--coverage` when needed.
- Docker: `pnpm docker:build` then `pnpm docker:run -p 3000:3000`.

## Coding Style & Naming Conventions

- Prettier: 2-space indent, single quotes (incl. JSX), semicolons, arrowParens always (`.prettierrc.js`).
- ESLint: Next + TypeScript + Prettier with import sorting and unused-import checks; keep imports ordered per `eslint.config.js`.
- Prefer functional React components, named exports, typed props. Use PascalCase for components, kebab-case for routes/files under `app` when they map to URLs.

## Testing Guidelines

- Jest + Testing Library, jsdom env (`jest.config.js`). Place `*.test.ts[x]` near the code they cover.
- Mock static assets via existing mappers; favor integration-style assertions around UI behavior and data parsing.
- Run `pnpm test` (or with `--coverage`) before opening a PR.

## Commit & Pull Request Guidelines

- Conventional commits enforced by commitlint; allowed types: feat, fix, docs, chore, style, refactor, ci, test, perf, revert, vercel (`commitlint.config.js`). Add a short scope when helpful.
- Before PR: ensure `pnpm lint && pnpm typecheck && pnpm test` pass. Provide a concise description, link related issues, and include UI screenshots/GIFs for visible changes.
- Keep PRs focused; call out any env/config changes explicitly.

## Security & Configuration Tips

- Keep secrets out of git. Use `.env.local` for credentials (Redis/Kvrocks/Upstash, admin auth, proxy settings).
- When changing domain/PWA settings, verify `next.config.js` and regenerate the manifest via `pnpm gen:manifest`.
