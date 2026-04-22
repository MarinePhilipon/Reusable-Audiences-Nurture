---
name: creative-wizzard-prototype-baseline
description: >
  Capture a live Rokt OnePlatform Creatives V2 page and produce a pixel-faithful,
  fully clickable standalone HTML prototype saved to /Users/marine/Desktop/Claude/.
  Replicates the full Creative Wizard UI: top nav with campaign stepper, sidebar with
  creative list, all form fields (Creative name, Format Targeting dropdown, Title, Body,
  CTA, URL, Image upload, Disclaimer, Callout Tags, More Options, Link Audiences, Link
  Offers), right panel with Creative Optimization Score and 4 preview tabs
  (Standard/Block/Tile/Hero), and bottom nav. All interactions wired: tab switching,
  sidebar selection, live preview updates, collapsibles, dropdown.
  ALWAYS use when the user says "grab the design from this creatives page", "make a
  prototype of the creative wizard", "clickable prototype of the creatives UI", or shares
  a my.rokt.com URL with "/creatives-v2/". Trigger on: "creative wizard", "creatives
  prototype", "creative wizzard", "prototype baseline", or any Rokt creatives URL.
---

# Creative Wizzard Prototype Baseline

Captures the live Rokt OnePlatform Creative Wizard UI and produces a standalone,
clickable HTML prototype that faithfully replicates the design — including all form
fields, preview states, and interactive behaviours.

---

## When This Skill Runs

The user provides (or already has open in Chrome) a `my.rokt.com` URL of the form:
```
https://my.rokt.com/accounts/.../campaigns/.../creatives-v2/...
```

---

## Step 1: Capture the Live Page

Use **Claude in Chrome** tools (`tabs_context_mcp`, `computer`, `find`) to:

1. Navigate to the URL if not already open.
2. Take a **full-page screenshot** at default zoom.
3. **Scroll down** through the entire form to capture any content below the fold — the
   form is long (Creative Image, Disclaimer, Callout Tags, More Options, Link Audiences,
   Link Offers all appear below the visible area on most screens).
4. Switch each **preview tab** (Standard → Block → Tile → Hero) using `find` to locate
   the buttons by name, then `left_click` via `ref`. Capture a screenshot after each switch.
5. **Open the Format Targeting dropdown** by clicking the combobox, capture a screenshot
   showing all options, then press Escape.
6. Note the exact text of every sidebar creative item (name + ID shown below it).

If Chrome tools time out, fall back to the screenshots already captured in the current
session — do not abandon the task.

---

## Step 2: Extract Design Tokens

From the screenshots, note:

| Token | Value |
|-------|-------|
| Primary / brand colour | Deep pink/magenta ~`#9B1756` |
| Primary dark (hover) | `#7a1244` |
| Primary light (bg tint) | `#fdf0f7` |
| Text primary | `#1a1a2e` |
| Text secondary | `#555` |
| Text placeholder | `#bbb` / `#c0c0c0` |
| Border | `#e2e2e2` |
| Background grey | `#f8f8f8` |
| Success green | `#2ea84c` |
| Warning orange | `#f5a623` |
| Border radius | `8px` (inputs), `20px` (CTA pills), `10px` (preview cards) |
| Font | System stack: `-apple-system, BlinkMacSystemFont, 'Inter', 'Segoe UI', sans-serif` |

---

## Step 3: Build the HTML Prototype

Write a **single self-contained HTML file** to `/Users/marine/Desktop/Claude/creatives-prototype.html`.

### Layout (three-column, full-viewport)

```
┌─────────────────────────────────────────────────────────┐
│  Top Nav: back ← | Campaign Title ✏️ | Stepper          │
├──────────┬──────────────────────────┬───────────────────┤
│ Sidebar  │  Form (scrollable)       │  Right Panel      │
│ 210px    │  flex:1                  │  355px            │
├──────────┴──────────────────────────┴───────────────────┤
│  Bottom Bar: Previous | Save and Exit | Submit          │
└─────────────────────────────────────────────────────────┘
```

Use `display:flex; flex-direction:column; height:100vh; overflow:hidden` on body.
The form column and right panel must be independently scrollable (`overflow-y:auto`).

### Top Nav
- Back arrow button (chevron left SVG)
- Campaign title text + pencil emoji
- Three-step stepper: **Campaign** (green ✓ badge) → line → **Audiences** (green ✓) → line → **Creatives** (pink badge with count "3", bold label)

### Left Sidebar (210px)
- Search bar (icon + "Search..." placeholder)
- "+ Add" pink button + "Sort: Newest ▾" button + filter icon button
- Scrollable list of creative items. Each item:
  - Coloured dot (pink = active/published, grey = inactive, orange = draft/carousel)
  - Creative name (bold when selected, pink colour)
  - Creative ID (small, grey, truncated)
  - "···" more button (visible on hover only)
  - Clicking selects the item and updates the form heading

### Main Form (scrollable)
Header row: `{creative name} • Draft` (orange dot for Draft badge)

Fields in order — all use 8px border-radius, 1px `#e2e2e2` border, focus ring `rgba(155,23,86,.1)`:

1. **Creative name** (text input, full width in left half of a two-column row)
2. **Format Targeting** (custom dropdown in right half — see Dropdown section below)
3. **Creative Title** (text input, full width, char counter `0/50` pinned right inside)
4. **Creative Body** (textarea 84px tall, "Insert Attribute +" button top-right, char counter `0/175` below)
5. **Total character count** line: `Total character count: 0 / 175`
6. **Positive CTA** (text input with 👍 emoji prefix, default "Yes please")
7. **CTA URL** (text input + "Add URL Parameters" outline button beside it)
8. **Creative Image** (dashed upload zone: upload icon SVG, "No images assigned yet" title, description, pink "+ Select or Upload Images" button)
9. **Disclaimer** (textarea 72px, placeholder "Eligible customers only.", helper text below)
10. **Add Callout Tag(s)** (dashed + button; clicking prompts for text and renders pill chips with × remove)
11. **[Divider line]** → **More Options (optional)** — collapsible chevron section:
    - Coupons: Yes / No radio (No pre-selected), helper text
    - Brand Domains: Yes / No radio (No pre-selected), helper text
    - Schedule: "Run Continuously" / "Set a start and end" radio (Continuously pre-selected)
12. **[Divider]** → **Link Audiences** — collapsible:
    - Search input "Search for an audience to link"
    - Table with "Audiences" header + "No audiences linked" empty state
13. **[Divider]** → **Link Offers** — collapsible:
    - Search input "Search or select an offer" with ⓘ icon on right
    - Helper text "Select offers or create an offer…"
    - Checkbox "This creative does not have an offer" + sub-text with linked phrase
    - Offer Overview box: "Offer Overview" header, warning triangle + "No offer selected" body

### Format Targeting Dropdown
Custom (not native `<select>`). A styled div that looks like an input with chevron.
Clicking opens an absolute-positioned menu with options:
- Dynamic (Standard, Block, Tile) ✓ (selected by default)
- Benefits
- Compact
- Savings
- Image

Clicking an option updates the label and closes. An invisible overlay div (z-index 100)
behind the menu closes it when clicked outside.

### Right Panel (355px, independently scrollable)
- **Score header**: `--` ring circle (36px, grey border) + "Creative Optimization Score" title + "View Actions" underlined link
- **Preview tabs**: Standard | Block | Tile | Hero — pill buttons, active = dark border+text, hover = pink border+text
- **Preview card** (changes per active tab):

  **Standard** — white card, border 1px:
  - Body row: italic placeholder text (left, flex:1) + "No image" grey square (62×62px, right)
  - Two pink rounded CTAs: "Yes please" + "Decline"
  - "Powered By Rokt – Privacy Policy" right-aligned footer

  **Block** — horizontal strip:
  - Small image placeholder (50×50px, left)
  - Italic placeholder text (flex:1)
  - Single "Yes please" CTA (far right)
  - "Powered By Rokt – Privacy Policy" right-aligned footer

  **Tile** — centred square card (155px wide):
  - Image placeholder (full width, 96px tall)
  - Italic title placeholder
  - Full-width pink "Yes please" CTA button
  - "Powered By Rokt – Privacy Policy" centred footer

  **Hero** — full-width contextual card:
  - Grey header bar: "Purchase complete. Order #: 12345678" + × close button
  - Body: "Thank you for your purchase:" label + white content box (110px tall)
  - Footer: "Powered By Rokt" (left) + pink "Yes please" CTA (right)

- Disclaimer: *"The exact layout of the ad is dependent on the site it is showing on and is liable to change"*

### Bottom Bar
`Previous` (outline) on left. `Save and Exit` (outline) + `Submit` (pink) on right.

---

## Step 4: Wire Up All Interactivity

```js
// Tab switching
switchTab(id, btn) → hide all .pcard, show #pcard-{id}, toggle .active on tabs

// Sidebar selection
pickCreative(el, name, id) → toggle .active on items, update form heading + name input

// Format dropdown
toggleDropdown() / closeDropdown() / setFormat(el, label)
→ toggle .open on menu + overlay, update label, move ✓ check mark

// Live preview update (fires on title, body, CTA input events)
updatePreview() →
  - Update char counters (title: /50, body: /175, total combined)
  - Update preview text in all 4 cards (italic placeholder if empty, real text if filled)
  - Update CTA button labels in all 4 cards to match Positive CTA field

// Collapsible sections
toggleSection(id) → toggle .collapsed class (CSS hides .collapsible-body when collapsed)

// Callout tags
addTag() → prompt() for text, render pill with × remove button

// No-offer checkbox
toggleNoOffer(cb) → swap warning state ↔ success state in Offer Overview
```

---

## Step 5: Quality Check

Before saving, verify:
- [ ] The file is fully self-contained (no external dependencies — all CSS and JS inline)
- [ ] All 4 preview tab states render correctly and switch on click
- [ ] The form scrolls independently of the right panel
- [ ] The Format Targeting dropdown opens/closes and updates the label
- [ ] Typing in Creative Title / Body / CTA updates the preview cards live
- [ ] All collapsible sections (More Options, Link Audiences, Link Offers) open and close
- [ ] Sidebar items are clickable and update the form heading

Save to: `/Users/marine/Desktop/Claude/creatives-prototype.html`

Tell the user: "Prototype saved to [creatives-prototype.html](creatives-prototype.html) — open it directly in your browser."
