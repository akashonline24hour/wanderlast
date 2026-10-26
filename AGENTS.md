<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

# wanderlast

Single-package Next.js App Router site (travel landing page in progress). The only route is `src/app/page.tsx`; app code is under `src/`. Only the create-next-app commit exists — nearly all UI work is uncommitted, so do not discard or `git checkout` the current tree.

## Commands

- `npm run dev` — Turbopack dev server on `http://localhost:3000`.
- `npm run lint` — ESLint over the whole repo.
- `npm run build` — production build (Turbopack) and TypeScript check.
- `npx tsc --noEmit` — typecheck only; there is no `typecheck` script.
- No tests, formatter, or CI are configured. Use npm; `package-lock.json` is the only lockfile.
- Node 22+ is required (the `cn` package declares `engines.node >= 22`).
- Lint is currently red: `react-hooks/set-state-in-effect` in `src/components/ui/carousel.tsx` and `src/hooks/use-mobile.ts`. Treat these as pre-existing, not regressions.

## Next.js 16 specifics

- Next.js 16.4.3 / React 19.3, App Router. Prefer the installed docs in `node_modules/next/dist/docs/` over training data.
- `next.config.ts` enables `cacheComponents` and `partialPrefetching`:
  - Runtime APIs (`cookies()`, `headers()`, `params`, `searchParams`) must be read behind `<Suspense>` or inside a `"use cache"` scope, or the route blocks prerendering.
  - Do not add `dynamic`, `revalidate`, or `fetchCache` route segment exports — they error under Cache Components. Use `"use cache"` with `cacheLife`/`cacheTag`.
  - `runtime` segment config is unsupported (Node.js only).
  - `<Link>` prefetches the shared app shell, not the full page.
- `PageProps<'/route'>` and `LayoutProps<'/route'>` are global generated helpers — no import needed (see `src/app/layout.tsx`).
- `next dev` rewrites the managed `nextjs-agent-*` blocks in this file; leave them intact.

## Styling and UI

- Tailwind CSS v4 is CSS-first: theme tokens live in `src/app/globals.css`; there is no `tailwind.config`. The `@tailwindcss/turbopack` CSS rule in `next.config.ts` is required.
- `src/components/ui/*` are shadcn components for the `base-nova` style (`components.json`). Add more with `npx shadcn add <name>` and reuse existing primitives.
- Primitives come from `@base-ui/react` (plus `@shadcn/react` for `questionnaire`), not Radix. Keep the generated `data-slot`/`render` patterns and `"use client"` markers.
- Import `cn` as `import { cn } from "cn"`; `@/lib/utils` only re-exports it.
- Font wiring is inconsistent: `layout.tsx` loads Josefin Sans into both `--font-geist-sans` and `--font-geist-mono`, and `globals.css` declares a self-referential `--font-sans: var(--font-sans)`. Change layout and CSS together when touching typography.
- Shiki highlighting is registered manually in `src/lib/highlighter.tsx` (`languages` + `aliases`); new languages must be added there. `src/hooks/use-highlighted-code.ts` handles streaming/partial lines.

## Conventions

- Registry-generated UI files: no semicolons, double quotes, named exports, `data-slot` attributes. Match that style when editing them; `src/app/*` currently uses semicolons.
- Path alias `@/*` → `src/*`; TypeScript is strict with `moduleResolution: "bundler"`.
