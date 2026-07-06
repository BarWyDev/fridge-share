---
bootstrapped_at: 2026-07-06T19:25:22Z
starter_id: 10x-astro-starter
starter_name: 10x Astro Starter (Astro + Supabase + Cloudflare)
project_name: fridge-share
language_family: js
package_manager: npm
cwd_strategy: git-clone
bootstrapper_confidence: first-class
phase_3_status: ok
audit_command: npm audit --json
---

## Hand-off

```yaml
starter_id: 10x-astro-starter
package_manager: npm
project_name: fridge-share
hints:
  language_family: js
  team_size: solo
  deployment_target: cloudflare-pages
  ci_provider: github-actions
  ci_default_flow: auto-deploy-on-merge
  bootstrapper_confidence: first-class
  path_taken: standard
  quality_override: false
  self_check_answers: null
  has_auth: true
  has_payments: false
  has_realtime: false
  has_ai: true
  has_background_jobs: true
```

### Why this stack (verbatim from hand-off)

A solo builder shipping FridgeShare — a mobile-browser web-app with e-mail/Google login, photo-based product entries, AI recognition with shelf-life estimates, and automatic expiry/archiving — in 3 after-hours weeks needs a starter that covers auth, database, and file storage out of the box. 10x Astro Starter (Astro 6 + React 19 + TypeScript + Tailwind 4 + Supabase + Cloudflare Pages) is the recommended default for a JS web-app and clears all four agent-friendly gates: typed, convention-based, popular in training data, and well-documented. Supabase handles accounts, member-scoped visibility, and product photos; Astro API routes give the AI recognition step a server surface; the auth, AI, and background-jobs feature flags are set (expiry marking/archiving per FR-010 will need a scheduled job — the edge runtime prefers a queue or cron trigger for that, flagged for bootstrapper). Payments and realtime are out of scope per the PRD. Deployment lands on cloudflare-pages, the starter default; CI runs on GitHub Actions with auto-deploy on merge to main. Bootstrapper confidence is first-class: scaffolding should be mostly smooth with occasional manual steps.

## Pre-scaffold verification

| Signal      | Value                                                           | Severity | Notes                                                                 |
| ----------- | --------------------------------------------------------------- | -------- | --------------------------------------------------------------------- |
| npm package | not run                                                         | —        | cmd_template starts with `git clone`; no npm create-* CLI to resolve   |
| GitHub repo | przeprogramowani/10x-astro-starter last pushed 2026-05-17T10:33:39Z | fresh    | from card.docs_url; ~7 weeks before bootstrap date (within 3 months)   |

## Scaffold log

**Resolved invocation**: `git clone https://github.com/przeprogramowani/10x-astro-starter .bootstrap-scaffold && cd .bootstrap-scaffold && npm install`
**Strategy**: git-clone
**Exit code**: 0
**Files moved**: 19 top-level entries (18 moved silently, 1 sidelined as `.scaffold` sibling)
**Conflicts (.scaffold siblings)**: CLAUDE.md.scaffold
**.gitignore handling**: moved silently (absent in cwd)
**.bootstrap-scaffold cleanup**: deleted

Details:

- Cloned `.git/` deleted before move-up — upstream starter history does not leak into this project.
- `npm install` (chained in the template) completed inside the scaffold dir: 774 packages added; `node_modules/` and `package-lock.json` moved up with the rest.
- Scaffold contained no `context/` paths; cwd `context/` preserved untouched by policy.
- Entries moved silently: `.env.example`, `.github/`, `.gitignore`, `.husky/`, `.nvmrc`, `.prettierrc.json`, `.vscode/`, `README.md`, `astro.config.mjs`, `components.json`, `eslint.config.js`, `node_modules/`, `package-lock.json`, `package.json`, `public/`, `src/`, `supabase/`, `tsconfig.json`, `wrangler.jsonc`
- Conflict: scaffold's `CLAUDE.md` vs existing `CLAUDE.md` (project instructions) → existing wins, scaffold copy at `CLAUDE.md.scaffold`.
- npm install warnings (informational): deprecated `@babel/plugin-proposal-private-methods@7.18.6`, deprecated `node-domexception@1.0.0`.

## Post-scaffold audit

**Tool**: `npm audit --json` (exit code 1 — non-zero is expected when findings exist; informational only)
**Summary**: 0 CRITICAL, 6 HIGH, 9 MODERATE, 2 LOW (17 total across 895 dependencies: 449 prod, 316 dev, 131 optional)
**Direct vs transitive**: 0/1/2/0 direct of total 0/6/9/2 — direct findings: `astro` (HIGH), `supabase` (MODERATE), `wrangler` (MODERATE). All 17 findings have a fix available per npm.

#### CRITICAL findings

None.

#### HIGH findings

- **astro** (direct) — Reflected XSS via unescaped slot name; XSS via unescaped attribute names in spread props; Host header SSRF in prerendered error page fetch. Fix available.
- **devalue** (transitive) — DoS via sparse array deserialization. Fix available.
- **miniflare** (transitive, via undici/ws) — inherits undici and ws advisories. Fix available.
- **undici** (transitive) — TLS certificate validation bypass via dropped requestTls in SOCKS5 ProxyAgent; HTTP header injection via Set-Cookie percent-decoding; WebSocket DoS via fragment count bypass. Fix available.
- **vite** (transitive) — launch-editor NTLMv2 hash disclosure via UNC path handling on Windows; `server.fs.deny` bypass on Windows alternate paths. Fix available.
- **ws** (transitive) — uninitialized memory disclosure; memory-exhaustion DoS from tiny fragments and data chunks. Fix available.

#### MODERATE findings

- **@astrojs/language-server** (transitive, via volar-service-yaml). Fix available.
- **@cloudflare/vite-plugin** (transitive, via miniflare/wrangler/ws). Fix available.
- **js-yaml** (transitive) — quadratic-complexity DoS in merge key handling via repeated aliases. Fix available.
- **supabase** (direct, via tar) — node-tar PAX size override parser differential (file smuggling). Fix available.
- **tar** (transitive) — same node-tar advisory as above. Fix available.
- **volar-service-yaml** (transitive, via yaml-language-server). Fix available.
- **wrangler** (direct, via esbuild/miniflare). Fix available.
- **yaml** (transitive) — stack overflow via deeply nested YAML collections. Fix available.
- **yaml-language-server** (transitive, via yaml). Fix available.

#### LOW / INFO findings

- **@babel/core** (transitive) — arbitrary file read via sourceMappingURL comment. Fix available.
- **esbuild** (transitive) — arbitrary file read when running the development server on Windows. Fix available.

Raw `npm audit --json` output archived at scratchpad during the run; per-severity counts and direct/transitive split parsed from `metadata.vulnerabilities` and per-advisory `isDirect` flags.

## Hints recorded but not acted on

| Hint                    | Value             |
| ----------------------- | ----------------- |
| bootstrapper_confidence | first-class       |
| quality_override        | false             |
| path_taken              | standard          |
| self_check_answers      | null              |
| team_size               | solo              |
| deployment_target       | cloudflare-pages  |
| ci_provider             | github-actions    |
| ci_default_flow         | auto-deploy-on-merge |
| has_auth                | true              |
| has_payments            | false             |
| has_realtime            | false             |
| has_ai                  | true              |
| has_background_jobs     | true              |

Note from the hand-off body carried forward for future action: expiry marking/archiving (FR-010) will need a scheduled job — the edge runtime prefers a queue or cron trigger. v1 records this; it does not scaffold it.

## Next steps

Next: a future skill will set up agent context (CLAUDE.md, AGENTS.md). For now, your project is scaffolded and verified — happy hacking.

Useful manual steps in the meantime:
- `git init` (if you have not already) to start your own repo history.
- Review any `.scaffold` siblings the conflict policy created and decide which version of each file to keep (here: `CLAUDE.md.scaffold`).
- Address audit findings per your project's risk tolerance — the full breakdown is in this log.
