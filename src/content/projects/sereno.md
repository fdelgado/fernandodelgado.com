---
title: Sereno
slug: sereno
description: Personal productivity agent that scans email, classifies action items, executes easy tasks, maintains 428 people profiles from Fathom transcripts, and presents an interactive morning digest. Built entirely with Claude Code skills (markdown instructions), a FastAPI dashboard, a Chrome extension, and Gmail/Calendar MCP integrations.
date_started: 2026-04-07
date_completed: 2026-04-24
active_hours: 14.0
sessions: 5
total_prompts: 39
tech_stack:
  - Claude Code (Skills + Triggers)
  - Python 3.11
  - FastAPI
  - Jinja2
  - Tailwind CSS
  - HTMX
  - Chrome Extension (Manifest V3)
  - Fathom API
  - Gmail MCP
  - Google Calendar MCP
  - LaunchD
  - Markdown (state + profiles)
platform: macOS / CLI / Chrome
lines_of_code: 4586
files: 1426
commits: 46
status: in-progress
cover_image: /images/projects/sereno/cover.png
screenshots:
  - path: /images/projects/sereno/commitments.png
    alt: Commitments dashboard showing I owe / they owe items across 176 tracked commitments
  - path: /images/projects/sereno/connections.png
    alt: Connections page with 19 relationship clusters (Google, Barcelona, LikeMagic, etc.)
  - path: /images/projects/sereno/morning-digest.png
    alt: Morning digest output in Claude Code CLI with action items classified and executed
tags:
  - ai
  - productivity
  - crm
  - automation
  - mcp
  - fathom
---

## Summary

Sereno is a personal productivity agent that extracts action items from email, classifies them by difficulty, executes what it can, and maintains rich profiles of 428+ contacts from Fathom meeting transcripts. Built across ~14 active hours and 5 sessions using Claude Code skills (pure markdown instructions, no custom runtime), a FastAPI + HTMX dashboard, a Manifest V3 Chrome extension that injects a per-contact context bar on top of Gmail, Meet, and LinkedIn, and Gmail/Calendar MCP integrations. The system polls Fathom every 30 minutes for new transcripts and auto-enriches profiles in near real-time.

## Features

- Morning digest pipeline: fetch transcripts, scan email, classify actions, execute easy tasks, enrich people, present interactive digest
- Email scanning across Gmail accounts with deduplication and source-type classification (self-notes, Fathom summaries, contact requests, system alerts)
- Action item classification (easy / need-input / difficult) with auto-execution of easy tasks (Gmail draft creation, research, calendar proposals)
- 336 people profiles with interaction history, key topics for next meeting, I owe / they owe tracking, and LinkedIn enrichment
- Commitments dashboard (`/commitments`): cross-profile view of 176 outstanding commitments with direction filter, search, and mark-obsolete via HTMX
- Connections map (`/connections`): 19 relationship clusters extracted via keyword co-occurrence (Google 112, Barcelona 69, LikeMagic 33, etc.)
- Real-time Fathom polling every 30 minutes via LaunchD, with automatic profile enrichment when new transcripts arrive
- Batch transcript processing: 107 transcripts (77 Margarita, 30 Meghin) processed via 5 parallel background agents
- Email handle-to-name resolution and directory renaming for cleaner profile data
- Progressive autonomy model: reads are autonomous, writes require human approval
- Sereno Chrome extension: injects a per-contact context bar on top of Gmail, Google Meet, and LinkedIn, pulled from the local dashboard API
- Reconciliation CLI: `python fathom_sync.py --reconcile` scans for stub profiles with transcripts and queues them for enrichment, closing the gap where daily sync landed transcripts without triggering the Claude enrichment pass
- ASCII slug normalization: diacritic-stripping `slugify()` plus a cleanup pass that merged 11 duplicate profile pairs (same person, different slug forms) into canonical full-name directories

## How It Was Built

The project started with a skills-based architecture: each pipeline step (scan-email, classify-actions, execute-actions, enrich-people) is a standalone Claude Code skill defined in markdown. No Python runtime for the core logic. The morning-digest skill orchestrates them in sequence, and a LaunchD plist triggers the full pipeline daily at 8am.

The first session established the pipeline, processed initial emails, and created the action item lifecycle. The second session tackled the people profile backlog: 500+ Fathom transcripts were fetched via the Fathom API and matched to person directories using slugified name/email matching. Profiles were enriched with interaction history, milestones, and bidirectional action items.

The third session was the most ambitious: a full morning digest run, processing 107 remaining transcripts via 5 parallel background agents, building two new dashboard pages (Commitments and Connections), generating a DTC product strategy from transcript data, creating the Aprender course project, drafting and sending a personalized email, and implementing real-time Fathom polling with auto-enrichment. The person-matching algorithm was also fixed after a misfiled transcript revealed a substring matching bug.

The fifth session focused on resilience and data hygiene. It started with a Chrome-extension bug report (the Sereno bar disappearing after Gmail SPA navigation) that traced to Google ripping the extension's root element out of the DOM during view changes, with a stale `state.built` flag preventing rebuild. The investigation then surfaced a deeper data gap: the daily Fathom sync was landing transcripts but not triggering the Claude enrichment pass (only the 30-min poll wrote `pending_enrichments.json`), leaving 116 stub profiles with unread transcripts. The fix extracted the enrichment-queue write into a shared helper called from both sync paths, added a `--reconcile` CLI mode to flush the existing backlog, and dispatched six parallel background agents to enrich all 116 stubs from their transcripts. A follow-on cleanup normalized profile directory slugs to pure ASCII, merged 11 duplicate profile pairs that had accumulated from different slug forms of the same person, and normalized 42 transcript filenames from Unicode to ASCII.

## Prompts

Key requests that drove the build, in order:

1. **Morning digest** -- "Run the full Sereno pipeline and present the digest"
2. **Course correction** -- "This course is NOT for DTC. It's for Aprender, an online school in Brazil. Create a new project folder"
3. **Katie email draft** -- "Draft an email response for Katie. Purely personal catch up, share 1-2 life updates, wish her luck"
4. **DTC product strategy** -- "Try to generate a DTC product strategy based on all you know"
5. **Transcript backlog** -- "Process the remaining margarita (77) and meghin (30) older transcripts"
6. **Handle renaming** -- "Enrich the newly-identified people from email handles (terhorstlaura1, kmsnagesh, lukeniquintana)"
7. **Commitments dashboard** -- "Build the I owe / they owe dashboard view with ability to mark items obsolete"
8. **Connections map** -- "Extract cross-contact insights. A relationship map view could surface these clusters"
9. **Fathom auto-sync** -- "Scan Fathom for new transcripts automatically"
10. **Real-time polling** -- "Plan a feature to check Fathom every 30 minutes, trigger enrichment for that person in real time"
11. **Email style feedback** -- "I never like to use em dashes. Please add this to the global CLAUDE.md"
12. **Mateo enrichment** -- "The conversation happened over Google Meet. Fathom has the transcript"
13. **Albert fix** -- "The last meeting was not Albert Song, it was Albert Zamora. Please fix"
14. **Gmail bar regression** -- "I click on someone in Gmail, then go to inbox, then click on another person. Instead of seeing the Sereno bar on top, I see whitespace"
15. **Enrichment gap diagnosis** -- "Why haven't you enriched profiles such as fda@umich.edu? What are you missing?"
16. **ASCII slugs** -- "Should we change handles so they use basic ASCII characters, and no accents?"
17. **Proper cleanup** -- "Let's do it as a proper cleanup please" (ASCII normalization plus merging 5 duplicate profile pairs)
18. **Transcript filename pass** -- "Let's normalize transcript filenames, too please"
19. **More merges** -- "Let's merge them. No need to fix morning-digest yet" (6 more duplicate pairs identified via shared transcripts)
20. **Fact correction** -- "Tomás Kabbabe should not be affiliated with Anthropic at all"

## Raw Prompts

Substantive user messages from the conversation, preserving original wording:

> "this course is NOT for DTC. It's for Aprender, an online school in Brazil. the title is Product Management in the era of AI. Please create a new project folder for this, and I will add more context later."

> "yes, please draft an email response for Katie. purely personal catch up right now, please share 1-2 life updates you've been able to get from the last 6-12 months, and do wish her luck in her competition."

> "do try to generate a dtc product strategy based on all you know, including the Projects I've set up on claude online. If you don't get it right, it's ok. I'll do a pass myself that you can learn from."

> "Process the remaining margarita (77) and meghin (30) older transcripts -- these are your two most important operational relationships and we only did the recent ones"

> "Build the 'I owe / they owe' dashboard view. I want to make sure I can also mark items obsolete, so they're not tracked anymore"

> "Extract cross-contact insights -- the LikeMagic investment thread touches ~15 profiles. A relationship map view could surface these clusters"

> "please /plan a feature to check fathom once every 30 minutes to see if new transcripts have been generated, and if so, trigger an enrichment session for that person? I want things that you learn about them to get tracked almost in real time."

> "katie email sent. can you learn what i tweaked from it? for instance, i never like to use em dashes. please add this to the global claude.md file."

> "the last meeting was not albert song, it was albert zamora. please fix. yes, enrich the profiles."

> "i click on someone in gmail, then go to inbox, then click on another person. instead of seeing sereno bar on top, i see whitespace"

> "looks good, yes, please commit. also, i see you haven't enriched profiles such as fda@umich.edu - what are you missing?"

> "should we change handles so they use basic ascii characters, and no accents?"

> "let's do it as a proper cleanup please"

> "yes, let's normalize transcript filenames, too please"

> "yes, let's merge them. no need to fix morning-digest yet"

> "tomas kabbabe should not be affiliated with anthropic at all - not sure where you got that from."

## Technical Challenges

### Batch Transcript Processing at Scale

**Problem:** 107 Fathom transcripts (77 for Margarita, 30 for Meghin) needed processing, but each is a long meeting recording. Reading all in one session would exceed context limits.

**Root cause:** Single-agent processing can't handle the volume. Transcripts range from a few pages to 20+ pages of full meeting recordings spanning June 2024 to December 2025.

**Solution:** Split into 5 parallel background agents, each handling a chronological batch. Each agent writes extracted interaction history entries to a temporary file rather than editing profiles directly (avoiding edit conflicts). After all agents complete, a Python script combines entries in reverse chronological order and patches the profiles.

**Debugging approach:** Monitored batch file creation with `wc -l` checks, validated entry counts against expected transcript counts, and verified the "Older Transcripts" section was fully replaced.

### Email Prefix Substring Matching Bug

**Problem:** A new Fathom transcript from "The Boutique Surfhouse Hotel" with participant `albert@theboutiquesurfhouse.com` was stored in Albert Song's directory instead of creating a new Albert Zamora profile.

**Root cause:** `find_person_dir()` in `fathom_sync.py` used `email_name in d.name` for email-based matching (line 75). The email prefix "albert" is a substring of "albert-song", causing a false match.

**Solution:** Changed to exact match: `d.name == email_name`. This prevents partial prefix collisions while still matching people whose directory name is their email handle (e.g., `meghin` matching `meghin` exactly).

**Debugging approach:** Read the transcript frontmatter to see the participant email, traced through `find_person_dir()` logic, identified the substring check as the culprit.

### Commitments Parsing Across Inconsistent Formats

**Problem:** The 336 people profiles use two different formats for I owe / they owe data: most use inline Quick Reference fields with semicolon-delimited items, but Meghin Gani's profile uses sub-headings with bullet lists.

**Root cause:** Profiles were enriched at different times by different agent sessions, leading to format drift.

**Solution:** The `_extract_quick_reference()` parser already extracts inline fields as flat strings. The commitments dashboard splits on semicolons and filters out placeholder values ("--", "-"). Meghin's sub-heading format is handled because the Quick Reference parser stops at `## ` boundaries, capturing the inline version when present. The `data/obsolete_commitments.json` file tracks dismissed items by content hash, avoiding edits to profile files (respecting the "never overwrite people files" rule).

**Debugging approach:** Tested with `get_commitments()` directly in Python, verified 176 items parsed correctly (94 I owe, 82 they owe), checked edge cases with empty and placeholder values.

### Gmail SPA Ripping the Extension Out of the DOM

**Problem:** The Sereno Chrome extension bar would render correctly on the first Gmail thread, hide correctly on the inbox list, and then reappear as empty whitespace (padding, no bar) after navigating to a second thread.

**Root cause:** A two-layer bug. First, `build()` in `bar.js` guarded on a closure-local `state.built` flag, which stayed `true` forever after the first render. When Gmail's SPA navigation swapped out the DOM subtree containing the extension root, `build()` would early-return even though `#sereno-root` no longer existed. Subsequent calls to `show()` would then hit null-element exceptions, but only after the static CSS rule `body[data-sereno]{padding-top:36px!important;}` had already been applied, producing a 36-pixel gap with no visible bar.

**Solution:** Replaced the flag-based guard with a DOM check: `if (document.getElementById('sereno-root')) return;`. This makes `build()` idempotent against Gmail's tree mutations. Dropped the now-unused `built` field from `state`. The extension now rebuilds its root whenever Gmail has removed it.

**Debugging approach:** Traced the user report through the scan loop in `gmail.js`, diffed the last extension fix commit to see what had changed, reasoned about the interplay between the static CSS rule (always applied when `data-sereno` attribute is set) and the extension lifecycle.

### Fathom Enrichment Gap Between Sync and Poll

**Problem:** 116 person profiles had transcripts in their `transcripts/` directory but were still stub files with empty Quick Reference fields. The auto-enrichment that should fire when a new transcript arrives wasn't running.

**Root cause:** Two LaunchD jobs were racing. `com.sereno.fathom-sync` ran daily, calling `sync_meetings()`, which stored transcripts and created stub profiles but did NOT write `data/pending_enrichments.json`. `com.sereno.fathom-poll` ran every 30 minutes, calling `poll_for_new()`, which DID write the pending file and trigger the Claude enrichment CLI. Because fathom-sync nearly always won the race (running when fathom-poll was asleep), meeting IDs were added to `processed_fathom_ids.json` before the poll saw them, and the poll then reported "no new meetings" and skipped enrichment. The transcripts piled up on disk without anyone reading them.

**Solution:** Extracted the pending-file write into a shared `queue_enrichments(people, meetings)` helper with merge-safe semantics (unions with any existing pending file so in-flight sync and poll runs don't clobber each other). Called it from both `sync_meetings()` and `poll_for_new()`. Added a `--reconcile` CLI flag that scans `people/*/profile.md` for stub profiles (short file, empty Relationship field) that have transcripts, and queues them for enrichment in a single merged batch, flushing the backlog.

**Debugging approach:** Compared mtimes on the stub profile, the transcript file, and the processed-IDs file to reconstruct timing. Read both launchd logs to confirm fathom-poll was running cleanly but always found "no new meetings". Grepped `fathom_sync.py` for writes to `PENDING_ENRICHMENTS_FILE` and confirmed only `poll_for_new()` called them.

### Unicode Slug Collisions During Deduplication

**Problem:** Fernando's repo had accumulated three variants of the same person: `eduardo-manchón/` (4 transcripts), `eduardomanchon/` (1 transcript), and `edu-manchon/` (identical 5 transcripts to the first two combined). Similar pairs existed for Dan Guenther, Diego Merchán, Edward Eichstetter, Enrique Muñoz Torres, Christian Wagner, Elias Mora, Joaquín Cuenca, and Tomás Kabbabe. A naive merge based on ASCII-normalizing slugs would collide `eduardo-manchón → eduardo-manchon` with `eduardomanchon` and overwrite one.

**Root cause:** `slugify()` used `re.sub(r"[^\w\s-]", "", text)`, where Python's `\w` matches Unicode word characters by default. Accented characters were preserved in slugs. The Fathom API returned names in various casings and accentings across calls, and person-matching via filesystem lookup failed to recognize `eduardo-manchón` and `eduardomanchon` as the same person.

**Solution:** Fixed `slugify()` to strip combining marks via `unicodedata.normalize('NFKD', text)` plus a filter for `unicodedata.combining(c)`, so all future slugs are pure ASCII. For the existing backlog, audited each dup pair individually: picked the canonical full-name form, unioned transcripts into the canonical directory, merged profile content from both sides (preserving unique phone numbers, LinkedIn details, family info, and interaction nuances), and deleted the redundant directories. Normalized 42 transcript filenames (which are generated via the same `slugify()` at store time) in a single pass. Updated `feedback_email_handle_mapping.md` memory with the new canonical-dir mappings so future scan-email runs route correctly.

**Debugging approach:** Scripted the audit using `unicodedata.combining()` to flag non-ASCII dirs, compared transcript sets and profile line counts across each pair to pick canonicals, and used `diff -q` to detect whether colliding filenames had identical content before deleting duplicates.

## Architecture

Key technical decisions:

- **Skills as markdown** -- Each pipeline step is a SKILL.md file with instructions for Claude Code, not executable code. This keeps the system flexible and easy to modify without deployments.
- **Flat file state** -- People profiles, action items, and runtime state all live as markdown/JSON files in the repo. No database. Git provides version history and collaboration.
- **Two-stage Fathom polling** -- Cheap Python API check every 30 min (no LLM needed), expensive Claude enrichment only when new transcripts arrive. PID-based lock file prevents concurrent enrichment.
- **HTMX for interactivity** -- Dashboard uses server-rendered HTML with HTMX for partial updates (mark-obsolete, filter changes), avoiding a separate frontend framework.
- **Parallel background agents** -- For batch transcript processing, 5 agents run concurrently on non-overlapping transcript sets, writing to temp files. This avoids edit conflicts and maximizes throughput.
- **Content-hash obsolete tracking** -- Rather than editing profile files to mark commitments as obsolete (which would violate the "never overwrite" rule), a separate JSON file tracks dismissed items by MD5 hash.
- **DOM-guarded Chrome extension build** -- Rather than caching "already built" in a closure flag, the extension checks for `#sereno-root` in the live DOM on every `show()` call. This makes the bar resilient to Gmail's SPA tearing out extension elements during view transitions without needing to observe those mutations directly.
- **Merge-safe pending-enrichments write** -- `queue_enrichments()` unions its input with any existing `pending_enrichments.json` (dedup by meeting ID and person dir) rather than overwriting. This lets the daily sync and the 30-min poll both enqueue work without racing each other, and lets a manual `--reconcile` run fold a backlog into whatever the poll is about to process.
- **ASCII-normalized slugs** -- `slugify()` strips Unicode combining marks via `NFKD` decomposition before filtering to `[a-z0-9-]`. This keeps all filesystem paths in ASCII, avoids NFC/NFD normalization quirks on APFS, and makes handle-vs-name deduplication tractable since both variants now normalize to the same slug.
