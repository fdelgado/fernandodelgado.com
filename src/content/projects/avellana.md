---
title: Avellana
slug: avellana
description: Multilingual hospitality training platform with AI voice simulations, multi-tenant content management, and an executive progress dashboard — built as a monorepo with Next.js, FastAPI, Supabase, and Terraform IaC.
date_started: 2026-03-25
date_completed: 2026-04-07
active_hours: 18.0
sessions: 10
total_prompts: 21
tech_stack:
  - Next.js 15
  - TypeScript
  - FastAPI
  - Python 3.11
  - SQLModel
  - PostgreSQL (Supabase)
  - pgmq
  - Deepgram Nova-3
  - Google Gemini 2.0 Flash
  - Google Gemini 2.5 Pro
  - Cartesia Sonic TTS
  - Google Cloud TTS Neural2
  - Terraform
  - HCP Terraform
  - Railway
  - Vercel
  - Doppler
  - GitHub Actions
  - Vitest
  - Playwright
  - Pytest
  - Ruff
  - TanStack Query
  - uv
platform: Web
lines_of_code: 5752
files: 123
commits: 38
status: in-progress
hidden: true
cover_image: /images/projects/avellana/cover.png
screenshots:
  - path: /images/projects/avellana/dashboard.png
    alt: Executive progress dashboard with changelog, kanban, and plain-English tabs
  - path: /images/projects/avellana/voice-pipeline.png
    alt: Voice simulation pipeline architecture diagram
tags:
  - ai
  - voice
  - hospitality
  - multi-tenant
  - terraform
  - monorepo
---

## Summary

Avellana is a multilingual hospitality training platform that uses AI voice simulations to help hotel and service teams practice guest interactions. Built as a monorepo across ~18 active hours and 10 sessions, it includes a Next.js 15 frontend, FastAPI backend, Supabase-powered multi-tenant data model, and a voice simulation pipeline chaining Deepgram ASR, Gemini LLM, and Cartesia TTS. Infrastructure is fully codified with Terraform + HCP Terraform across dev/staging/prod environments.

## Features

- Multi-tenant org/property data model with role-based access (director, manager, trainer, trainee)
- AI voice simulation pipeline: Deepgram Nova-3 ASR → Gemini 2.0 Flash (turns) → Cartesia Sonic TTS
- Async evaluation with Gemini 2.5 Pro scoring simulation sessions
- Executive progress dashboard with Changelog, ADRs, Kanban, and Plain English tabs
- Magic link onboarding + password login + email/SMS password reset
- Full IaC with Terraform, HCP Terraform remote state, and per-env Doppler secrets
- CI/CD pipeline with GitHub Actions: lint, unit tests, E2E, and deploy gates
- Voice vendor cost analysis with lite/medium/high usage modeling across ASR, TTS, and LLM providers
- pgmq-based async job queue (zero extra infra, runs inside Supabase)
- Knowledge-base chatbot grounded in org-specific content (planned)
- Supabase keep-alive cron to prevent free-tier project pausing
- Feature-flagged realtime voice path (Deepgram Voice Agent API) alongside stable turn-based default

## How It Was Built

The build started with a monorepo scaffold and a deep stack audit — every major technology choice (ORM, queue, i18n, deployment, TTS provider) was evaluated against pilot constraints: <500 users, cost-conscious, 2-week delivery window. Early sessions focused on infrastructure: Terraform modules for Railway and Vercel, migration to HCP Terraform remote state, Doppler secret management across three environments, and a CI pipeline that took ~10 push/fix cycles to get green in the monorepo layout.

The second phase tackled the data model and auth system. The multi-tenant schema was designed with "ultrathink" pressure-testing sessions to future-proof extensibility — orgs own properties, content is org-scoped but property-overridable, and operational data (sessions, evaluations) is always property-scoped. Auth uses Supabase magic links for onboarding with password login after setup.

Voice vendor analysis was a recurring thread throughout the build — evolving from a simple cost comparison to a full strategic document with realtime-first ordering, per-user cost modeling at multiple usage tiers, and hot-standby fallback strategies. The project also served as a live case study for a Product Management course, with prompts and decisions captured in `docs/course/`.

## Prompts

Key requests that drove the build, in order:

1. **Stack audit** — "Analyze what we have planned and verify whether the big decisions on tech stack are appropriate"
2. **Blocker resolution** — "Chose i18n approach (next-intl + manual JSON), Terraform stance, pgmq for queue, in-app-only notifications"
3. **Doppler secrets setup** — "Walked through setting Railway API token, Supabase passwords, and TF_VAR_ prefixes across all envs"
4. **Terraform cleanup** — "Refactor now if it will simplify things, including deleting variables we don't need or are duplicate"
5. **HCP Terraform migration** — "Created HCP org 'avellana' and migrated local state to remote workspaces"
6. **Vercel import** — "All environments use the same Vercel project. Is that how it should be?"
7. **Stack switches** — "Switch pip to uv, Ruff only, Supabase CLI migrations, pgmq for async queue"
8. **Railway CLI over Terraform** — "Replace with Railway CLI in GitHub Actions. Nobody will be messing with the dashboard"
9. **Dashboard requirements** — "Audience: co-founders. Tabs: Changelog/ADRs/Kanban. Auto-refresh every minute"
10. **UX redesign** — "Redesign the executive dashboard paying close attention to all the UX principles"
11. **Authority aesthetic** — "Make it look more authoritative, grab design inspiration from the advisory board digest"
12. **Voice vendor scouting** — "Google launched Gemini 3.1 Flash Live and Cohere announced Transcribe. Add them to the analysis"
13. **PM course meta-task** — "Use my experience building Avellana as a case study for a Product Management in the AI Era course"
14. **Schema ultrathink** — "Ultrathink to challenge your own suggestion and make sure this schema is the most optimal for Avellana"
15. **Auth design** — "Magic link for setup, password login after. Allow resets via email, WhatsApp, or SMS"
16. **CI debugging marathon** — "Ultrathink to fix all remaining issues before we stop" (across ~10 failing CI pushes)
17. **Realtime-first analysis** — "Rewrite the analysis assuming realtime is a requirement and turn-based is a cost fallback"
18. **Cost modeling** — "Show cost/user/month at lite (2h), medium (6h), and high (30h) usage tiers"
19. **Supabase keep-alive** — "Got the inactivity pause email. What can we do to generate basic activity?"
20. **Kanban board** — "Render kanban as a horizontal board with columns"
21. **Keep-alive fix** — "Got this from GitHub" (shared failing workflow screenshot, triggering the HTTP status fix)

## Raw Prompts

Substantive user messages from the conversation, preserving original wording:

> "I would like for you to analyze what we have planned and executed so far in this project, and help me verify whether the big decisions on tech stack (frontend, backend, deployment, tooling, testing, etc) are appropriate."

> "Blocker 1: not decided yet. This is a biggie. Blocker 2: yes, terraform. Blocker 3: next-intl + manual JSON files for the pilot. Blocker 4: in-app only. yes to adrs"

> "The latter steps seem to work, but I'm open to a refactor now if it will simplify things, including deleting variables we don't need or are duplicate"

> "Based on what you recommend: 1. Yes, let's switch pip + requirements to uv 2. Ruff only is fine 3. Supabase CLI migrations + SQLModel 4. pgmq for async job queue"

> "1. For railway: let's Replace with Railway CLI in GitHub Actions. Nobody will be messing with the railway dashboard (only read-mode). 2. Let's go with TanStack Query."

> "1. Me and the other cofounders of avellana 2. Openly accessible from my repo, but not hosted anywhere for now 3. Yes, auto-refresh every minute"

> "I just added a new ux-principles file to the docs/ folder. Please redesign the executive dashboard paying close attention to all the principles. Also, give me a single toggle at the top from light to dark theme"

> "The style still looks a little too computer sciency. The audience of founders is not very technical. Can you make it look more authoritative?"

> "Very recently, google launched gemini 3.1 flash live and cohere announced transcribe. Can you look for their announcements, and help me add them to the voice cost analysis? i think it's worth creating a skill or two..."

> "Here's a meta-task that's important to me: I am working on my first course. It's about Product Management in the era of AI. The capstone project will be for students to build a solid prototype using AI. I want to use my own experience building avellana as a case study"

> "ultrathink to challenge your own suggestion for this, and make sure that this schema design is the most optimal for avellana"

> "magic link should also be an easy way for the admin to invite back users who forgot their password. it just needs to generate a new magic link every time. please allow password resets not just via email but also whatsapp or sms"

> "please re-write the analysis and the report assuming that realtime is a requirement, and that turn-based is a cost fallback to consider"

> "can the executive summary show me cost/user/month at lite (2 hours per month), medium (6 hours per month) and high (30 hours per month)?"

> "got the email below. what can we do to generate basic activity? should we write a test record?"

> "got this from github:" (shared screenshot of failing keep-alive workflow)

## Technical Challenges

### Terraform variable naming collision with Doppler

**Problem:** After setting up Doppler to inject secrets into Terraform, `terraform plan` kept failing with "undeclared variable" and "undeclared input variable" errors across multiple runs.

**Root cause:** Doppler injected `TF_VAR_` prefixed secrets (e.g., `TF_VAR_DATABASE_PASSWORD_SECRET`) but the Terraform root module declared the variable as `database_password` — a name mismatch. Additionally, stale variables from earlier iterations lingered in Doppler configs, causing Terraform to reject them as undeclared.

**Solution:** Audited and deleted stale `TF_VAR_` secrets from Doppler, aligned variable names between `.tfvars` files and root `main.tf`, and ran `terraform init -upgrade` to clear provider caches between iterations.

### HCP Terraform remote state migration

**Problem:** After migrating from local state to HCP Terraform remote backends, all previously-imported Railway service resources vanished from state, and subsequent `terraform plan` attempts showed resources as needing creation.

**Root cause:** The migration to HCP Terraform created fresh remote workspaces with empty state. Railway resources needed to be removed from state locally _before_ the backend switch, not after.

**Solution:** Ran `terraform state rm` on all Railway resources in each workspace, removed the Railway provider entirely, and rerouted deployments to Railway CLI in GitHub Actions — eliminating the fragile community Terraform provider dependency.

### GitHub Actions CI: 10-commit debugging marathon

**Problem:** The initial CI pipeline failed on every push for different reasons — Docker build errors, Playwright config issues, ESLint not finding `tsconfig.json`, wrong npm cache path, DB integration tests hitting an unseeded Supabase.

**Root cause:** The monorepo layout (`apps/frontend/`, `apps/api/`) broke CI assumptions about root-level config files. Playwright needed a dummy env to run, and the npm cache hash path didn't account for the nested `package-lock.json`.

**Solution:** Fixed working-directory references in each job, excluded Playwright files from `tsc`/ESLint, added a dummy `.env.test` for E2E, gated Docker build/deploy jobs on Dockerfile existence. CI went green on the 11th push.

**Debugging approach:** Each failure was diagnosed from the GitHub Actions log annotations, fixed locally, pushed, and re-checked — a tight feedback loop across ~10 iterations.

### Vercel prerender crash on auth pages

**Problem:** After configuring Cloudflare DNS and adding Supabase env vars to Vercel, the build failed with `TypeError: Cannot read properties of null (reading 'auth')` during static prerendering of `/auth/setup`.

**Root cause:** Next.js was statically prerendering the auth setup page at build time, calling `supabase.auth` outside a request context. The Supabase client returned `null` in the static pass since environment variables aren't available during static generation.

**Solution:** Added a runtime guard to skip Supabase initialization during static prerender, making auth pages fully dynamic and bypassing static export for those routes.

### Supabase keep-alive workflow: silent HTTP failures

**Problem:** A cron workflow to prevent Supabase free-tier pausing failed silently — the logs showed "ping failed" but no HTTP status code, making it impossible to diagnose whether the issue was bad keys, wrong URL, or a network problem.

**Root cause:** The original `curl -sf` flags suppressed all output on failure. When the anon key didn't match the project, the 401 response was swallowed entirely.

**Solution:** Added `-w "%{http_code}"` to log the status explicitly, then changed the success criteria: any HTTP response (including 401) proves Supabase is alive and prevents pausing. Only a `000` (no response) is treated as failure.

## Architecture

Key technical decisions:

- **Monorepo with independent deploys** — Frontend (Vercel), API (Railway), Workers (Railway) share types via `packages/domain` but deploy independently, keeping blast radius small
- **pgmq over Redis/SQS** — Postgres-native message queue runs inside Supabase with zero extra infra, perfect for pilot scale
- **Turn-based voice as stable default** — Deepgram ASR → Gemini Flash → Cartesia TTS is the production path; realtime (Deepgram Voice Agent API) is feature-flagged for when latency requirements tighten
- **Terraform + HCP Terraform** — Remote state across dev/staging/prod workspaces, with Doppler injecting secrets as `TF_VAR_` environment variables
- **Multi-tenant with property override** — Content is org-scoped by default, property-overridable; operational data is always property-scoped. This avoids per-property content duplication while allowing local customization
- **Cost-first vendor selection** — Every voice provider choice was modeled at lite/medium/high usage tiers with explicit $/user/month breakdowns before committing
