---
title: TuneSpace
slug: tunespace
description: A personal music library manager and explorer that combines background data pipelines (YouTube search, yt-dlp, Moises integration, multi-API metadata enrichment) with a visually striking dark-themed frontend for browsing 1,233 songs across 557 artists with instant search, world map visualization, country flags, auto-generated lead sheets, and natural language metadata editing powered by Claude Haiku — with 100% metadata coverage across duration, year, genre, language, and artist country.
date_started: 2026-03-29
date_completed: 2026-04-03
active_hours: 40.1
sessions: 12
total_prompts: 208
tech_stack:
  - FastAPI
  - React
  - TypeScript
  - Tailwind CSS
  - PostgreSQL
  - Redis
  - Celery
  - SQLAlchemy
  - Alembic
  - VexFlow
  - Framer Motion
  - Fuse.js
  - Outfit (Google Fonts)
  - Docker
  - Chrome Extension (Manifest V3)
  - yt-dlp
  - Deezer API
  - iTunes Search API
  - MusicBrainz API
  - YouTube Data API
  - Music AI API (Moises)
  - librosa
  - Google Maps API
  - Vitest
  - Playwright
  - Ruff
  - Anthropic Claude API (Haiku)
  - svguitar
  - "@tombatossals/chords-db"
  - Cloudflare Pages
  - Render.com
platform: Web
lines_of_code: 24974
files: 187
commits: 69
status: completed
cover_image: /images/projects/tunespace/cover.png
screenshots:
  - path: /images/projects/tunespace/library-view.png
    alt: Dark glassmorphism library view with album art grid and instant search
  - path: /images/projects/tunespace/lead-sheet.png
    alt: Auto-generated lead sheet with Ranchers chord font over rhythm slashes
  - path: /images/projects/tunespace/chord-editor.png
    alt: Full-screen chord editor modal with beat grid and autocomplete
  - path: /images/projects/tunespace/song-detail.png
    alt: Song detail modal with album art editor and audio feature bars
  - path: /images/projects/tunespace/world-map.png
    alt: Interactive Google Maps world map with dark theme and clickable bubble markers sized by song count
  - path: /images/projects/tunespace/artist-page.png
    alt: Artist detail page with high-res photo, country badges, and full song list
  - path: /images/projects/tunespace/sidebar-browse.png
    alt: Sidebar navigation with collapsible genre, country, language, and decade sections
tags:
  - music
  - full-stack
  - react
  - python
  - data-pipeline
  - visualization
  - chrome-extension
  - personal-tool
---

## Summary

A full-stack music library manager built entirely with Claude Code across ten sessions (~37 active hours), designed to handle a personal collection of 1,233 songs across 557 artists. The system automates song addition (YouTube search, yt-dlp download, Moises upload), enriches metadata from Deezer, MusicBrainz, iTunes, and librosa audio analysis, and renders auto-generated lead sheets with VexFlow. Features include natural language metadata editing powered by Claude Haiku, full playlist management with drag-and-drop reordering, an artists gallery with multi-source photo search and BiRefNet background removal, sidebar navigation by genre/country/language/decade, an interactive Google Maps world map with dark theme, play tracking, drag-and-drop album art uploads, a random mix generator with filter-aware multi-key selection, and a chord editor with drag-and-drop repositioning, annotations, and auto-scroll for live performance. A Chrome extension handles library sync, setlist import, and bulk chord extraction running in a background service worker. Metadata coverage is 100% across all fields thanks to a multi-API backfill pipeline. 69 commits, 25,000+ lines of code, 200+ substantive prompts. Deployed live via Cloudflare Pages (frontend) + Render (backend + PostgreSQL).

## Features

- **Song addition pipeline**: search YouTube by text, download via yt-dlp, upload to Moises, enrich metadata — all in Celery background workers
- **Instant search**: client-side fuzzy matching across all metadata fields (title, artist, genre, country, language, tags) using Fuse.js with tuned threshold, backed by PostgreSQL pg_trgm indexes
- **Chord progression search**: find songs by chord sequence (e.g., Am → F → C → G). Precomputed chord_sequence column with GIN trigram index for fast substring matching. Pill-based input with autocomplete, preset progressions (I–V–vi–IV, ii–V–I), 1,232 songs indexed
- **Code splitting**: React.lazy + Suspense for all 12 page routes — initial bundle reduced from 1.5MB to 361KB (76% reduction)
- **Keyboard shortcuts**: / to focus search, Escape to close modals, Space to pause/resume auto-scroll
- **Rich metadata per song**: key, BPM, time signature, energy, danceability, valence (mood), acousticness, album art, genres, languages, artist origin countries, tags
- **Auto-generated lead sheets**: Moises ML chord extraction → quantized to measures → rendered as interactive sheet music with VexFlow (Ranchers font for chords, Georgia for text) → exportable as PDF
- **Chord editor**: full-screen modal with beat-level editing grid, chord autocomplete, Tab navigation, drag-and-drop chord repositioning, right-click context menu (silence rests, colored dot annotations from a 6-color pastel palette, free-text annotations like "bar" or "harmonics at 12th", measure copy/paste with flash feedback, measure deletion), measure selection, copy/paste chord groups, and a "Golden" badge system for verified-correct sheets
- **Natural language editing**: type instructions like "this song is in Spanish" or "the artist is from Chile" — Claude Haiku interprets them into structured field edits via tool_use, shows a preview diff, and applies changes through existing PATCH endpoints. Works for both songs and artists simultaneously in a single instruction
- **Metadata editor**: manual save with sticky save bar, autocomplete for genres/languages/tags/key, smart propagation to same-artist songs, full audit trail (accessible via "Switch to manual editor" from the NL editor)
- **3D card flip animation**: hovering the album art in the song detail modal flips it in 3D to reveal artist photos with spring physics (stiffness/damping/mass), specular shine at 90° (edge-on), and scale pulse. Multi-artist songs cycle every 3 seconds with continuous flips, swapping hidden face content mid-rotation. Armed gate prevents flip on modal open when cursor is already over the art
- **Album art management**: Deezer search, iTunes fallback, manual URL paste, copy from same-artist thumbnails, retry button, drag-and-drop image upload on the entire song modal — editing gated behind edit mode button
- **Playlists**: full CRUD, drag-to-reorder in sidebar, inline rename, add/remove songs from playlist view and song detail, album art mosaic header, Moises setlist import with "Artist - Title" parsing
- **Artists gallery**: browsable /artists page with grid/list views, sort by song count or country, country grouping with headers, song count badges over photos, scroll-to-top button
- **Artist photo management**: dedicated /artists/photos page for bulk photo management — search online across 6 free sources (TheAudioDB, Wikimedia Commons, Deezer, Spotify, Wikipedia, Wikidata), paste URL, batch background removal with BiRefNet (birefnet-general, birefnet-portrait, birefnet-massive) and white-bg compositing
- **Album pages**: clickable album names navigate to a dedicated page with canonical cover art, artist name(s), year (earliest from songs), and a song list — all filtered client-side from the cached song data
- **Artist pages**: dedicated route with high-res artist photo (drag-and-drop image upload or URL paste), country editing with autocomplete, full song list
- **Sidebar navigation by dimension**: collapsible browse-by sections for genres, countries, languages, decades — each with song counts and "show all" expansion. Clicking filters the library via URL params
- **Interactive world map**: Google Maps API with custom dark theme (region labels only), clickable bubble markers sized by song count, hover tooltips, legend. Clicking a country navigates to the filtered library
- **Random mix with filters**: genre, language, country, decade dropdowns plus collapsible multi-key selection (toggle pills, select/deselect all) with server-side filtering for the random playlist generator
- **Play tracking**: counts clicks to Moises player, visible in list view and artist pages
- **Advanced filters**: sliders for BPM range, year range, energy, danceability; dropdowns for key, genre, language, country, decade
- **Grid/list toggle**: album art cards with ambient glow or compact table view, toggle positioned next to sort controls
- **Auto-scroll lead sheet**: BPM-synchronized auto-scroll with beat-based countdown (configurable 1-8 beats), gradient tracking dot with trail animation that follows playback position beat-by-beat using VexFlow note coordinates, spacebar pause/resume, bottom-left elapsed timer, progress bar, crossfade line transitions, trail fades on pause
- **Golden Set playlist**: auto-populated virtual playlist of all verified lead sheets, ordered by verification date, draggable position in sidebar persisted via localStorage
- **Chord annotations**: right-click any beat to add colored dots (6-pastel palette, rendered as superscript in the lead sheet) or free-text annotations (rendered in italic below the chord), with drag-and-drop preserving annotations
- **Measure timestamps**: each line in both the lead sheet and chord editor shows the song timestamp (mm:ss) for when that measure starts, calculated from BPM
- **Guitar chord diagrams**: interactive SVG fretboard diagrams on lead sheets using svguitar + @tombatossals/chords-db (2,069 voicings across 756 chord types). Edit mode shows toggleable chord pills, left/right voicing cycling, and side-by-side variation comparison. Chord name parser handles Unicode ♭/♯, slash chords, and all common notations. Selections persist in the chords JSONB and render above the staff
- **PDF export**: download button on lead sheets using browser print for vector-quality output
- **Chrome extension**: Sync Library with delta detection, Import Setlists with scan-then-select, Grab Chords from player, Bulk Extract Chords (background service worker — survives popup close and tab switching)
- **Bulk chord extraction**: background service worker opens each song in a tab, polls for chord data with configurable patience (34s total per song), uses chord_complex_pop for accuracy (preserves m7b5 vs dim)
- **Deduplication**: fuzzy matching on title + artist to prevent duplicate imports
- **Song archive system**: soft-delete songs to an archive page with one-click restore, keeping the library clean without losing data
- **Chord revert**: chords_previous stored before re-import, "Revert chords" button in song detail, verified (golden) chords never overwritten
- **Multi-artist handling**: comma-separated artist names split into individual artists, ft./feat. parsed from titles and linked as featured artists, artist counts include all appearances
- **Unicode normalization**: NFC normalization on all imports prevents decomposed-accent duplicates (e.g., é vs e+combining accent)
- **Production deployment**: Cloudflare Pages (frontend CDN) + Render.com (backend + PostgreSQL). Password-protected via server-side Bearer token validation — password never baked into the JS bundle. Read-only mode strips Celery/Redis for minimal infrastructure cost (free tier)
- **Hidden audio features**: energy, danceability, and mood bars hidden from the UI until data quality from librosa extraction improves — fields still stored for future use
- **Country flag emojis**: shared utility converting country names to ISO 3166-1 alpha-2 codes → Regional Indicator Symbol flag emoji. Shown in world map country table and artist page group headers
- **Metadata backfill pipeline**: MusicBrainz artist search + ISO subdivision code handling, Deezer/iTunes track lookup, language inference from artist countries. Automated audit script checks all songs for gaps and inconsistencies
- **100% metadata coverage**: every song has duration, year, decade, genre, language; every artist has a country — achieved through automated backfill + manual curation
- **Scroll position restoration**: map view saves scroll position before navigation, restores it on back button (fixed React StrictMode double-mount bug that cleared the restore timer)

## How It Was Built

The project started as a deep architectural planning session. Before writing any code, Claude interviewed me across three rounds of questions — covering data sources, scale requirements, metadata needs, background processing, frontend vision, deployment, and design inspiration. I shared visual references that established the dark glassmorphism aesthetic.

A critical early discovery was that Moises.ai (rebranded as Music AI for developers) has a robust audio processing API but no endpoint for library management. This shaped the entire architecture: a Chrome extension for initial library import, with the API handling uploads and chord extraction for new songs.

The build progressed through distinct phases in a single marathon session: (1) full project scaffold with 38 files, (2) Chrome extension for Moises library import — debugging virtual scroll to capture all 1,300 songs, (3) metadata enrichment pipeline — initially targeting Spotify, but pivoting to Deezer after a 23-hour rate limit lockout, adding iTunes as fallback, then building librosa-based local audio feature extraction when Spotify deprecated their audio features endpoint, (4) frontend polish with instant search, metadata editing with smart propagation, album art management, (5) lead sheet rendering — iterating through three chord fonts (Gong → Yatra One → Ranchers), building a full chord editor, and implementing a "Golden" badge system for verified sheets, and (6) Chrome extension chord grabber — a multi-iteration debugging saga to intercept Moises's ML chord data from CDN requests inside cross-origin iframes.

The MP3 matching phase was particularly interesting: 1,226 MP3 files spread across three directories (main, Done, extras with artist subdirectories) needed to be matched to database entries using Unicode normalization, accent stripping, track number removal, and bidirectional containment matching.

After the core was solid, a second wave added 16 features in parallel using multiple Claude Code agents: playlists with Moises setlist import, artist pages with clickable names throughout the app, sidebar navigation by dimension (genre/country/language/decade with song counts), an interactive Leaflet world map replacing react-simple-maps, play tracking, random mix filters, auto-saving metadata editor, chord copy/paste for the lead sheet editor, PDF export, and a bulk chord extraction system that processes the entire library unattended. The Chrome extension grew from a single "Extract Library" button to four features: Sync Library (with delta detection), Import Setlists, Grab Chords, and Bulk Extract Chords. The codebase nearly doubled from 9,100 to 15,200+ lines in this phase.

## Prompts

Key requests that drove the build, in order:

1. **Project setup** — Triggered /setup-repo on an empty directory called "tunespace"
2. **Core concept** — Described the combination of background processes, data fetchers, database, and frontend for exploring 50K-song music libraries
3. **Data source and scale** — Specified Moises.ai as starting point with 1,300 songs, wanting YouTube → yt-dlp → Moises → metadata enrichment pipeline
4. **Frontend vision** — Requested visually striking experience with high-res album art, glassy effects, world map, instant search, sliders, filters
5. **Moises integration** — Confirmed no official API, open to Chrome extension approach
6. **Lead sheet generation** — Added auto-generated music sheets from extracted chords, with reference PDF
7. **Chrome extension debugging** — Extension only importing 100 of 1,300 songs; virtual scroll not loading enough rows
8. **Metadata enrichment** — Requested album art, genres, years from Deezer after Spotify lockout
9. **MP3 matching** — Pointed to three directories of local MP3s for librosa audio feature extraction
10. **Album art management** — Requested retry button, manual URL paste, copy from same-artist thumbnails
11. **Metadata editor** — Full inline editing with autocomplete, propagation to same-artist songs, audit trail
12. **Lead sheet polish** — Complained about dark background, inline rendering; requested new-tab viewer with white background per UX guidelines
13. **Chord font iteration** — Tried Gong → Yatra One → Ranchers (Google Fonts), adjusting letter-spacing
14. **Moises chord data** — Proposed grabbing ML chord data from Moises CDN instead of open-source extraction
15. **Extension chord grabber** — Multiple debug rounds: script injection → Performance API → cross-frame discovery (player runs in iframe at studio1.moises.ai)
16. **Chord editor** — Requested beat-level editing grid for manual corrections and overrides
17. **Golden badge system** — "I want to add a concept of 'golden' to songs I know are 100% correct"
18. **Golden toggle fix** — Reported button state not updating after toggle; fixed with optimistic UI
19. **Feature batch request** — Detailed list of 16 features across large/medium/small categories, with plan to step away while Claude works
20. **Sidebar navigation** — Browse library by genre, country, language, decade in collapsible sidebar sections
21. **Playlists + setlist import** — Support playlists, import existing Moises setlists via Chrome extension
22. **Interactive map** — Replace react-simple-maps with Google Maps (settled on Leaflet for free, no API key)
23. **Artist pages** — Clickable artist names everywhere, dedicated artist page with photo and songs
24. **Auto-save metadata** — Remove explicit save button, auto-save with debounce, cancel reverts all changes
25. **Bulk chord extraction** — Faster way to extract chords in bulk for the full library
26. **Song archive** — Archive/ignore songs with restore from a dedicated /archive page
27. **Playlist import selector** — Playlist importer should scan first, show checkboxes, then import only selected playlists
28. **Hide audio features** — Hide energy/danceability/mood from UI until data quality improves
29. **Multi-genre songs** — Songs can have multiple genres
30. **Archive button placement** — Move archive button to top right alongside Edit and Close
31. **Map fix** — Fix react-leaflet v5 crash on React 18 ("render2 is not a function")
32. **Crown icon styling** — Crown icon in lead sheet should match album art accent color
33. **Show all countries in map table** — Map view was only showing top 20 countries, should show all
34. **Scroll restoration on back navigation** — Clicking a country below the fold, then pressing back, should restore scroll position
35. **Playwright-verified scroll fix** — Previous scroll fix didn't work; asked to use Playwright to test and verify
36. **Country flag emojis** — Add flag icons next to country names in map table and artist page group headers
37. **Fix wrong artist countries** — Kurt, Timo, Ragazzi, Lagos all had wrong countries from MusicBrainz
38. **Artist country backfill** — Check for missing countries and write a job to backfill from MusicBrainz
39. **Metadata audit script** — Write a script to double-check ALL data (countries, years, decades, genres, duration)
40. **Missing metadata report** — Generate a text file with all gaps so user can manually provide data
41. **Merge duplicate artists** — Merge "Gianmarco" into "Gian Marco"
42. **Fix swapped artist/title** — "Crush Company" was the artist, not the song title
43. **Milestone tag** — Commit, document, and create a special build marker at the 24-hour point
44. **Artist data report** — Generate a txt file with artist: country, genres for external audit
45. **Fix archive page crash** — Archive view failing with "songs.map is not a function"
46. **External data audit** — Applied 15 country corrections, 11 genre corrections, 7 duplicate merges, genre taxonomy cleanup from external audit tool
47. **Natural language editing** — Explored using Claude Code CLI headlessly for DB edits; pivoted to Claude API with tool_use for a clean interpret → preview → apply architecture
48. **Combined song + artist NL editing** — After testing, noticed artist edits weren't possible from the song context; requested combined mode
49. **Modal scroll fix** — Reported persistent phantom scroll on the song detail modal after editing
50. **Drag-and-drop album art** — Requested drag-and-drop image upload on the song modal, matching the existing artist page pattern
51. **Genre rename** — Asked to rename "Singer-Songwriter" to "Singer Songwriter" across all songs
52. **Clickable albums** — Requested album names be clickable, linking to a dedicated album page with canonical cover art and list view
53. **Album year** — Asked to show the album year derived from the earliest song year
54. **Production deployment** — Requested deploying to Cloudflare Pages + Render with password protection (shared password "tunespace")
55. **Database migration** — Imported 8.8 MB pg_dump to Render's managed PostgreSQL
56. **Fix asyncpg dialect** — Render provides `postgresql://`, app needs `postgresql+asyncpg://`
57. **Fix broken thumbnails** — Locally uploaded artist photos needed to be committed and DB URLs made absolute
58. **Security hardening** — Removed hardcoded password from JS bundle, validate server-side instead
59. **Password and localhost** — Changed deployment password to "venezuela", made password gate auto-skip on localhost
60. **Drag-and-drop chords** — Chords often land one beat off from ML extraction; requested drag-and-drop to reposition them
61. **Right-click context menu** — Add silence symbol and future actions via right-click on beat cells
62. **Proper rest rendering** — Silence symbol should render as a VexFlow quarter rest, not a unicode box
63. **Auto-scroll for live performance** — BPM-aware auto-scroll with countdown, smooth scrolling, progress bar — "ultra smart" so the right line is always centered
64. **Note spacing and timestamps** — Notes should fill full measure width; add timestamps below measure numbers
65. **Chord annotations** — Right-click to add colored dots (6-pastel palette) and free-text annotations like "bar" or "harmonics at 12th"
66. **Save button always visible** — Save button disappeared when no changes; should always be present
67. **Golden toggle in save flow** — Toggling golden didn't activate the Save button; unified into the save flow
68. **Measure copy/paste/delete** — Right-click menu actions for copying, pasting, and deleting entire measures with flash feedback
69. **Chord-to-staff spacing** — Moved chord rendering from VexFlow Annotations to raw SVG for pixel-precise vertical positioning
70. **Artist photo search** — Build multi-source image search (TheAudioDB, Wikimedia, Deezer, Spotify, Wikipedia, Wikidata) for artist photos
71. **Background removal** — BiRefNet-powered background removal for artist photos, with white-bg compositing and batch processing
72. **Artist photo page** — Dedicated /artists/photos page for bulk photo management with search, paste URL, batch background removal
73. **Memory optimization** — Background removal using 28GB RAM; reduced to ~300MB by switching from in-process to subprocess execution
74. **Random mix keys** — Multi-key selection filter with toggle pills and select/deselect all for the random playlist generator
75. **Scroll-to-top on artists** — Artists page should have the same scroll-to-top button as the Library
76. **Collapsible keys panel** — Make keys a collapsible panel in random mix, collapsed by default
77. **Google Maps migration** — Replace Leaflet with Google Maps API for the world map, with dark theme and region-only labels
78. **Map label contrast** — Make country labels brighter against the dark map background
79. **Deploy to Cloudflare** — Push all changes to Cloudflare Pages and the online instance
80. **Guitar chord diagrams** — Requested fretboard diagrams on lead sheets with X/O string indicators, voicing selector in edit mode, and variation cycling
81. **Chord diagram positioning** — Moved selector below Lead Sheet Notes, not above everything
82. **Unicode flat in chord parser** — D#m7♭5 wasn't recognized; parser needed to normalize ♭/♯ to ASCII
83. **Accidental kerning fix** — Flat/sharp spacing too wide in chord names; kerning logic was running before chord text was rendered
84. **Kerning tuning** — Adjusted kerning multipliers from 0.75/0.70 → 0.60/0.45 after two iterations (too tight, then just right)
85. **Fix garbled keys** — "Daj" displayed in key field; fixed 27 rows in both local and production DB

## Raw Prompts

Substantive user messages from the conversation, preserving original wording:

> "it's a combination of background processes, data fetchers and cleaners, database and front end to explore music libraries of up to 50K songs at a time."

> "I have a starting point of a library in my account on moises.ai of around 1300 songs. I want that to be the starting point. At the same time, I want a search experience to add new songs that behind the scenes: searches youtube for a match, uses yt-dlp to get the mp3, uploads it to my moises account, adds it to my library, and then populates all the metadata"

> "A far more visually rich experience to start with, where the graphic design stands out as creative and incredibly striking (high-res album art, maybe cool glassy effects, map of the world to visualize where songs are from, etc), while the interaction design feels truly delightful: pressing on buttons, using sliders, providing proper filters for everything, etc. Search should feel INSTANT."

> "i use moises on web, desktop, and ipad. last time i looked into this, they didn't have an official api. please research this, and if you can't find one, i'm happy to use a chrome extension or some other route to fetch the song list from moises"

> "every time we process a song with moises and split it into stems, we're likely getting the extracted chords. I want to build music sheet for each of the songs I add, with this attached one as a solid reference"

> "the lead sheet renders poorly...dark background...did you use those UX guidelines?"

> "I want to improve the fonts...eliminate the vertical bar...should be very similar to this one"

> "the chords seem wrong on cuando vuelvas...they should start: C G F G Am Dm7 C/E F G C"

> "within the 'Done' directory in Desktop/Music/mp3s there's more mp3s"

> "for the mp3s, i added a new folder to Desktop/Music/mp3s/extras"

> "I would like to add a concept of 'golden' to songs I know are 100% correct"

> "In the editor, after tapping the mark golden button (or tapping it again to remove something from being golden) doesn't update the state of the button within the editor. please fix that"

> "I need to be able to easily browse my music with the various dimensions I have: by country, by language, by genre, by decade, etc. Please use the left-sidebar to give me ways to navigate my library this way"

> "i want to be able to support playlists, so any of the songs I have can be a part of none, one or multiple playlists. i have some playlists in moises that i would like to import. please add this functionality to the chrome extension."

> "make the world map a lot more interactive in terms of being able to click on specific places in the map (please render it using google maps API so I can smoothly scroll around - use the most basic map without too many geographic labels/features)"

> "when i edit a song in the library (e.g. change its year), i would like for it to auto-save, not for me to explicitly click on the save button. if/when something gets auto-saved, there should still be a way for me to cancel from the edit window, and all the edits i made should revert"

> "help me understand a better/faster way for chord extraction, perhaps in bulk, given what you know now."

> "Archive/ignore songs with restore from /archive page"

> "Playlist importer should scan first, show checkboxes, then import selected"

> "Hide energy/danceability/mood from UI — don't trust the data yet"

> "Songs can have multiple genres"

> "Move archive button to top right with Edit and Close"

> "Fix map — react-leaflet v5 incompatible with React 18"

> "Crown icon in lead sheet should match album art"

> "in the world map, can you make sure all countries are shown in the table below the map?"

> "if i click on a country below the fold in the map and then the back button, i'm taken to the map view scrolled all the way to the top. is there a way to save state so it knows i had scrolled past the map view?"

> "the scroll is still not working. do you have a way of using chromium, playwright or something else to test it yourself and implement a verified fix?"

> "kurt, timo, ragazzim lagos all have the wrong countries, please fix"

> "are there songs or artists for which you don't have a country? if so, can you please write a job to backfill that?"

> "Can you write a new script that double checks ALL information you have so far in the database? i want to make sure that countries, years, decades, genres are all accurate. Please make sure we also store song duration and show it on the list view"

> "Can you give me a text file with the songs and artists for which you're missing information, so I can manually provide it to you?"

> "Can you also merge Gianmarco with Gian Marco as the artist?"

> "for crush company, you have the artist and song title mixed. Crush Company is the artist, and they're from the United States. Please update that"

> "Can you commit, document, and also mark this as a special build in the commit history? I'm around 24 hours into this project, and I want to make sure in the future I can spin off an instance that has that exact feature set"

> "all of these show as japan. that seems incorrect. please fix by using web search and finding the right countries"

> "other that the first 3, the other 3 are wrong in country (not germany). please fix"

> "can you generate a report in a txt file with: artist: country, list of genres? I want to run it by another software to spot data issues"

> [Pasted full external audit report with 15 country corrections, 11 genre corrections, 7 duplicate merges, and taxonomy cleanup rules]

> "i want to understand if it's possible to enable a song and artist edit flow in which i specify with natural language what i want to change, and it does it, for instance: 'this song's language should be spanish' or 'this artist is from venezuela' and tunespace should call an LLM, ideally claude, and ideally claude code without using extra tokens (e.g. by running a headless terminal command claude in skip permissions mode, and issuing it instructions to edit the db directly"

> "can you make sure i can edit artist, too?" [with screenshot showing Claude correctly identified the artist change but couldn't apply it because the song editor only had the song tool]

> "i can still scroll. please test with playwright or others before completing" [with screenshot of phantom scroll on song modal]

> "allow me to add a song art cover by dragging a photo into the edit modal, just like we do for artist photo. the target should be the entire edit modal"

> "can you rename 'singer-songwriter' to 'Singer Songwriter' with a space?"

> "could we make albums clickable, so that all songs from that album are shown? it's fine in that case to use the canonical ablum cover at the top, and the rest is a list (no grid view)"

> "can you show the year of the album in the album page by grabbing the smallest year from all the songs?"

> "Would you be able to push out a version of this to Cloudflare's infrastructure, and make the experience password-protected? I also want to share the private github repository with a friend"

> "let's make it read-only for now... whatever is free for very little use... single shared password is fine: tunespace"

> "when I edit a lead sheet, it often has the right chord, but one noe after/before where it should go. allow me to drag and drop chords to the previous note easily"

> "i would like to be able to add a silent note easily. can you allow me to add it by right clicking on a editable cell and displaying a menu of special symbols and actions?"

> "next to the pdf/ edit chord buttons, add an auto-scroll button. this button should be ultra smart, so ultrathink. you know the duration of the song, you know the bpm. so if i play this live, you'll know precisely where in the song i should be at any given time."

> "one of the other actions in addicion to adding the silence symbol is i want to be able to annotate a particular chord or note. I may add stuff like 'bar', a colored dot (pick from a nice slightly bright pastel palette of 6 different colors), 'harmonics at 12th' or something like that"

> "if i go into edit mode and mark something as golden, the button still shows in gray state. same as when i undo a golden. can you please make sure this works well?"

> "please add as an action to copy an entire measure, and to paste an entire measure with the right click menu. when doing so, please briefly highlight with a rounded rectangle the measure i just copied, and also highlight it when i paste it"

> "didn't work. can you ultrathink to make sure your fix is correct?" [about chord-to-staff spacing — VexFlow's setYShift had no effect, leading to raw SVG rendering approach]

> "artist page should have the scroll to top button similar to library"

> "can you make keys a collapsible panel in the random mix page, and make it collapsed by default?"

> "can we try out google maps api instead of what we have right now?"

> "Can you make the map dark, and with fewer label details - i care mostly about regions"

> "can you make the contrast of the map a bit higher by making the labels brighter?"

> "can we push all of these changes to cloudflare and the online instance we have?"

> "i need a nice new feature: i want the lead sheet to be able to show me the guitar chord diagrams (with the fretboard), similar to this...the X's and O's should show at the top...when I enter edit mode, I should be able to see a simple chord selection box that allows me to pick which chords I need diagrams for. by default, none is selected. once I pick a chord i want a diagram for, i should have a way to choose which of the variations i prefer - so there must be a way to cycle through them"

> "on this song, how can i pick for you to show me D#m7♭5? the list of chords I can select should be taken from the actual list of chords, and should be shown below the lead sheet notes box"

> "why does this song show 'Daj' in the key?"

> "the spacing between the 7 the b and the 5 should be much narrower. I thought we had fixed/addressed this in the past"

> "can you make them 0.6/0.45?"

## Technical Challenges

### Spotify Rate Limit Lockout → Full API Pivot

**Problem:** During the initial metadata enrichment pass across 1,300 songs, the Spotify API returned a 429 (Too Many Requests) with a `Retry-After: 84907` header — a 23-hour lockout.

**Root cause:** Spotify's rate limiting is aggressive for new app registrations. Even with 0.3-second delays between requests, the burst of searches for album art, genres, and release years triggered the lockout. Additionally, Spotify's audio features endpoint (energy, danceability, valence) was deprecated for new apps in November 2024, returning 403 errors.

**Solution:** Pivoted the entire enrichment pipeline away from Spotify. Replaced with: (1) Deezer API for album art, genres, and release years — no API key needed, generous rate limits, (2) iTunes Search API as a fallback for missing album art, and (3) librosa for local audio feature extraction (energy, danceability, valence, acousticness) from the user's MP3 collection.

**Debugging approach:** Examined the Retry-After header value, researched Spotify's rate limit policies, and decided the pivot was more reliable than waiting 23 hours and risking another lockout.

### Chrome Extension Chord Grabber: The iframe Discovery

**Problem:** The Chrome extension's "Grab Chords" button was designed to intercept Moises's ML-extracted chord data from CDN requests (d3.moises.ai/v3/download/.../chords.json). It worked in initial testing but consistently failed in production — returning zero chord URLs even when the player was clearly loaded.

**Root cause:** After multiple failed approaches (script tag injection, fetch interception, Performance API in the main frame), the breakthrough was discovering that the Moises player runs inside a cross-origin iframe at `studio1.moises.ai`, not in the main `studio.moises.ai` frame. The Performance API's `getEntriesByType("resource")` only returns entries for the frame it's called in — so scanning the main frame never found the chord URLs.

**Solution:** Used `chrome.scripting.executeScript` with `allFrames: true` to execute the Performance API scan in every frame on the page, including the studio1.moises.ai iframe. The chord.json URLs were immediately visible in the iframe's resource entries. The extension re-fetches the chord data from within the iframe context and sends it to the TuneSpace backend.

**Debugging approach:** Added console logging at every stage. Compared the user's console output (showing "Found 0 chord URLs") with expected behavior. Examined the Moises player DOM and discovered the iframe. Tested `allFrames: true` which immediately found the chord URLs.

### MP3 Matching Across Three Directories with Messy Filenames

**Problem:** 1,226 MP3 files needed to be matched to database entries for librosa audio feature extraction. Files were spread across three directories with inconsistent naming: track numbers ("01 - Waka Waka.mp3"), accented characters ("María María.mp3"), featuring artists in filenames, and artist subdirectories in the extras folder.

**Root cause:** The MP3s came from different sources over years — some from CD rips with track numbers, some from downloads with "Artist - Title" format, some in artist-named subdirectories. No single naming convention.

**Solution:** Built a multi-pass fuzzy matching pipeline: (1) Unicode normalization and accent stripping (NFD decomposition), (2) track number removal via regex, (3) "Artist - Title" splitting, (4) featuring-artist removal ("feat.", "ft.", "con"), (5) bidirectional containment matching (filename contains DB title OR DB title contains filename), (6) recursive directory scanning across all three directories.

**Debugging approach:** Ran matching in stages, logging unmatched files to a text file. The user manually identified edge cases (ChocQuibTown vs Chocquibtown, Monsieur Periné vs Monsieur Perine) which informed additional normalization rules.

### Virtual Scroll Library Scraping

**Problem:** The Chrome extension initially only captured ~100 songs from the Moises Studio library, despite there being 1,300+. The library uses virtual scrolling — only rendering DOM rows for the visible viewport.

**Root cause:** The scraper's scroll-and-capture loop was reaching the bottom detection threshold too quickly. With 500px scroll steps and 300ms pauses, the virtual scroll wasn't rendering new rows fast enough, causing the scraper to conclude it had reached the bottom.

**Solution:** Increased scroll delay to 800ms (giving the virtual scroll time to render), scroll step to 500px, and "stable rounds" threshold to 5 — meaning the scraper needs 5 consecutive scroll attempts with no new rows before declaring the bottom reached. Also tracked captured rows by `data-row-id` attribute to avoid duplicates as rows recycled during scrolling.

**Debugging approach:** Logged row count after each scroll step. Observed the count plateauing at ~100 while the scroll position kept increasing, indicating new rows weren't rendering fast enough.

### Lead Sheet Font Rendering: Clipped Ascenders

**Problem:** After switching to the Gong font for chord symbols, the top portions of tall letters (like "G", "C", "A") were visually clipped in the VexFlow-rendered lead sheet.

**Root cause:** VexFlow positions text annotations relative to the stave with a fixed top margin. The Gong font (and later Ranchers) has unusually tall ascenders compared to standard web fonts, causing the chord text to extend above the allocated space and get clipped by the SVG viewBox.

**Solution:** Increased the `TOP_MARGIN` constant from 20 to 40 pixels, giving the chord annotations enough headroom. Also iterated through three font choices (Gong → Yatra One → Ranchers) based on aesthetic fit, adjusting letter-spacing along the way before settling on Ranchers with normal spacing.

**Debugging approach:** Visual inspection of the rendered SVG. Compared font metrics across the three candidates. The user provided side-by-side feedback after each font change.

### Bulk Chord Extraction at Scale

**Problem:** The chord grabber worked for one song at a time — open the Moises player, wait for the chord data to load in a cross-origin iframe, extract it. But the user had 1,300+ songs needing chord data, and doing it manually was impractical.

**Root cause:** Moises loads chord data lazily when a song's player page opens. The chord data lives at a CDN URL (`d3.moises.ai/.../chords.json`) that's only fetchable from within the player's iframe context. There's no API to request chord data by song ID — it requires an actual browser page load.

**Solution:** Built a "Bulk Extract Chords" button in the Chrome extension that automates the entire flow: (1) fetches the list of songs still missing chords from `GET /api/songs/pending-chords`, (2) opens each song in a background tab at `studio.moises.ai/player2/{moises_id}`, (3) waits 8 seconds for the player iframe to load and fetch chord data, (4) scans all frames via Performance API for `chords.json` URLs, (5) retries once with extra wait if not found, (6) sends extracted data to the backend, (7) closes the tab and moves to the next song. Includes a stop button for cancellation and is fully resumable — restarting skips songs that already have chords.

**Debugging approach:** Timed the player's network requests to determine the 8-second wait. Added retry logic after observing that some songs needed extra load time. The `pending-chords` endpoint ensures no duplicate work across multiple runs.

### Building 16 Features in Parallel with Multiple Agents

**Problem:** The user requested 16 features across backend, frontend, and Chrome extension — then stepped away for 30 minutes. Building them sequentially would take hours.

**Root cause:** Many of the features were independent: playlists (backend model + API + frontend pages), artist pages, sidebar navigation, world map replacement, Chrome extension updates, and metadata editor changes could all be worked on simultaneously without conflicts.

**Solution:** Orchestrated 4 parallel Claude Code agents, each with a specific domain: (1) backend agent built playlist/artist models, migrations, CRUD APIs, facets endpoint, play counter, and random filters, (2) frontend agent built artist pages, sidebar navigation, playlist pages, play tracking, random mix filters, and wired all new API types, (3) Chrome extension agent rewrote the popup with sync, setlist import, and configurable URLs, (4) Leaflet map agent replaced react-simple-maps with dark-themed interactive markers. Meanwhile, the main thread handled small UI fixes (5 items), PDF export, auto-save metadata, and chord copy/paste. All agents completed without file conflicts, and TypeScript compiled cleanly on first check.

**Debugging approach:** Careful task partitioning to avoid file conflicts. Each agent got a complete spec with exact code patterns to follow. Post-completion verification with `tsc --noEmit` confirmed zero type errors across the 15,200-line codebase.

### Scroll Restoration vs React StrictMode Double-Mount

**Problem:** After clicking a country in the world map table (below the fold) and pressing the browser back button, the map page always scrolled to the top instead of restoring the user's previous scroll position.

**Root cause:** The initial fix saved scroll position to `sessionStorage` and restored it in a `useEffect` with a 300ms timer. This worked in theory but failed in practice due to React StrictMode's double-mount behavior in development: (1) component mounts, effect runs, consumes the sessionStorage value and starts a timer, (2) StrictMode immediately unmounts the component, cleanup runs and clears the timer, (3) component re-mounts, effect runs again but the sessionStorage value is already consumed and a ref guard blocks re-entry. The scroll never happens. Additionally, the staggered entrance animations on country buttons (`delay: i * 0.03`) meant items below the fold were still invisible (opacity 0) when the scroll position was restored.

**Solution:** Moved the `sessionStorage.getItem()` call inside the `setTimeout` callback instead of in the effect body. This way, StrictMode's cleanup clears the first timer before it reads sessionStorage, and the second mount creates a new timer that successfully reads the still-intact value. Also disabled browser scroll restoration (`history.scrollRestoration = 'manual'`) and skipped entrance animations when restoring (`initial={false}` on motion components).

**Debugging approach:** Built a Playwright test script that automated the full flow: navigate to /map, scroll down, click a country, press back, measure scrollY. The first run confirmed the bug (expected 532, got 0). Instrumentation revealed `window.scrollTo(0, 532)` worked fine when called manually 2 seconds later — proving it was purely a timing issue. Tracing the StrictMode mount/unmount cycle revealed the root cause.

### Google Maps Migration: mapId vs styles Conflict

**Problem:** After replacing Leaflet with Google Maps API (@vis.gl/react-google-maps), the dark map styles weren't applying — the map rendered in Google's default light theme despite having a comprehensive `DARK_MAP_STYLES` array.

**Root cause:** The `Map` component was configured with both a `mapId` prop (required for `AdvancedMarker`) and a `styles` prop. When `mapId` is present, Google Maps ignores the `styles` prop entirely — styling must be done through Cloud Console's Map Styles instead. The `mapId` was originally added because `AdvancedMarker` (which supports custom HTML content) requires it.

**Solution:** Removed the `mapId` prop so the inline `styles` array would apply. Replaced `AdvancedMarker` with the basic `Marker` component using dynamically-generated SVG data URIs for the bubble icons (creating circles of varying sizes at runtime). Hover tooltips switched from custom HTML overlays to Google Maps `InfoWindow` components. The dark style was refined to show only country labels (hiding provinces, cities, roads, water labels, POIs) with progressively brighter label colors for better contrast.

**Debugging approach:** Compared the rendered map against the expected dark theme. Checked Google Maps documentation which confirmed that `mapId` takes precedence over `styles`. Tested removing `mapId` and verified the dark theme applied immediately.

### BiRefNet Background Removal: 28GB Memory → 300MB

**Problem:** Artist photo background removal was consuming 28GB of RAM, making the process impractical on a development machine.

**Root cause:** The BiRefNet model (state-of-the-art segmentation for hair, fine edges, and group photos) was being loaded in-process by the FastAPI backend. Each invocation loaded the full model weights into memory, and Python's garbage collector wasn't releasing them promptly due to CUDA/MPS tensor references.

**Solution:** Moved background removal to subprocess execution — the FastAPI endpoint spawns a separate Python process that loads the model, processes the image, and exits (freeing all memory). Added model variant selection (birefnet-general, birefnet-portrait, birefnet-massive) and alpha matting with edge feathering via Gaussian blur on alpha channel edges only. Processed images are composited onto a white background.

**Debugging approach:** Monitored memory usage with Activity Monitor during processing. Identified that model weights persisted after inference. Tested subprocess approach and confirmed memory dropped from 28GB peak to ~300MB.

### Silent Auto-Trigger: Background Removal Respawning at 32GB

**Problem:** After the initial BiRefNet memory fix (above), the 32GB Python process kept reappearing in Activity Monitor. Killing it via `kill` only worked temporarily — it respawned within minutes, consuming 32GB again. The user wasn't explicitly running any background removal.

**Root cause:** Both artist photo upload endpoints (`POST /{artist_id}/image` and `POST /{artist_id}/photo-from-url`) included a `background_tasks.add_task(_process_single, ...)` call that silently auto-triggered background removal after every photo upload. FastAPI's `BackgroundTasks` ran this after the HTTP response was sent, so the user never saw it happen. The background task imported `rembg`, loaded the BiRefNet ONNX model into memory, and ONNX Runtime spawned a multiprocessing subprocess (visible as a `multiprocessing.spawn` child of the uvicorn process) that inflated to 32GB. Because the ONNX session was cached in a module-level dict (`_current_session`), the memory was never released — and any subsequent photo upload would re-trigger the entire chain if the subprocess had been killed.

**Solution:** Removed the auto-trigger from both upload endpoints. Background removal is now strictly opt-in — users must explicitly click "Remove Background" in the UI or use the batch removal endpoint. The upload endpoints simply save the photo and return, with no side effects.

**Debugging approach:** Used `ps aux -p <PID>` to identify the process as a `multiprocessing.spawn` child, then `ps -o ppid=` to trace it back to the uvicorn parent (not Celery, as initially suspected). Grepped the codebase for `background_tasks.add_task` in the artist API routes, which revealed the two auto-trigger call sites. The fix was a two-line deletion — but finding it required understanding the full chain: FastAPI BackgroundTasks → lazy import of `background_removal` → `rembg.new_session()` → ONNX Runtime → multiprocessing subprocess.

A third session added library management refinements: a song archive system with soft-delete and restore, a playlist selector UI for Moises setlist import (scan available playlists, show checkboxes, import selected), hiding unreliable audio feature bars from the UI, multi-genre support per song, and a crown icon color fix in the lead sheet viewer. The session also hit a react-leaflet v5/React 18 incompatibility that crashed the map page — resolved by downgrading to react-leaflet 4.2.1 and clearing Vite's module cache.

The fourth session focused on data quality and completeness. The world map was improved to show all countries (not just top 20), with country flag emojis added throughout. A metadata backfill pipeline was built: MusicBrainz for artist countries (172 artists backfilled automatically, with improved ISO subdivision code handling for cases like Jack Johnson whose area is "Hawaii" not "United States"), Deezer and iTunes for song duration/year/genre, and language inference from artist countries. An audit script was written to detect and fill all gaps. After automated backfill, the user manually provided data for the remaining 44 artists and edge-case songs, achieving 100% coverage across all metadata fields for 1,233 songs and 557 artists. A scroll restoration bug was diagnosed using Playwright — the fix required understanding React StrictMode's double-mount behavior, which was clearing the scroll restore timer before it could fire. The project was tagged as `v1.0-24h` to mark the milestone.

The seventh and eighth sessions added artist photo infrastructure: a multi-source image search aggregating 6 free APIs (TheAudioDB, Wikimedia Commons, Deezer, Spotify client_credentials, Wikipedia en+es, Wikidata), a dedicated /artists/photos page for bulk management, and BiRefNet-powered background removal that was initially consuming 28GB of RAM — fixed by moving to subprocess execution, dropping to ~300MB. The random mix generator gained multi-key selection with toggle pills.

The ninth and tenth sessions focused on UI refinements and a major map migration: scroll-to-top buttons on the artists page, collapsible key panels in random mix, and replacing Leaflet with Google Maps API. The Google Maps migration required resolving a conflict between `mapId` (needed for AdvancedMarker) and inline `styles` (needed for the dark theme), ultimately switching to basic Marker components with SVG data URI icons. The dark theme was tuned to show only country labels with enough contrast against the dark background.

The twelfth session added guitar chord diagrams to lead sheets — SVG fretboard renderings with X/O indicators, finger numbers, barre arcs, and fret labels using svguitar + @tombatossals/chords-db (2,069 voicings across 756 chord types). A chord name parser was built to handle Unicode ♭/♯ normalization and slash chords. The edit mode gained a diagram selector with toggleable chord pills and voicing variation cycling. A pre-existing ♭/♯ kerning bug was discovered — the fix ran before chord text existed in the SVG — and garbled key values ("Daj" instead of "D") were traced to truncated Moises API output and fixed across 27 rows in both local and production databases.

The eleventh session focused on deployment, mobile responsiveness, and data quality. The frontend was deployed to Cloudflare Pages with `VITE_API_URL` pointed at the Render backend — the first deploy failed because the API base URL defaulted to `/api` (relative), sending requests to the Cloudflare domain instead of Render; the second failed because it was set to `https://tunespace-api.onrender.com` without the `/api` prefix, resulting in 404s. A mobile hamburger menu was refined with a close (X) button inside the sidebar, body scroll lock, and conditional hamburger visibility. A top progress bar was added using React Query's `useIsFetching` hook — it trickles from 0% to 90% during data loading and snaps to 100% on completion, using the project's accent gradient colors. A critical memory leak was debugged: a 32GB Python process kept respawning because both artist photo upload endpoints silently auto-triggered BiRefNet background removal via FastAPI's `BackgroundTasks` — traced via `ps aux` → parent PID → grep for `background_tasks.add_task`, fixed by making background removal opt-in only. Data quality fixes included merging duplicate artists (Timø/Timo via Unicode variation), correcting artist countries (Pablo Lacadiere: Jamaica → Mexico, verified via web search), and splitting combined artists ("Shakira & Papatinho" into separate Shakira and Papatinho entries with proper song linkage). The playlist page's artist marquee was polished: bottom spacing tightened to match side margins, artist thumbnails made right-clickable via `<a>` tags (supporting "Open in new tab"), and hover name tooltips repositioned as gradient overlays inside the image bounds to avoid `overflow-hidden` clipping.

The sixth session focused on the live performance workflow. The chord editor gained drag-and-drop chord repositioning (for correcting off-by-one-beat placement from ML extraction), a right-click context menu with silence rests (rendered as proper VexFlow quarter rests), colored dot annotations (6-pastel palette rendered as superscripts in the lead sheet), free-text annotations ("bar", "harmonics at 12th" rendered in italic below chords), measure copy/paste with animated flash feedback, and measure deletion. A BPM-synchronized auto-scroll was added for live performance — with a 4-second countdown, continuous smooth scrolling using frame-rate-independent exponential interpolation, and a progress bar. Measure timestamps were added to both views. The VexFlow chord rendering was eventually moved from VexFlow's built-in Annotation system (which provided no control over vertical positioning) to raw SVG text elements placed at pixel-precise positions relative to `stave.getYForLine(0)`, giving full control over the chord-to-staff spacing. The password gate was made conditional (auto-skipped on localhost), and the deployment password was updated.

The fifth session introduced AI-powered editing. The user proposed using Claude Code CLI headlessly to edit the database directly via natural language — an interesting idea, but the latency (10-30s per edit from process spawn) and security risks (`--dangerously-skip-permissions` with DB access) led to a cleaner architecture: Claude Haiku via the Anthropic API with constrained tool_use. The NL endpoint is interpret-only — it returns a preview diff of proposed changes, and actual mutations go through the existing PATCH endpoints, preserving audit logging, validation, and propagation. After testing, the user noticed artist edits couldn't be made from the song context ("this is Spanish and Dani is from Chile"), so both tools were made available simultaneously — Claude can now return song AND artist edits in a single call. A persistent phantom scroll bug on the song detail modal was traced to the `scale(1.25)` CSS transform on the ambient background inflating `scrollHeight` — confirmed via Playwright tests comparing the old and new structure. The session also added drag-and-drop album art uploads on the entire modal, mirroring the existing artist page pattern.

### CSS Transform Inflating Scroll Height

**Problem:** The song detail modal always had a scrollbar, even when the content clearly fit within the viewport. The user reported extra empty space at the bottom that allowed phantom scrolling.

**Root cause:** The ambient album art background used `position: absolute; inset: 0; transform: scale(1.25)` for a blurred edge effect. Per the CSS spec, CSS transforms on positioned elements DO affect overflow calculation. The `scale(1.25)` expanded the element to 125% of the container, and since the container had `overflow-y: auto`, the browser created a scrollbar to accommodate the transformed size — adding ~25% extra scroll height with no visible content.

**Solution:** Split the modal into two layers: an outer container with `overflow: hidden` that clips the scaled background, and an inner container with `overflow-y: auto` that handles content scrolling. The background is rendered but its transform can no longer inflate the scrollable area.

**Debugging approach:** Built a Playwright test with two HTML structures — the old (overflow-y-auto + scale-125 on same element) and new (overflow-hidden wrapper + inner scroll container). The old structure was scrollable by 25px on a short content page; the new structure was not scrollable. Confirmed the root cause is the CSS transform affecting overflow.

### VexFlow Annotation Positioning: Abandoning the Built-in System

**Problem:** Chord symbols above the staff had too much vertical whitespace — roughly 40px of gap between the chord text and the top staff line. The user wanted this reduced by 65%.

**Root cause:** VexFlow's `Annotation` class with `VerticalJustify.TOP` positions chord text at a fixed distance above the stave, determined by internal font metrics and stave padding. The `setYShift()` modifier, which should offset the annotation vertically, had no visible effect — likely overridden by VexFlow's internal layout calculations during the draw phase. Multiple attempts with shifts from 25 to 45 pixels produced identical output.

**Solution:** Removed VexFlow's Annotation system entirely. Chord symbols are now rendered as raw SVG `<text>` elements in a post-processing step, positioned at `stave.getYForLine(0) - 22px` — exactly 22 pixels above the top staff line. This gives pixel-precise control over spacing. Colored dots and text annotations are positioned relative to the chord text's `getBBox()`, and the flat/sharp kerning fix still processes the Ranchers-font text elements since they share the same font-family attribute.

**Debugging approach:** Iterative visual testing with increasing `setYShift` values (25, 45) confirmed the modifier had no effect. Switched to measuring exact stave line positions via `stave.getYForLine(0)` and rendering chord text as manually-placed SVG elements, verifying against the user's screenshots at each step.

### Accidental Kerning: Execution Order Bug

**Problem:** Chord names with ♭/♯ symbols (like `C#m7♭5`) had excessive spacing on the lead sheet — the flat symbol was much wider than surrounding characters, making the chord name look broken.

**Root cause:** A kerning fix already existed in the codebase — it measured the width difference between the ♭ glyph and "b" in the Ranchers font, then applied negative `dx` attributes on SVG `<tspan>` elements to tighten spacing. However, this fix ran via `svgEl.querySelectorAll("text")` *before* the chord text elements were created and appended to the SVG. The fix was processing VexFlow's internal text elements (which don't use Ranchers font) and missing all the chord symbols entirely.

**Solution:** Moved the kerning fix to execute *after* all chord symbols, colored dots, and text annotations are appended to the SVG. The `querySelectorAll("text")` now captures all text elements including the dynamically-added chord names. Kerning multipliers were tuned iteratively (0.75/0.70 → 0.40/0.35 → 0.60/0.45) based on visual feedback.

**Debugging approach:** The user reported the spacing was broken despite a fix existing. Reading the render function revealed the execution order: kerning fix at line 533, chord text creation at line 572. The fix simply couldn't process elements that didn't exist yet. A second issue emerged: chord names containing the Unicode ♭ (U+266D) symbol weren't matching in the chord database — the parser only normalized accidentals in the root note position, not throughout the suffix. Adding a `normalizeAccidentals()` pass before suffix matching resolved both `D#m7♭5` and `D#m7b5` to the same chord.

### NL Editing: CLI vs API Architecture Decision

**Problem:** The user proposed running Claude Code CLI in headless mode (`--dangerously-skip-permissions`) to interpret natural language edit instructions and modify the database directly. The idea was to avoid extra API tokens by using the CLI subscription.

**Root cause:** While creative, this approach had fundamental issues: (1) Claude Code CLI still uses API tokens under the hood — the subscription covers them, but it's not "free", (2) process spawn latency of 10-30 seconds per edit vs ~1 second with the API, (3) `--dangerously-skip-permissions` with direct DB access is a security risk — the CLI could execute any SQL, (4) only works on the developer's machine, not deployable.

**Solution:** Used the Anthropic Python SDK with Claude Haiku directly in the FastAPI backend. The key design: constrained tool_use with two tool definitions (`edit_song_metadata` with an enum of 11 valid fields, `edit_artist_metadata` with 3 fields) ensures Claude can only return valid structured edits — no SQL, no arbitrary actions. The NL endpoint is interpret-only; it never writes to the DB. The frontend shows a preview diff, and on confirmation, calls the existing PATCH endpoints that preserve audit logging, validation, and propagation. Cost: fractions of a cent per edit with Haiku.

**Debugging approach:** After the initial implementation with one tool per entity type, testing revealed that combined instructions ("this is Spanish and the artist is from Chile") couldn't edit both song and artist. The fix: provide both tools simultaneously with `tool_choice: "any"`, then dispatch based on `tool_use.name` in the response. A subtle mock issue emerged in tests — `MagicMock().name` returns the mock's internal name, not an attribute, so tool name matching silently failed. Switched to `SimpleNamespace` for tool_use mocks.

## Architecture

Key technical decisions:

- **FastAPI over Flask** — Async-native for background task coordination, auto-generated OpenAPI docs, Pydantic validation. The user's background is Flask but the async requirements (multiple concurrent API enrichments, non-blocking downloads) favor FastAPI.
- **PostgreSQL with pg_trgm** — Enables fuzzy search with `%` operator across a GIN-indexed `search_text` column. Combined with `unaccent` extension for accent-insensitive matching (critical for Spanish-language music: "Maria" matches "maria").
- **Celery + Redis over simple BackgroundTasks** — Persistent task queue survives server restarts. Fan-out pattern for metadata enrichment: one song triggers parallel Deezer, MusicBrainz, and chord extraction tasks. Redis doubles as cache layer.
- **Client-side search with Fuse.js** — For instant perceived performance (<100ms). All songs loaded in memory on the frontend with tuned fuzzy matching (threshold 0.2, minMatchCharLength 3). Server-side pg_trgm search serves as fallback.
- **Deezer + iTunes over Spotify** — After a 23-hour Spotify rate limit lockout, pivoted entirely. Deezer needs no API key and has generous limits. iTunes as fallback for remaining album art gaps.
- **librosa for audio features** — Spotify deprecated their audio features endpoint. Built local extraction: RMS energy, onset strength, beat regularity, chroma profiles → normalized to 0-1 energy, danceability, valence, acousticness scores.
- **Chrome extension with cross-frame scripting** — No Moises library API exists. The extension scrapes the DOM for library data and uses `allFrames: true` with Performance API to intercept chord data from the player's cross-origin iframe.
- **VexFlow for lead sheets** — JavaScript music notation library that renders in-browser. Chord symbols over rhythm slashes, measure numbers, bar lines. Print-friendly white background per Tufte-inspired UX guidelines.
- **JSONB for chord data** — Flexible schema for chord progressions with optional `annotation` and `dot` fields per beat. Supports the chord editor's beat-level editing, colored annotations, and the "Golden" verification badge system.
- **Raw SVG over VexFlow Annotations** — VexFlow's built-in Annotation system provided no reliable control over vertical positioning (setYShift was ignored). Chord symbols are rendered as raw SVG text elements at `stave.getYForLine(0) - 22px`, with colored dots and text annotations positioned via getBBox(). This gives pixel-precise layout control while still benefiting from VexFlow's staff line and note rendering.
- **Optimistic UI for the Golden toggle** — Immediate visual feedback on the chord editor's verification button, with server-side persistence and rollback on error. Prevents the lag that frustrated the user in the initial implementation.
- **Leaflet + CartoDB dark tiles over Google Maps** — User wanted Google Maps but didn't want to pay. Leaflet with CartoDB dark_all tiles is 100% free, no API key needed, and matches the dark glassmorphism theme. Still has smooth zoom/pan and clickable markers.
- **URL-param-driven sidebar navigation** — Clicking "Rock" in the sidebar navigates to `/?genre=Rock`. The Library page reads search params on mount and applies them as filters. Simple, shareable, and bookmarkable — no complex state management needed.
- **Server-side facets endpoint** — `GET /songs/facets` uses PostgreSQL `unnest()` + `GROUP BY` to aggregate genres, languages, decades, and countries with song counts. Runs once and is cached for 5 minutes. Avoids computing facets client-side from 10K songs on every sidebar render.
- **Auto-save with debounced mutation** — Non-propagatable fields auto-save after 1.5s of inactivity. Propagatable fields (genres, languages, tags) still require explicit confirmation via the propagation dialog. "Cancel & Revert" stores original values and sends a revert mutation on exit.
- **Multi-API metadata backfill with audit script** — MusicBrainz for artist countries (with ISO 3166-2 subdivision code fallback for areas like "Hawaii" → "United States"), Deezer for duration/year/genre, iTunes as fallback, language inferred from artist country. An audit script runs all checks and fills gaps in a single pass, achieving 100% metadata coverage.
- **Bulk chord extraction via background tabs** — The Chrome extension opens songs in inactive tabs (`active: false`), avoiding focus stealing. 8-second wait per song accounts for Moises's lazy chord loading. Fully resumable via the `pending-chords` endpoint that only returns songs still missing chord data.
- **NL editing via Claude Haiku with constrained tool_use** — Natural language metadata editing uses the Anthropic API with forced tool_use to constrain output to valid field edits (enum of allowed fields, typed values). The interpret endpoint is read-only — it returns a preview diff; actual mutations go through existing PATCH endpoints preserving audit logging and propagation. Both song and artist tools are provided simultaneously so a single instruction can edit both entities. Cost: ~700 input tokens + ~100 output tokens per edit at Haiku pricing.
- **Separated overflow containers for scaled backgrounds** — CSS transforms on positioned elements inflate `scrollHeight`. The song modal uses `overflow: hidden` on the outer container (clips the `scale(1.25)` ambient background) and `overflow-y: auto` on the inner container (handles content scrolling). Never put `overflow-y: auto` on the same element as a scaled absolute child.
- **svguitar + chords-db for guitar diagrams** — svguitar renders pure SVG chord diagrams (no React 19 dependency, unlike svguitar-react), while @tombatossals/chords-db provides 2,069 voicings across 756 chord types with finger positions, fret numbers, and barre data. A custom chord name parser normalizes Unicode ♭/♯ to ASCII and handles slash chords, mapping to chords-db's key+suffix format. Both libraries are code-split into separate lazy-loaded chunks (237KB + 162KB) to avoid bloating the main bundle.
- **Split deployment: Cloudflare Pages + Render** — Frontend SPA on Cloudflare's CDN (free, fast, global), backend API + PostgreSQL on Render (free tier with sleep after 15 min). Password validated server-side via Bearer token middleware — the frontend PasswordGate hits the API to verify, never stores or bundles the password in client code. Render's `DATABASE_URL` uses `postgresql://` but SQLAlchemy needs `postgresql+asyncpg://` — a Pydantic model_validator auto-converts on startup.
