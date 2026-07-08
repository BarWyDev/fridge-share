# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project: FridgeShare

Community food-sharing app built around shared fridges: register a fridge, get a printable QR code, and anyone who scans it can add products (manually, or via AI photo recognition with shelf-life estimates) and see the fridge's live contents. Canonical spec: `context/foundation/prd.md`; raw product notes (Polish): `fridge-share.md`. The codebase is currently the untouched `10x-astro-starter` scaffold — auth works end-to-end, but no FridgeShare features exist yet.

## Commands

- `npm run dev` — dev server (Cloudflare workerd runtime)
- `npm run build` — production build (SSR via `@astrojs/cloudflare`)
- `npm run preview` — preview the production build
- `npm run lint` / `npm run lint:fix` — ESLint with type-checked rules
- `npm run format` — Prettier (astro + tailwindcss plugins)
- Deploy: `npm run build && npx wrangler deploy`

No test runner is configured yet. Pre-commit (husky + lint-staged) runs `eslint --fix` on `*.{ts,tsx,astro}` and `prettier --write` on `*.{json,css,md}`.

## Architecture

Astro 6 SSR app (`output: "server"` — every page is server-rendered, so no `prerender` flags are needed) with React 19 islands, Tailwind 4, shadcn/ui, and Supabase auth, deployed to Cloudflare Workers (`wrangler.jsonc`).

### Auth flow (the one multi-file subsystem)

- `src/lib/supabase.ts` builds the Supabase SSR client with cookie-based sessions. It returns `null` when `SUPABASE_URL`/`SUPABASE_KEY` are unset — callers must handle that. Both vars are server-only secrets declared in `astro.config.mjs` `env.schema` and imported from `astro:env/server`, never `import.meta.env`.
- `src/middleware.ts` runs on every request, puts the current user on `context.locals.user`, and redirects unauthenticated requests matching `PROTECTED_ROUTES` to `/auth/signin`. New protected paths go in that array.
- API endpoints: `src/pages/api/auth/{signin,signup,signout}.ts` (form-POST + redirect, no JSON). Pages: `src/pages/auth/*.astro`; `src/pages/dashboard.astro` is the protected-page example.

### Conventions

- Path alias `@/*` → `./src/*`.
- Astro components for static content; React only where interactivity is needed. No Next.js directives (`"use client"`). Extract hooks to `src/components/hooks/`.
- Merge Tailwind classes with `cn()` from `@/lib/utils`; never concatenate class strings.
- shadcn/ui ("new-york" style) lives in `src/components/ui/`; add components with `npx shadcn@latest add [name]`.
- API routes use uppercase `GET`/`POST` exports.
- Services/business logic go in `src/lib/` (or `src/lib/services/`); shared entity/DTO types in `src/types.ts`.
- Supabase migrations go in `supabase/migrations/`, named `YYYYMMDDHHmmss_short_description.sql`. Always enable RLS on new tables with granular per-operation, per-role policies.

### Environment

- Node v22.14.0 (`.nvmrc`). Secrets live in `.env` (Node/Supabase CLI) and `.dev.vars` (Cloudflare local dev), both copied from `.env.example`.
- Local Supabase: `npx supabase start` (requires Docker); Studio at `http://localhost:54323`.
- CI (`.github/workflows/ci.yml`) runs lint + build on push/PR to master and needs `SUPABASE_URL`/`SUPABASE_KEY` repository secrets.

<!-- BEGIN @przeprogramowani/10x-cli -->

## 10xDevs AI Toolkit - Module 2, Lesson 1

Move from sprint-zero setup to project orchestration with the **roadmap chain**:

```
(Module 1 foundation docs) -> /10x-roadmap -> backlog-ready roadmap items
```

`/10x-roadmap` is the lesson focus. `/10x-new` is intentionally introduced in Module 2, Lesson 2, when a selected roadmap item becomes an implementation change folder.

### Task Router - Where to start

| Skill | Use it when |
| --- | --- |
| **Roadmap (lesson focus)** | |
| `/10x-roadmap` | You have `context/foundation/prd.md` and a scaffolded project baseline, and you need a vertical-first MVP roadmap. The skill reads the PRD, inspects the code baseline, uses available foundation docs such as `tech-stack.md`, `infrastructure.md`, and `deploy-plan.md`, then writes `context/foundation/roadmap.md`. Use it BEFORE creating per-change folders or implementation plans. |
| **Re-run upstream if needed** | |
| `/10x-shape` / `/10x-prd` / `/10x-tech-stack-selector` / `/10x-bootstrapper` / `/10x-agents-md` / `/10x-infra-research` | Bundled from Module 1 so foundation contracts can be fixed before roadmap sequencing. If roadmap generation exposes a PRD gap, repair the PRD before pretending the backlog is ready. |

### How the chain hands off

- `/10x-roadmap` bridges product and implementation. It does not choose frameworks, design schemas, or write a per-change implementation plan.
- The output is `context/foundation/roadmap.md`: ordered milestones, vertical slices, bounded foundations, dependencies, unknowns, risk, and backlog handoff fields.
- Roadmap items should receive stable human-readable identifiers in backlog tools. The actual `context/changes/<change-id>/` folder is created in Lesson 2 with `/10x-new`.

### Roadmap boundaries

- Default to vertical slices: user-visible outcomes that cross UI, data, business logic, and integrations.
- Horizontal work is allowed only as a bounded enabler that names the downstream vertical milestone it unlocks.
- Avoid orphan horizontal work such as "build the whole database", "build all API endpoints", or "design the whole UI" before the first user-visible flow.
- Roadmap is not a calendar estimate. Do not invent dates, story points, or sprint velocity unless the user explicitly asks for a separate planning artifact.

### Foundation paths used by this lesson

- `context/foundation/prd.md` - input
- `context/foundation/tech-stack.md` - optional input
- `context/foundation/infrastructure.md` - optional input
- `context/deployment/deploy-plan.md` - optional input
- `context/foundation/roadmap.md` - output
- `context/foundation/lessons.md` - recurring rules and pitfalls
- `docs/reference/contract-surfaces.md` - load-bearing names registry

Skills must not write to `context/archive/`. Archived changes are immutable; if a resolved target path starts with `context/archive/`, abort with: "This change is archived. Open a new change with `/10x-new` instead."

<!-- END @przeprogramowani/10x-cli -->
