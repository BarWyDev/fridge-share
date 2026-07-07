# Repository Guidelines

FridgeShare — a community food-sharing app built around shared fridges, QR codes, and AI product recognition. Astro 6 SSR with React 19 islands, TypeScript, Tailwind 4, Supabase auth, deployed to Cloudflare Workers. Product spec: @context/foundation/prd.md. Deeper agent guidance: @CLAUDE.md.

## Hard rules

- Read `SUPABASE_URL`/`SUPABASE_KEY` only via `astro:env/server`, never `import.meta.env` — they are server-only secrets declared in @astro.config.mjs. `createClient()` from `@/lib/supabase` returns `null` when they are unset; handle that case.
- Never write to `context/archive/` — archived changes are immutable.
- Every new Supabase table gets RLS enabled with per-operation, per-role policies. Migrations live in `supabase/migrations/`, named `YYYYMMDDHHmmss_short_description.sql`.
- Merge Tailwind classes with `cn()` from `@/lib/utils`, never by concatenating strings.
- No Next.js directives (`"use client"` etc.) — React renders as Astro islands.
- Known gap: CI (@.github/workflows/ci.yml) triggers on `master`, but the repo branch is `main`, so CI does not run yet. Fix the workflow before relying on the gate.

## Commands

- `npm run dev` — dev server on the Cloudflare workerd runtime
- `npm run build` — production SSR build (needs Supabase env vars)
- `npm run lint` / `npm run lint:fix` — ESLint with `strictTypeChecked`; `no-console` warns, `react-compiler` errors
- `npm run format` — Prettier (astro + tailwindcss plugins)
- `npx supabase start` — local Supabase stack (requires Docker)

No test runner is configured yet.

## Structure & conventions

- `src/pages/` Astro pages; `src/pages/api/` endpoints with uppercase `GET`/`POST` exports; `src/middleware.ts` is the auth gate — add protected paths to `PROTECTED_ROUTES`.
- `src/components/` — Astro components for static content, React only where interactive; shadcn/ui ("new-york") in `src/components/ui/`; hooks in `src/components/hooks/`.
- `src/lib/` — services and helpers; shared entity/DTO types in `src/types.ts`.
- Import alias `@/*` → `./src/*`.
- `context/` is the planning workspace (PRD, tech stack, change logs) — source of truth for product decisions, not code.

## Environment & commits

Copy `.env.example` to `.env` and `.dev.vars`; Supabase setup steps in @README.md. Node 22.14.0 (`.nvmrc`). Pre-commit runs husky + lint-staged (`eslint --fix`, `prettier --write`). Only the scaffold commit exists so far — no commit convention established yet.
