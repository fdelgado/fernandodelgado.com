---
title: Avellana
slug: avellana
description: Multilingual hospitality training platform with realtime AI voice simulations powered by Gemini Live native audio, multi-tenant content management, and five role-specific frontend experiences (trainee, supervisor, director, admin, super-admin). Built as a monorepo with Next.js, FastAPI, Supabase, and Terraform IaC.
date_started: 2026-03-25
date_completed: 2026-04-14
active_hours: 36
sessions: 17
total_prompts: 48
tech_stack:
  - Next.js 15
  - TypeScript
  - FastAPI
  - Python 3.11
  - SQLModel
  - PostgreSQL (Supabase)
  - pgmq
  - Google Gemini 2.5 Flash Native Audio (Live)
  - Anthropic Claude Sonnet 4.6
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
lines_of_code: 31808
files: 299
commits: 112
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

Avellana is a multilingual hospitality training platform that uses realtime AI voice simulations to help hotel and service teams practice guest interactions. Built as a monorepo across ~30 active hours and 16 sessions, it includes a Next.js 15 frontend, FastAPI backend, Supabase-powered multi-tenant data model, and a voice simulation pipeline built on Google Gemini 2.5 Flash Native Audio (Live) — a single model that handles ASR, LLM, and TTS over one WebSocket. Async post-session evaluation runs on Anthropic Claude Sonnet 4.6 with prompt caching. Infrastructure is fully codified with Terraform + HCP Terraform across dev/staging/prod environments.

## Features

- **Five role-specific frontends** shipped over 5 phases — trainee dashboard (progress, KB with grounded Ask Gemini chat, simulations, how-to quizzes, history, feedback), supervisor team overview + trainee detail + session deep-dive with audio replay and flag resolve, director content hub with real CRUD for simulations/how-tos/characters/rubrics, admin with user invite + CSV bulk import + SOPs + policies + org registration
- Shared CRUD scaffold (`ContentCrudPage<T>` + responsive `ContentTable` + `ContentSheet` + 6 form field variants + `useContentMutations`) built once and instantiated 8× across director and admin resources — 70% code reduction vs hand-rolling
- Multi-tenant org/property data model with role-based access (6 roles: trainee, supervisor, director, user admin, billing admin, super admin)
- Realtime voice simulation over one WebSocket: Gemini 2.5 Flash Native Audio handles ASR, reasoning, and TTS in a single model
- Browser mic capture at 16 kHz PCM, streamed frame-by-frame to FastAPI, forwarded to Gemini Live; AI audio streamed back and played through a 24 kHz AudioContext queue
- Gender-aware voice selection (Kore / Fenrir / Puck) derived from `character.voice_config`
- Character speaks first: system prompt + kickoff `send_client_content` trigger an in-scene greeting as soon as the WebSocket opens
- Async post-session evaluation with Anthropic Claude Sonnet 4.6 (prompt-cached rubric/policy/scenario block) in a pgmq worker
- Executive progress dashboard with Changelog, ADRs, Kanban, and Plain English tabs
- Magic link onboarding + password login + email/SMS password reset
- Full IaC with Terraform, HCP Terraform remote state, and per-env Doppler secrets
- CI/CD pipeline with GitHub Actions: lint, unit tests, E2E, and deploy gates
- Voice vendor cost analysis with lite/medium/high usage modeling across ASR, TTS, and LLM providers
- pgmq-based async job queue (zero extra infra, runs inside Supabase)
- Knowledge-base chatbot grounded in org-specific content (planned)
- Supabase keep-alive cron to prevent free-tier project pausing
- Single-vendor voice path: no runtime feature flags — vendor swaps happen via a `LiveClient` Protocol fork in `voice_session.py`, per ADR-005

## How It Was Built

The build started with a monorepo scaffold and a deep stack audit — every major technology choice (ORM, queue, i18n, deployment, TTS provider) was evaluated against pilot constraints: <500 users, cost-conscious, 2-week delivery window. Early sessions focused on infrastructure: Terraform modules for Railway and Vercel, migration to HCP Terraform remote state, Doppler secret management across three environments, and a CI pipeline that took ~10 push/fix cycles to get green in the monorepo layout.

The second phase tackled the data model and auth system. The multi-tenant schema was designed with "ultrathink" pressure-testing sessions to future-proof extensibility — orgs own properties, content is org-scoped but property-overridable, and operational data (sessions, evaluations) is always property-scoped. Auth uses Supabase magic links for onboarding with password login after setup.

Voice vendor analysis was a recurring thread throughout the build — evolving from a simple cost comparison to a full strategic document with realtime-first ordering, per-user cost modeling at multiple usage tiers, and hot-standby fallback strategies. The project also served as a live case study for a Product Management course, with prompts and decisions captured in `docs/course/`.

The fourth phase was the frontend buildout — five role-specific experiences delivered in sequence, each within a single working session. The trainee experience (Phase 2: dashboard, knowledge base with grounded Ask Gemini chat, simulations list, how-to quizzes with hot-zone maps, history, progress) landed first with ~14 new shared primitives in `src/components/trainee/` and hand-rolled SVG charts (CircularScore, CompetencyRadar, TalkTimeDonut — no Recharts). Phase 3 added the supervisor experience: team overview with trainee roster, drill-down to individual trainee detail with a skill radar and activity timeline, and a session review deep-dive with a procedural-waveform audio replay player that plays real recorded audio via a hidden `<audio>` element but renders a deterministic 100-bar visualization seeded off the session id (real amplitude analysis via `OfflineAudioContext` was deferred as decorative-UI tech debt). Phase 4 shipped the director content management hub with full CRUD for simulations, how-tos, characters, and rubrics — all wired to real FastAPI endpoints with no mock branches. The shared `ContentCrudPage<T>` scaffold was the highlight: one generic render-prop page component handles list + edit Sheet + delete ConfirmDialog + TanStack mutation plumbing for every resource, so adding a new CRUD surface is ~150 lines instead of ~500. Phase 5 extended the scaffold to admin (users with CSV bulk import, SOPs, compensation policies) and shipped a single-item form for org registration. Each phase passed a `/ux-guidelines` audit before merge and ran through a manual smoke test on the live Vercel deploy. Mock registry went from 33 mocked resources to 21 mocked / 12 real by the end of Phase 5.

The third phase was the voice rebuild. The original design was a turn-based chain (Deepgram Nova-3 → Gemini Flash → Cartesia), but once Gemini 2.5 Flash Native Audio became available we collapsed the entire ASR/LLM/TTS stack into a single WebSocket-based model. This meant building a browser mic capture pipeline (16 kHz PCM via `ScriptProcessorNode`), a FastAPI WebSocket route that bridges the browser ↔ Gemini Live bidirectional stream, a `LiveClient` Protocol in `voice_session.py` so vendor code never leaks into route handlers, and a 24 kHz `AudioContext` playback queue on the client. Several low-level issues surfaced along the way: deprecated `send(input=...)` frame encoding, Supabase pooler prepared-statement conflicts, SQLAlchemy enum name mismatches, and — the most stubborn — a radio-silence bug that killed every conversation after the first turn.

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
22. **Voice rebuild on Gemini Live** — "Switch to native-audio Gemini Live model and real browser mic + playback end-to-end"
23. **Turn-2 radio silence** — "I first greet it, then it responds, then I say something, and then radio silence. Is the simulation not happening properly?"
24. **Gender-aware voice** — "Add gender-aware voice selection matching the prototype"
25. **Character speaks first** — "Please add it so the character starts speaking immediately"
26. **Supabase Storage 403** — "Please fix the supabase storage 403 bug"
27. **End-to-end eval verification** — "Verify prompt caching kicks in on eval #2 and walk me through the checks"
28. **Autonomous SQL checks** — "You do the SQL checks directly. I'll run the sims. Give me two trainee emails."
29. **Diagnose stale `created_at`** — "Please fix the bug you pointed out"
30. **Sweep latent worker bugs** — "Please fix both: the pgmq SELECT \\* in kb\\_worker and the datetime footgun"
31. **Timestamp mixin migration** — "Please migrate all model timestamps to TimestampMixin"
32. **Portfolio update** — "Can you document this in portfolio?"
33. **Frontend buildout kickoff** — "Continue the frontend buildout from the design doc; start Phase 2 (trainee screens)"
34. **Scope cut on director phase** — Chose "Real CRUD + hub (5 screens)" instead of shipping all 11 director screens as mocks
35. **Mock audio playback** — "Can you mock a play button for now?" when the supervisor review page couldn't exercise recorded audio for seeded roster sessions
36. **UX audit follow-ups** — "Let's fix these next: UX audit of Phase 3" (identified trainee UUID leak on review page + insight-driven bento labels)
37. **Trainee name leak fix** — Replaced `evaluation.trainee_id.slice(0, 8)` UUID fragment with a nullable `SupervisorReviewBundle.traineeName` populated from the mock roster
38. **Admin account request** — "Can you give me a director account?" / "Tell me an admin account I can use"
39. **Bulk CSV file upload** — Shipped textarea-paste + client-side `File` synthesis because the backend is `multipart/form-data` and the UX felt cleaner than a file picker
40. **Phase 5 a11y polish** — "Yes, please fix it" after the UX audit flagged the `is_active` column glyphs as unlabeled for screen readers
41. **Continue until done** — "Let's kick it off and continue until we're done. I have lots of stamina left and a demo to do pretty soon"
42. **Merge queue housekeeping** — Two `/merge-updates` runs to promote Phases 3-4 and Phase 5 from the update queue into kanban + changelog
43. **Plan mode for Phase 5** — "Yes, /plan it" followed by scope-cut confirmation via `AskUserQuestion`
44. **Portfolio refresh** — "/portfolio-add" to update this very document with Phases 2-5

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

> "I'm noticing that if I start a conversation in the simulation, I first greet it, then it responds, then I say something, and then radio silence. Is the simulation not happening properly?"

> "1. yes, please. 2. yes, please add it so the character starts speaking immediately. 3. no need for now. also, please fix the supabase storage 403 bug."

> "please execute all the items you suggested, plus mine"

> "you do the sql checks and everything else you have direct access to, and i will take care of the parts that require human input, such as running the actual simulations. please tell me 2 different trainee emails, so i can run 2 of the same simulations within 5 minutes."

> "please fix the bug you pointed out, and i agree we can do #1 for the caching issue"

> "please go ahead and migrate all model timestamps to TimestampMixin. anything else you recommend doing in this session?"

## Technical Challenges

### Shared CRUD scaffold as a render-prop generic — build-once vs copy-paste

**Problem:** Phase 4 (director) introduced CRUD pages for 4 resources (simulations, how-tos, characters, rubrics) and Phase 5 extended the pattern to 3 more (admin users, SOPs, compensation policies). All shared ~90% of the same structure: list with responsive table/card collapse, right-side edit Sheet, ConfirmDialog-gated delete, TanStack mutation plumbing, status filter chips, inline error display. Hand-rolling each page would have meant ~500 lines of near-identical code × 7 pages = 3,500 lines of drift-prone copy-paste. But the resources differed meaningfully — simulations needed character + rubric dropdowns populated by secondary queries, rubrics needed a JSON criteria editor, characters needed a read-only `voice_config` display pre-formatted from the backend, admin users needed a bespoke "invite + bulk import" flow because the backend has no per-user CRUD.

**Root cause:** No cause to fix — this was a design decision, not a bug. The tension was between "premature abstraction" (a generic that can't express the variations) and "drift debt" (7 hand-written pages that fall out of sync).

**Solution:** Built a `ContentCrudPage<T extends { id: string }>` as a render-prop generic in `src/components/director/ContentCrudPage.tsx`. It owns the orchestration (header + new button + list + edit Sheet + delete ConfirmDialog + search param `?new=1` auto-open) and takes `columns: ColumnDef<T>[]`, `getRowTitle`, `renderEditForm: ({ initial, onClose, onSaved }) => ReactNode`, `onDelete`, `deleteConfirmMessage` as props. Each resource page is then ~150 lines: declare the columns, instantiate a small edit-form component with its specific fields, wire the fetchers. The render-prop pattern on `renderEditForm` preserves per-resource form variation (every form owns its own state hook and field composition). For Admin Users — which cannot fit the scaffold because the backend has no per-user CRUD — the page reuses `ContentTable` directly (generic enough to render arbitrary rows with custom columns) and ships two bespoke Sheets for the invite + bulk-import flows. Result: ~70% code reduction across the 7 CRUD pages, with the bespoke escape hatch where needed.

**Debugging approach:** Started by building the first page (simulations) hand-rolled, then built the second (how-tos) and noticed the ~90% duplication. Factored out `ContentCrudPage` before building the third. Phase 5 validated the scaffold by reusing it verbatim for SOPs and policies — literal copy-paste of the Phase 4 how-tos / rubrics pages with the fetcher imports swapped. Admin Users surfaced the edge case ("no per-user CRUD on backend") and forced the decision to keep `ContentTable` usable standalone instead of forcing every page through `ContentCrudPage`.

### Worker `now()` drift: Postgres transaction_timestamp frozen across idle polls

**Problem:** After the eval pipeline went live, routine verification of prompt caching turned up evaluation rows with `created_at` timestamps from hours *before their sessions even existed* — one was 10 hours off. Session IDs were correct; only the timestamps were wrong. Initial hypothesis blamed a SQLModel `default_factory=datetime.utcnow` footgun in the ORM layer, but no route constructs `Evaluation(...)` via SQLAlchemy — the worker writes rows via raw SQL with no explicit `created_at`, relying on the column's `DEFAULT now()`.

**Root cause:** The evaluation worker runs `psycopg2.connect(...)` with `conn.autocommit = False` and polls `pgmq.read` in a loop. When a poll returns zero messages the code sleeps and continues — without committing. That read-only transaction stays open for the entire idle window. Postgres `now()` returns `transaction_timestamp()`, the moment the transaction began, so when a real message finally arrives (sometimes many minutes later) the subsequent INSERT records `created_at` = the first empty poll's time, not the actual insert time. A one-line fix — `conn.rollback()` on the empty-poll branch — makes each iteration open a fresh transaction.

**Debugging approach:** Queried staging directly via the Supabase Management API `/database/query` endpoint (the pooler rejected the Doppler-stored passwords). Joined `simulation_sessions`, `evaluations`, `ai_usage`, and `pgmq.a_evaluation_jobs` on session_id to spot the cross-wire pattern: each eval's `created_at` matched the *previous* job's archive time, not its own. Walked the worker source to trace the psycopg2 transaction lifecycle and confirmed the idle-poll transaction was the culprit. Also caught two latent bugs while in the code: `kb_worker.py` still used `SELECT *` from `pgmq.read` with positional unpacking (already broken by Supabase's new `headers` column, would have crashed on the next doc upload), and the SQLModel timestamp footgun itself — fixed both in the same pass.

### Prompt caching silently below Anthropic's 1024-token minimum

**Problem:** Two back-to-back eval runs on the same scenario + rubric within 2 minutes both reported `cached_tokens = 0`. Cache should have hit on the second run.

**Root cause:** Not a code bug. The worker was structured correctly — `cache_control: {"type": "ephemeral"}` on the stable system block, transcript in a separate uncached block. But total input was only 888-954 tokens, below Claude Sonnet's 1024-token minimum cacheable block size. Anthropic silently returns zero cached tokens in that case with no error or warning. Deferred the fix (padding the stable block) since pilot-scale savings are trivial and real rubric/policy content will cross the threshold organically.

### Gemini Live turn-2 radio silence

**Problem:** After rebuilding the voice pipeline on Gemini 2.5 Flash Native Audio, every simulation ran beautifully for exactly one exchange and then went dead. The trainee would greet the AI, the AI would respond with perfect native-audio voice, the trainee would speak again, and then — nothing. No audio, no transcript, no error. Just silence until the 8-minute time cap fired.

**Root cause:** The `google-genai` SDK's `AsyncSession.receive()` is a turn-scoped async iterator, not a continuous stream. At `google/genai/live.py:459` it explicitly `break`s out of its inner loop after seeing `turn_complete=True`. Our `GeminiLiveClient.receive()` wrapper called `async for msg in self._session.receive()` exactly once — so after turn 1 the generator exited, the outer `pump_gemini_to_ws` task saw the end of its iterator, cleanly returned, and the WebSocket sat open with nothing listening to Gemini anymore. The docstring on the wrapper even said "Loop it so the WS handler sees a continuous stream across turns" — but the code didn't actually loop.

**Solution:** Wrapped `self._session.receive()` in an outer `while True:` so a fresh turn iterator is entered after each `turn_complete`. Verified by reading the SDK source directly to confirm the break-after-turn behavior before touching the code.

**Debugging approach:** Built a Python probe that drove the pipeline with synthetic audio and inspected the raw `LiveServerMessage` stream — which initially pointed at a red herring (`interrupted=True` on audio input). A text-input smoke test proved the model + config + SDK were healthy, so the issue had to be in our receive loop. Testing from a real browser with echo cancellation eliminated the `interrupted` red herring (the probe was interrupting itself). That left exactly one suspect: the wrapper's loop structure. Reading `google/genai/live.py:433-460` confirmed the turn-scoped break and closed the case.

### Voice pipeline rewrite from turn-based to Gemini Live

**Problem:** The original voice stack was a chain: Deepgram Nova-3 ASR → Gemini 2.0 Flash LLM → Cartesia Sonic TTS. Three vendors, three API keys, three latency budgets, and a lot of glue. When Gemini 2.5 Flash Native Audio became available — a single model that handles ASR, reasoning, and TTS over one WebSocket — the whole chain could collapse, but every layer of the stack had to be rewritten.

**Root cause:** The existing `voice_session.py` was built around discrete turn boundaries and HTTP calls, not a bidirectional WebSocket with PCM streaming. The browser side used no real mic capture. FastAPI's WebSocket route had no pump-task architecture. And cross-origin browser auth had to be solved because `WebSocket` has no way to send `Authorization` headers.

**Solution:** Built a `LiveClient` Protocol so route code never imports `google.genai` directly. Implemented `GeminiLiveClient` using `client.aio.live.connect(...)` + `send_realtime_input(...)` + an async `receive()` generator that yields normalized `LiveServerEvent` records (audio, input transcript, output transcript, turn-complete, interrupted). On the route side, a `pump_gemini_to_ws` task pushes Gemini audio/transcripts to the client while the main receive loop forwards browser mic frames the other way. Browser-side: `getUserMedia` + `ScriptProcessorNode` resample to 16 kHz PCM16 and send as WebSocket binary frames; incoming binary frames feed a 24 kHz `AudioContext` playback queue. Auth is smuggled through the `Sec-WebSocket-Protocol` header as `['bearer', <supabase access_token>]` subprotocols.

**Debugging approach:** Compared the Google AI Studio reference prototype (`reference/google-ai-studio-prototype/components/SimulationRoom.tsx`) against the new implementation field-by-field to align `speechConfig`, `inputAudioTranscription`, and `outputAudioTranscription`. Used a Python probe to validate each layer independently before touching the browser.

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
- **Gemini Live as the only voice path** — A single model (Gemini 2.5 Flash Native Audio) handles ASR, reasoning, and TTS over one WebSocket. No feature flags, no turn-based fallback. Vendor swaps happen by forking the `LiveClient` Protocol in `voice_session.py`, per ADR-005
- **Claude Sonnet 4.6 for async evaluation** — Scoring runs post-session in a pgmq worker with prompt caching on the stable rubric/policy/scenario block; retries up to 3x on malformed JSON
- **Terraform + HCP Terraform** — Remote state across dev/staging/prod workspaces, with Doppler injecting secrets as `TF_VAR_` environment variables
- **Multi-tenant with property override** — Content is org-scoped by default, property-overridable; operational data is always property-scoped. This avoids per-property content duplication while allowing local customization
- **Cost-first vendor selection** — Every voice provider choice was modeled at lite/medium/high usage tiers with explicit $/user/month breakdowns before committing
