---
title: Team Techito
slug: team-techito
description: A hospitality automation platform that manages cleaning schedules, SMS crew notifications, receipt processing, pet guest alerts, trash reminders, and reservation monitoring for short-term rental properties — powered by Flask, PostgreSQL, Hospitable API, Twilio, Gmail API, and OpenAI Vision.
date_started: 2025-07-23
date_completed: 2026-04-07
active_hours: 30
sessions: 19
total_prompts: 120
tech_stack:
  - Flask
  - PostgreSQL
  - SQLAlchemy
  - APScheduler
  - Twilio (SMS)
  - Gmail API (OAuth2)
  - Hospitable API
  - OpenAI GPT-4 Vision
  - Google OAuth2
  - Jinja2
  - Docker
  - Render.com
platform: Web
lines_of_code: 22452
files: 3519
commits: 169
status: in-progress
hidden: false
cover_image: /images/projects/team-techito/cover.png
screenshots:
  - path: /images/projects/team-techito/cleaning-schedule.png
    alt: Cleaning schedule with late checkout times displayed in red
  - path: /images/projects/team-techito/admin-dashboard.png
    alt: Admin dashboard with crew management and schedule controls
tags:
  - automation
  - hospitality
  - property-management
  - flask
  - api-integration
---

## Summary

Team Techito is a hospitality automation platform built to manage three short-term rental properties in Colorado. It consolidates cleaning schedule generation, SMS crew notifications, automated receipt processing, pet guest welcome messages, trash day reminders, and reservation monitoring into a single Flask application backed by PostgreSQL. The system integrates with the Hospitable API for reservation data, Twilio for SMS, Gmail API for email notifications, and OpenAI Vision for receipt parsing — all running on Render.com with APScheduler handling background jobs.

## Features

- **Automated cleaning schedules** — Generates per-crew HTML schedules from Hospitable reservations, with same-day turnaround detection, manual overrides, and late checkout time highlighting in red
- **SMS crew notifications** — Sends daily SMS messages to cleaning crews at 5 PM MT via Twilio with their upcoming cleanings and schedule links
- **Receipt processing** — Polls Gmail for receipt emails, extracts data with OpenAI GPT-4 Vision, and tracks expenses
- **Pet guest notifications** — Automatically sends welcome messages to guests arriving with pets, with deduplication to prevent repeat messages
- **Trash day reminders** — Configurable SMS reminders for trash collection days, per-property with custom schedules
- **Reservation alerts** — Hourly monitoring of specific properties for bookings by a watchlist of guest names, with email alerts including Hospitable deep links
- **Manual cleaning overrides** — Admin interface to add one-off cleanings with custom times, merged into auto-generated schedules
- **Late checkout display** — Detects non-standard checkout times from the Hospitable API and displays them in red on crew schedules and in SMS messages
- **Google OAuth admin** — Secured admin dashboard with crew management, property assignments, and manual trigger endpoints
- **Activity tracking** — Logs all automated actions (SMS sent, emails processed, webhooks received) with a daily summary report
- **New property auto-detection** — Periodically checks Hospitable for new properties and alerts when one appears
- **Dry-run testing** — Manual trigger endpoints with dry_run and test_name parameters for safely verifying alert logic in production

## How It Was Built

The project started as a consolidation of four separate microservices (cleaning scheduler, file converter, Hospitable integration, and Zapier webhook handler) into a single Flask application. The initial build focused on getting cleaning schedules generated from Hospitable reservation data and published as static HTML pages accessible via unique crew URLs.

From there, features were layered on incrementally: SMS notifications via Twilio, receipt processing with OpenAI Vision, pet welcome messages, trash reminders, and most recently, a reservation watchlist alert system. Each module follows a one-concern-per-file pattern under `src/modules/`, with APScheduler managing all background jobs. A recurring theme was debugging external API integrations — Hospitable's v2 API, Gmail OAuth token refresh, Twilio delivery, and OpenAI rate limits each required careful error handling and retry logic. The latest session added late checkout time detection (extracting times from Hospitable's `check_out` datetime field) and a full reservation alert pipeline with email delivery, DB-backed deduplication, and production-safe testing via dry_run mode.

## Prompts

Key requests that drove the build, in order:

1. **Consolidate services** — "Merge the four microservices into a single Flask app with shared scheduling"
2. **Cleaning schedules** — "Generate per-crew cleaning schedules from Hospitable reservations with same-day turnaround detection"
3. **SMS notifications** — "Send daily SMS to cleaning crews with their upcoming cleanings via Twilio"
4. **Admin dashboard** — "Add a Google OAuth admin interface with crew management"
5. **Receipt processing** — "Poll Gmail for receipts and extract data with OpenAI Vision"
6. **Pet notifications** — "Send automated welcome messages to guests arriving with pets"
7. **Trash reminders** — "Add configurable trash day SMS reminders per property"
8. **Manual overrides** — "Let me add manual cleaning entries with custom times that merge into the auto schedule"
9. **Late checkout display** — "Find reservations where checkout time differs from 10am and show that time in red on the schedule"
10. **Reservation alerts** — "Monitor the Trailridge property for bookings by specific guests and email me an alert within 1 hour"
11. **Testing infrastructure** — "Add dry_run and test_name params so we can test the alert pipeline with a current guest"
12. **Debug alert pipeline** — "The live run returns 0 alerts even though the match works — add diagnostics to find the failure"

## Raw Prompts

Substantive user messages from the conversation, preserving original wording:

> "if there's a manual override for the cleaning schedule, that shows up with a specific time in parenthesis. i also want you to find hospitable reservations in which the checkout time is different from 10am. if you find something that is later, please have that special checkout time also show up in parenthesis (and in red) on the cleaning schedule."

> "if any of the people below ever book the property in Trailridge (Lafayette), please send me an email saying: 'ALERT: Austin Running Club just booked the Beechen home' and the name of the guest, dates of the reservation, etc. This email should arrive at most 1 hour after the reservation is sent, so we need either a webhook or a cron job"

> "let's also add the link to hospitable to the reservation directly"

> "let's test both with dry run and manual run with current guest"

> "more than that, how exactly should we test something that should happen very rarely, almost never?"

> "can you give me the exact commands i need to run, including escape characters?"

## Technical Challenges

### Discovering Hospitable API checkout time fields

**Problem:** The cleaning schedule needed to show late checkout times, but the existing code only stored checkout dates (YYYY-MM-DD), discarding any time information.

**Root cause:** The Hospitable API v2 returns two different date fields: `departure_date` (date only, at midnight) and `check_out` (full datetime with actual checkout time, e.g., `2026-05-10T12:00:00-06:00`). The existing code used `departure_date` and truncated to 10 characters, losing time info entirely.

**Solution:** Wrote a debug script to call the API and inspect raw reservation fields. Discovered the `check_out` field contains the actual checkout time. Also found that default checkout times vary by property (10am vs 11am), so comparison is against each property's own default rather than a hardcoded value.

**Debugging approach:** Created `scripts/debug/debug_checkout_times.py` to fetch raw API responses, print all field keys, and compare checkout times across all properties and reservations.

### Silent email send failures in reservation alerts

**Problem:** The reservation alert matched a guest name correctly (`matched=True` in diagnostics) but reported 0 alerts sent with empty details.

**Root cause:** Multiple issues compounded: (1) The initial extraction of match processing into a helper function left broken indentation and `continue` statements that are invalid outside a loop, causing a `SyntaxError` on import. (2) Because Render caches the previous working version during failed deploys, the old code kept running — which had a `continue` after email send failure that skipped the `alerts_sent.append()`, making failures invisible.

**Solution:** Rewrote the module with `_process_match()` as a clean function using `return` instead of `continue`, wrapped in a try/except that always reports results — including errors — in the response. Added incremental diagnostics (name parsing, match results, processing errors) to make the pipeline fully transparent.

**Debugging approach:** Added diagnostic fields to the JSON response in stages: first guest names with match status, then raw first/last name parsing, then a try/except wrapper that surfaces any processing error. Each iteration was deployed and tested via the browser console.

## Architecture

Key technical decisions:

- **Hourly cron over webhooks** — Chose APScheduler polling over Zapier webhooks for the reservation alert system. Self-contained, no external dependencies, and meets the 1-hour SLA. The webhook endpoint exists but requires Zapier configuration.
- **Per-property default checkout** — Late checkout detection compares against each property's own default (fetched from the API) rather than hardcoding 10am, since one property has an 11am default.
- **DB-backed deduplication** — `AlertedReservation` model tracks which reservations have triggered alerts, preventing duplicate emails across server restarts and redeployments.
- **`include=guest` API parameter** — The Hospitable v2 `/reservations` endpoint doesn't return guest names by default. Adding `include=guest` to the request returns the full guest object with first/last name.
- **Separate `late_checkout_time` field** — Used a distinct field name from manual override's `cleaning_time` to keep the two features independent in both the data pipeline and template rendering.
- **One module = one concern** — Each feature lives in its own file under `src/modules/`, keeping the codebase navigable as it grows.
