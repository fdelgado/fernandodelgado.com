---
title: Project Caracas
slug: project-caracas
description: An AI-powered interactive marketing diagnosis wizard for MKTGBrains — an 11-step guided survey that researches a prospect's company and competitors in the background, renders a live scorecard and tier recommendation, and delivers a branded PDF brief. Built on React 19 + Cloudflare Pages Functions + D1 at the edge, with Claude Sonnet 4.6 and Haiku 4.5 split across tasks to stay under rate limits.
date_started: 2026-04-08
date_completed: 2026-04-17
active_hours: 11.2
sessions: 7
total_prompts: 116
tech_stack:
  - React 19
  - Vite
  - TypeScript
  - Tailwind CSS v4
  - Framer Motion
  - Cloudflare Pages Functions (Workers)
  - Cloudflare D1 (SQLite at edge)
  - Anthropic Claude API
  - Claude Sonnet 4.6
  - Claude Haiku 4.5
  - "@anthropic-ai/sdk"
  - "@react-pdf/renderer"
  - Playwright
  - Docker
  - Cloudflare Pages
platform: Web
lines_of_code: 7081
files: 86
commits: 30
status: completed
hidden: false
cover_image: /images/projects/project-caracas/cover.png
screenshots:
  - path: /images/projects/project-caracas/email-step.png
    alt: Landing email-capture step with MKTGBrains kinetic orange gradient
  - path: /images/projects/project-caracas/company-confirm.png
    alt: Auto-detected company profile with industry, location, and confidence score
  - path: /images/projects/project-caracas/competitors.png
    alt: AI-identified top competitors with the add-your-own form
  - path: /images/projects/project-caracas/channels.png
    alt: Channel audit with + Add custom channel chip
  - path: /images/projects/project-caracas/budget-goals.png
    alt: Budget bands and up-to-2 primary goal selection
  - path: /images/projects/project-caracas/preview-scorecard.png
    alt: 5-dimension scorecard scoring the prospect vs competitors
  - path: /images/projects/project-caracas/pdf-brief.png
    alt: Downloadable branded PDF brief with orange bullets and CTA card
tags:
  - ai
  - marketing
  - wizard
  - cloudflare
  - claude-api
  - react
  - edge
  - client-work
---

## Summary

Project Caracas is an AI-powered interactive marketing diagnosis wizard built for MKTGBrains. A prospect enters their work email, and an 11-step wizard auto-detects their company, identifies top competitors, asks guided questions about budget, goals, and channels, and renders a live 5-dimension scorecard plus a branded PDF brief — all without the user typing more than their email. The entire stack runs at the edge on Cloudflare Pages Functions + D1, with Claude Sonnet 4.6 and Haiku 4.5 split across tasks (Haiku for web_search-grounded research, Sonnet for diagnosis and scorecard reasoning) to stay inside Anthropic's per-model rate limits while running three agents in parallel. Built across seven sessions (~11 active hours) with 30 commits and a Playwright scenario gate that blocks shipping on broken flows.

## Features

- **Email-to-diagnosis in 2 minutes**: drop a work email, and background agents scrape the domain, identify the company, pick industry options, detect channels, and find top-3 competitors before the user finishes step 1
- **11-step guided wizard with URL-per-step**: email → company confirm → business type → products → differentiators → competitors → channels → budget + goal → goal refine → first need → timeline → analyzing → preview. Browser back works naturally; the URL reflects where you are
- **Auto-company research**: Haiku 4.5 + web_search reads the domain, pulls industry, location, products, and detected channels into a typed `CompanyData` shape — the prospect just confirms
- **Top-3 competitor identification**: a second Haiku + web_search agent surfaces real local/national competitors grounded in search results, not model-invented names
- **Add-your-own competitor with live enrichment**: users can add competitors the AI missed — each one fires a `/api/research/competitor-lookup` call that returns a 1-sentence positioning description, swapped into the card when it arrives
- **Multi-select with + Add chips**: products and channels support both toggling detected options and adding custom ones via an inline chip-shaped input
- **Primary + secondary goals (choose up to 2)**: multi-select goal grid with a per-step cap, surfacing both into the downstream prompts and the PDF brief
- **AI-refined focus**: a secondary Haiku call that narrows the chosen goal into 4 specific focus alternatives tailored to the company
- **5-dimension scorecard**: Sonnet scores the prospect vs all confirmed competitors on positioning, offer/pricing, content/communications, customer journey, and social proof — each with a 1-sentence note referencing specific public signals
- **Parallel diagnosis + scorecard**: diagnosis prompt and scorecard prompt fire simultaneously via `Promise.all`, each ~half the original single-shot prompt, so the whole preview renders in ~15s wall clock instead of 30s
- **Branded PDF brief**: `@react-pdf/renderer` generates a download-on-demand brief with the MKTGBrains kinetic gradient header, orange bullet points, per-section icons, and a "book a call" CTA card
- **Demo mode (`?demo`)**: bypasses every backend call with pre-loaded mock company, competitors, and preview — used for click-through demos, stakeholder feedback sessions, and Playwright scenario tests
- **Optimistic UI throughout**: chip toggles, competitor adds, and navigation are all instant; network calls fire in the background with graceful fallback if they fail
- **Citation sanitization**: a `sanitizeDeep` helper strips Claude web_search's `<cite index="1-1">` tags and HTML entities from every string field of every response, on both backend save and frontend render — so legacy dirty data in D1 never leaks into the UI
- **Rate-limit-aware agent routing**: Haiku handles web_search-grounded research (its 50K input-token budget absorbs the 15-20K tokens of search results), Sonnet handles pure reasoning — doubles the effective rate limit on a single-tier Anthropic account
- **Playwright scenario gate**: `scripts/gate-check.sh` typechecks + runs 6 scenarios before every commit via `/commit-smart`; covers email fallback, company research failure recovery, demo smoke, and competitor manual add
- **Auto-deploy on push**: Cloudflare Pages hook deploys main on every push. The PDF, scorecard, and wizard UI all render at the edge with no separate backend infra

## How It Was Built

The project started as a design-first exercise — before any code, Fernando generated UI mockups in Google Stitch using a handcrafted product summary, then translated those mockups into a React + Tailwind scaffold. Design tokens (kinetic-gradient orange, font-headline, on-surface color roles) were locked in early and every step reuses them.

The wizard backbone is a `useReducer`-based state machine with one orchestrator (`WizardOrchestrator.tsx`) and one leaf component per step. Each step dispatches typed actions (`SET_EMAIL`, `SET_GOAL`, `SET_PRODUCTS`, etc.) into a single `WizardState` object. Background research runs in a `useBackgroundResearch` hook that polls `/api/status/{sessionId}` every 2 seconds and hydrates the state as agents complete — the user advances through the wizard while the AI works behind them. Every step has its own URL, seeded via `history.pushState` so browser back rewinds the reducer instead of losing state.

The backend is pure Cloudflare Pages Functions. Three agent endpoints (`/api/research/company`, `/api/research/competitors`, `/api/research/competitor-lookup`) and one preview endpoint (`/api/preview/generate`) each use `context.waitUntil` to run AI work in the background after returning `{status: "processing"}` immediately. Agent results land in D1's `agent_results` table; final preview lives in a dedicated `preview` table with flags saved separately. The frontend never talks to D1 directly — all DB access is through typed D1 helpers in `functions/api/_shared/db.ts`.

The hardest phase was wiring user-added competitors into the preview pipeline. The original flow read competitors from the `competitor_research` agent result, so anything the user added manually was dropped from the final scorecard. The fix required two moves: (1) a new enrichment endpoint that looks up a single user-added competitor via Haiku + web_search at add time, and (2) rewiring `preview/generate.ts` to prefer the user's confirmed list from the `responses` table, falling back to agent results only for older sessions that saved just names. The prompt's prospect block now gets rich `Name — 1-line description; Name — 1-line description` pairs instead of a comma-separated names string.

The final pass was a 9-step layout sweep: every wizard page's heading sizes, margins, and CTA padding got tightened so they fit short viewports (browsers with tab-sharing bars) without scrolling — verified with a UX audit that also caught two accessibility issues (a retry button with too-generous padding and a Cancel button with no background to distinguish it as a button).

## Prompts

Key requests that drove the build, in order:

1. **Project summary for Stitch design** — "can you summarize what this project is about in a way i can feed it to google's stitch, so it can help me design a ui for it?"
2. **Secure API key storage** — Asked how to store the Anthropic key locally in the safest way
3. **Match MKTGBrains brand orange** — "Can you change the primary color to match mktg brain's orange shade?"
4. **Deploy click-through demo to Cloudflare Pages** — "can you push the click-through mock demo http://localhost:5176/?demo to a cloudflare page, please?"
5. **Extract PPTX feedback from Andres** — "i just added a pptx in references that has landing page feedback from Andres. Are you able to extract his feedback?"
6. **Plan from feedback, with honest opinion** — "yes, let's /plan around this. and from his feedback, what are your honest opinions about what we should do?"
7. **Build Docker dev environment** — Accepted the Docker setup proposal for reproducible dev
8. **Hitting rate limits during evaluation** — "i keep hitting the limits in evaluation and having to click retry. what's the issue?"
9. **Save tokens by skipping competitor research?** — Explored whether dropping a research agent would free budget; ended up splitting agents across Haiku/Sonnet instead
10. **+ Add products and services** — "in this page, please add a plus button to add services and products"
11. **Fix vertical scrolling on wizard steps** — "this page is vertical scrolling and shouldn't. please fix"
12. **Primary goals (choose up to 2)** — "please change to 'Primary goals (choose up to 2)' in title and functionality"
13. **Treat user-added competitors like AI ones** — "i added a couple of competitors manually, and it didn't use them here. Please make sure once i add competitors, that you treat them exactly as you treated the ones you came up with"
14. **Clean up HTML and escape weird characters** — "please clean up html and escape weird characters when I add a competitor"
15. **Remove the 64% stat** — Decided to drop a hardcoded "64% of local digital reach" factoid that showed on every session
16. **Documentation + cleanup pass** — Asked for a portfolio write-up and any remaining cleanup

## Raw Prompts

Substantive user messages from the conversation, preserving original wording:

> "can you summarize what this project is about in a way i can feed it to google's stitch, so it can help me design a ui for it?"

> "i want to store the anthropic key locally in the safest manner possible. how do i do that?"

> "Can you change the primary color to match mktg brain's orange shade?"

> "can you push the click-through mock demo http://localhost:5176/?demo to a cloudflare page, please?"

> "i just added a pptx in references that has landing page feedback from Andres. Are you able to extract his feedback?"

> "yes, let's /plan around this. and from his feedback, what are your honest opinions about what we should do?"

> "i keep hitting the limits in evaluation and having to click retry. what's the issue?"

> "if we skipped competitor research, would we save a ton of tokens?"

> "in this page, please add a plus button do add services and products: Confirm your products & services."

> "this page is vertical scrolling and shouldn/t please fix"

> "please change to 'Primary goals (choose up to 2)' in title and functionality"

> "i added a couple of competitors manually, and it didn't use them here. Please make sure once i add competitors, that you treat them exactly as you treated the ones you came up with, in terms of looking up their information (right when I add them), and also at the final evaluation"

> "please clean up html and escape weird characters when I add a competitor"

> "for 3, do remove the 64% stat"

## Technical Challenges

### Hitting Anthropic's per-model rate limit during preview

**Problem:** Users kept hitting rate-limit errors during the final preview step and had to click retry. The three-agent background research (company + competitors + preview) plus the single-shot diagnosis-and-scorecard Sonnet call saturated the per-minute input-token bucket, especially when `web_search` returned results (search tokens count as input).

**Root cause:** All agents defaulted to Sonnet 4.6. Anthropic's rate limits are per-model — Sonnet had ~30K input tokens/min, Haiku had ~50K. Pushing 15-20K tokens of search results plus prompt overhead into Sonnet left nothing for the diagnosis call.

**Solution:** Routed web_search-grounded calls (company research, competitor research, competitor-lookup) to Haiku 4.5, and split the single-shot preview prompt into two parallel calls (diagnosis + scorecard) running via `Promise.all`. Each call became ~half the token footprint, and the Haiku bucket absorbed the web_search overhead. Doubled the effective budget on a single-tier account.

**Debugging approach:** Inspected the Anthropic error response to distinguish rate-limit from auth failures. Read the SDK's retry behavior, profiled token counts per agent via `console.log` on prompt lengths, and validated the split by watching per-model headers in the response metadata.

### User-added competitors dropped from the final preview

**Problem:** A user added two competitors manually on the Competitor step. The final scorecard Positioning card only showed the AI-suggested competitors — the manual ones were nowhere. Screenshot confirmed they were missing from the scored list entirely.

**Root cause:** Two layers. First, `preview/generate.ts` read competitors from `agent_results` (the AI-generated research) rather than from the user's confirmed list. Second, even if we switched sources, the confirmed list was being saved as `JSON.stringify(names)` — just names, no descriptions — so the Sonnet scorecard (which runs without web_search) couldn't reason about them.

**Solution:** Built a new `/api/research/competitor-lookup` endpoint that enriches a single manually-added competitor via Haiku + web_search, returning a 1-sentence positioning description. `CompetitorStep` now saves the full `Competitor[]` objects (name + domain + description) as JSON on Continue. `preview/generate.ts` prefers that confirmed list and falls back to agent results only for old sessions. The scorecard prompt receives rich `Name — description; Name — description` pairs so every confirmed competitor gets scored.

**Debugging approach:** Traced the data flow from CompetitorStep → saveResponse → D1 responses table → preview/generate.ts reading. Inspected what the Sonnet prompt actually received via the `prospectBlock` helper. Wrote a Playwright scenario (`competitor-manual-add.spec.ts`) that stubs the lookup endpoint and verifies the added competitor surfaces in the confirmed list.

### Claude web_search citations leaking into the UI

**Problem:** Competitor cards displayed literal `<cite index="1-1">Aesthetic and anti-aging medicine practice</cite>, with <cite index="1-31">advanced non-invasive laser technologies</cite>` instead of clean prose. Same artifact appeared in the scorecard notes once the preview rendered.

**Root cause:** Claude's `web_search` tool wraps cited text in `<cite>` tags inside the JSON string fields it returns. `parseJsonResponse` parses the JSON fine — the tags sit inside the string values, not the JSON structure itself.

**Solution:** A `sanitizeText` helper strips HTML tags and decodes common entities (`&amp;`, `&quot;`, etc.) via regex. A `sanitizeDeep` variant recursively applies it to every string in a parsed JSON payload, so diagnosis + scorecard outputs get scrubbed before saving to D1. Applied at four points: at agent save time (`competitors.ts`, `competitor-lookup.ts`), at preview parse time (`preview/generate.ts`), and at render time in the frontend (`CompetitorCard`, `CompetitorStep`) as belt-and-suspenders for legacy dirty data still sitting in D1. Consolidated the two runtime copies (Workers + browser) into a single file that the backend re-exports.

**Debugging approach:** Inspected the raw Claude response in the Cloudflare logs to confirm the tags originate server-side, not in the frontend. Tested the sanitizer with the exact dirty string from the screenshot, verified it renders as clean text, and confirmed D1's existing rows were scrubbed at render time without a migration.

### Wizard steps overflowing short viewports

**Problem:** Several wizard steps (Channels, Budget, Goal Refine, etc.) caused vertical scrolling when the user's browser had a tab-sharing bar, bookmark bar, or URL suggestions panel open. The design assumed a full-height viewport — on a short window, the heading + options + insight card + CTA button didn't fit and scrolled awkwardly.

**Root cause:** Heading sizes (`text-3xl sm:text-4xl md:text-6xl`) were optimized for a hero page, not for 11 short survey steps. Combined with `mb-6` / `mt-4` margins, `p-6` container padding, and `py-4` CTAs, each step needed ~900px of vertical real estate on desktop — more than most short windows offered.

**Solution:** Applied a consistent tightening pattern across all 11 step components: `text-6xl` → `text-4xl` at the largest breakpoint, `mb-6` → `mb-4`, `mt-4` → `mt-2`, `p-6` → `p-4`, `py-4` CTA → `py-3`. Audited via a UX guidelines skill that also caught two outliers (a retry button still at `py-4` and a Cancel button with no background). Every step now fits a 720px viewport with room to spare.

**Debugging approach:** Delegated the pattern audit to an Explore subagent with the exact class-string changes to look for, so it returned a compact table of per-step diffs. Verified visually on production after deploy by curling all 11 URLs for 200s and then eyeballing them in a browser.

## Architecture

Key technical decisions:

- **Cloudflare Pages Functions + D1 over Render/Vercel** — The whole stack runs at the edge: React bundle from Pages, API handlers as Workers, sessions/responses/agent results in D1 (SQLite at edge). No separate backend infra, no cold starts, deploy is `git push`. The trade-off is the 30-second `waitUntil` budget on background work, which drove the decision to split the single-shot preview prompt into two parallel calls.
- **`useReducer` wizard state, not TanStack Query** — The wizard is stateful and linear; every step mutates a single `WizardState` through typed actions. TanStack would have been overkill — the state never comes back from the server after initial hydration. Background research streams in via a separate polling hook that dispatches `SET_COMPANY_DATA` / `SET_COMPETITORS` when agents finish.
- **URL per step with `history.pushState`** — Each of the 11 steps has its own path (`/company`, `/business`, `/products`, ...). Browser back rewinds the reducer via `popstate`. No router library — the orchestrator listens for `popstate` and dispatches `SET_STEP`.
- **Haiku for web_search, Sonnet for reasoning** — Model choice is per-agent: Haiku 4.5 runs anything that needs `web_search` (absorbs search-result tokens), Sonnet 4.6 runs pure reasoning (diagnosis, scorecard, goal refine). Effectively doubles the Anthropic rate budget on one API key.
- **Parallel `Promise.all` for preview** — The original preview prompt was one ~2500-token call. Split into diagnosis (1200 max tokens) + scorecard (1500 max tokens) firing in parallel — total wall clock is `max(a, b) ≈ 15s` instead of `a + b`, and each call fits comfortably in the Workers `waitUntil` budget.
- **D1 as an append-only event log** — `responses` table stores each user answer keyed by step + field. `agent_results` stores each AI phase with status + JSON. `preview` stores the final output. No mutations on existing rows — easier to debug and allows resuming a session if needed.
- **Optimistic UI everywhere** — Per the project CLAUDE.md: every user action produces instant visual feedback. Chip toggles are local state, competitor adds show optimistically with a "Researching..." placeholder, PDF download disables the button instead of waiting for server ack. Failures fall back to safe defaults without blocking the flow.
- **Scenario-gated commits via `/commit-smart`** — `scripts/gate-check.sh` typechecks + runs 6 Playwright scenarios before every commit. A red gate blocks shipping. Demo mode (`?demo`) makes the scenarios deterministic without standing up a fake backend.
- **Sanitize once, re-export to both runtimes** — One file in `src/lib/sanitize.ts` with both `sanitizeText` and `sanitizeDeep`. The Workers runtime re-exports from there via a relative import. No duplicate implementations to drift.
