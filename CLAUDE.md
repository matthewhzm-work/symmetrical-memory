# Matthew Ho — Portfolio Site

## Project Overview
Personal portfolio site for Matthew Ho, Senior Creative (Copywriter · Strategist · Campaign Lead), based in Singapore. Built in vanilla HTML/CSS/JS — no frameworks, no build tools. Deployed on GitHub Pages at matthewho.work.

## Resume
`resume.html` — ATS-friendly resume page. Pulls all content (name, tagline, bio, experience, awards) live from `data.js`. Accessible at matthewho.work/resume.html. Has a browser-print "Save as PDF" button. No Claude/AI attribution anywhere in this file or its commit.

## File Structure
- `index.html` — Homepage: 3-column independent flex grid, About section, Recognition section
- `work.html` — All project pages (single file, block-based renderer + legacy template fallback)
- `admin.html` — Password-protected CMS
- `data.js` — Single source of truth for all content. Exposes `PORTFOLIO_DATA` global.
- `resume.html` — ATS-friendly resume, data-driven from data.js, print-to-PDF ready
- `images/` — All processed images as WebP (80% quality, 2400px max) or optimised GIF

## Design System
- Fonts: Lexend (display/titles), Inter (body), IBM Plex Mono (labels/UI/mono)
- Colours: --paper:#F5F4EF  --ink:#0A0A0A  --red:#E8200C  --mid:#888  --light:#E2E1DC
- Nav: fixed, transparent, 2-row on mobile. Name left · tagline+awardstrip centre · links right
- Cards: 3 independent flex columns (true Pinterest masonry), 8px gap, translateY(-8px) lift on hover, red overlay, NO shadow, NO image zoom
- Video embeds: max-width 1075px, centred
- Images: always full height (height:auto), never cropped

## data.js Structure
Each project has:
  id, brand, title, subtitle, award, tags, placeholderColor, gridSpan,
  thumbnailUrl, thumbnailType, active, projectTemplate,
  description, fullDescription,
  videos: [{url, type, caption}],       ← array, NOT single videoUrl
  collaterals: [{type, url, caption, orientation}],
  thumbnails: [{url, caption, active}], ← gallery thumbs (HSBC Mind Athletes)
  prints: [{url, caption}],             ← portrait prints (MHA)
  quote,                                ← opening quote (HSBC Mind Athletes, vivo)
  openingQuestion,                      ← opening question (CWYL)
  blocks: [{...}]                       ← page builder blocks (see below — overrides template if present)

## Block-Based Page Builder (PRIMARY system)
Every active project now has a `blocks` array in data.js. When `P.blocks && P.blocks.length > 0`, work.html renders from blocks and ignores the projectTemplate entirely.

### Block Types
- `video`     — {id, type, url, videoType, caption, width}
                width: 'full' | 'wide' (1075px) | 'medium' (720px) | 'narrow' (560px)
- `callout`   — {id, type, text, size, align}
                size: 'xl' (clamp 28→52px) | 'lg' | 'md' | 'sm'
                align: 'center' | 'left'
- `text`      — {id, type, html, columns, width}
                html: Quill-generated HTML string
                columns: 1 | 2 | 3
- `image`     — {id, type, url, caption, width, lightbox}
- `image-row` — {id, type, images:[{url,caption}], columns, gap, lightbox}
                gap: 'tight' (4px) | 'normal' (12px) | 'wide' (32px)
- `split`     — {id, type, leftType, leftUrl, leftVideoType, leftHtml, leftLabel,
                              rightType, rightUrl, rightVideoType, rightHtml, rightLabel}
                leftType/rightType: 'text' | 'video' | 'image'
                Renders as 50/50 grid, collapses to 1-col on mobile

### Block IDs
Each block has a short unique id (e.g. 'b101', 'ba03'). These must be unique across the entire data.js — use a project-prefix convention.

### Lightbox Architecture
Pool-based. LB_POOLS object stores named arrays. Block renderer auto-registers:
  LB_POOLS['blk_'+b.id] for image and image-row blocks with lightbox:true
Never use variable references in inline onclick strings — use LB_POOLS string keys.

## Legacy Templates (fallback only — do not use for new projects)
If a project has no blocks array (or empty blocks), work.html falls back to projectTemplate:
- `lux`, `hsbc-mind-athletes`, `writeup-then-video`, `writeup-left-video-right`,
  `video-left-writeup-right`, `writeup-left-video-right-row3`, `video-left-writeup-right-row3`,
  `two-videos-side-by-side`, `prints-side-by-side`, `mha`, `cwyl`, `ofnoah`, `vivo`, `vw-prints`, `default`
Hidden projects (active:false) still use legacy templates and do not need blocks.

## Active/Hidden Projects
- active: true  → shown on homepage grid and project rail
- active: false → deployed but hidden everywhere
- Currently hidden: mercedes-me-adapter, trifecta-snow-surf-skate, nea-good-for-me-good-for-the-environment

## Project Rail
Bottom of every project page. Dark ink background. All active projects as thumbnails.
Auto-scrolls to centre current project on load. Current project dimmed (opacity 0.35).
Desktop: ‹ › arrow buttons. Mobile: swipe only (buttons hidden).

## Image Processing
- Convert to WebP at 80% quality, max 2400px wide
- GIFs preserved as-is (animated), optimised with gifsicle --lossy=80 --colors 128
- Thumbnails for cards: 1400×900px landscape WebP or GIF
- All images go in images/[project-id]/ folder
- GIF background colour replacement: use Python + numpy (exact pixel match). sips cannot write WebP; use PIL/Pillow instead. PyMuPDF (fitz) available for PDF text extraction.
- Site paper colour is `#FFFAF0` — all GIF backgrounds should match this, not the old `#F5F4EF`

## Phone Number
REMOVED everywhere — do not add it back to any file.

## GitHub Deployment
Repo: github.com/matthewhzm-work/matthewho.github.io. Deploy from main branch via GitHub Pages.
Images uploaded via GitHub Desktop (not web UI — too slow for bulk).
Custom domain: matthewho.work

## CMS Admin
URL: admin.html. Password stored in sessionStorage only — not documented here.
3-tab project editor: Card | Page Content | Gallery
- Card tab: brand, title, subtitle, award, tags, description (RTE), thumbnail
- Page Content tab: full block builder — add/remove/reorder blocks of any type
- Gallery tab: toggle individual thumbnail images on/off
Toggle Live/Hidden per project in list view. Visual 3-column grid mirrors homepage order.
← → arrows to reorder projects. Export + Download data.js after making changes.
All save operations read from DOM by ID on save (no oninput closures).

## Remaining Projects Not Yet Built
The following projects are hidden (active:false) and need bespoke blocks when Matthew provides assets:
- nea-good-for-me-good-for-the-environment
- bybit-valentines-day (active, has blocks — may need richer layout)
- vivo-v15 (active, has blocks — social films are 4:5 ratio, currently rendered as 16:9)
- volkswagen-prints (active, has blocks)

## Anchored Festival Strip (index.html)
On desktop (> 900px wide), the award festival strip (`#fest`) is fixed to the bottom of the viewport on initial page load. The about section bio is vertically centred in the remaining space. A double-chevron scroll indicator (`#scroll-indicator`) sits just above the strip. On scroll, the strip releases at the exact position where its natural document position aligns with its fixed position (zero jump). The about section padding transitions smoothly back to natural CSS values.

Key JS functions: `festSetup()` (called on `window.load`), `festRelease()` (called when `scrollY >= festReleaseAt`).
- `festAnchored` flag — guards `fixAboutPad()` from clearing inline padding while in fixed mode
- `festPh` — placeholder div inserted after `#fest` to preserve document flow
- `festReleaseAt` — calculated as `naturalFestTop - (vp - festH)`, the scroll position where positions align
- Does NOT run on mobile (≤ 900px) — `fixAboutPad()` handles mobile padding separately

## Key Rules
1. Never use oninput closures for save logic — always read from DOM by ID on save
2. Never put variable references in inline onclick strings — use LB_POOLS string keys
3. Always filter D.projects by active !== false before rendering grid or rail
4. Images must use height:auto — never object-fit:cover on displayed images
5. Video max-width is 1075px (25% bigger than the original 860px)
6. No box-shadow on card hover — translateY(-8px) lift only
7. No credentials/meta sidebar on any project page template
8. data.js uses videos[] array not videoUrl/videoType/videoCaption single fields
9. Block IDs must be unique across all projects in data.js
10. **Any direct code edit to a project's page content (copy, layout, assets) must also be reflected in that project's blocks[] array in data.js so the CMS stays in sync.** Do not patch work.html templates or hardcode content that bypasses the block system.
11. Never document passwords or credentials in this file or any tracked file in the repo.
12. resume.html must never contain AI/Claude attribution — neither in code comments nor commit messages.
