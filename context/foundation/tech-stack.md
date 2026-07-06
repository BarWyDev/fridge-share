---
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
---

## Why this stack

A solo builder shipping FridgeShare — a mobile-browser web-app with e-mail/Google login, photo-based product entries, AI recognition with shelf-life estimates, and automatic expiry/archiving — in 3 after-hours weeks needs a starter that covers auth, database, and file storage out of the box. 10x Astro Starter (Astro 6 + React 19 + TypeScript + Tailwind 4 + Supabase + Cloudflare Pages) is the recommended default for a JS web-app and clears all four agent-friendly gates: typed, convention-based, popular in training data, and well-documented. Supabase handles accounts, member-scoped visibility, and product photos; Astro API routes give the AI recognition step a server surface; the auth, AI, and background-jobs feature flags are set (expiry marking/archiving per FR-010 will need a scheduled job — the edge runtime prefers a queue or cron trigger for that, flagged for bootstrapper). Payments and realtime are out of scope per the PRD. Deployment lands on cloudflare-pages, the starter default; CI runs on GitHub Actions with auto-deploy on merge to main. Bootstrapper confidence is first-class: scaffolding should be mostly smooth with occasional manual steps.
