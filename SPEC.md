# SearchForge — UI Architecture & Component Specification

**Version:** 1.0  
**Design System:** Reese Design System v1.2 (dark theme)  
**Domains:** CNC Machines · Endmills · Used Cars  

---

## 1. Layout Architecture

### 1.1 Main Structure

```
┌──────────────────────────────────────────────────────────────────┐
│  Windows Titlebar (minimize/maximize/close)                      │
├──────────┬───────────────────────────────────────────────────────┤
│          │  Search Bar + Domain Selector                         │
│ Sidebar  ├───────────────────────────────────────────────────────┤
│ (240px)  │  Domain Tabs (CNC | Endmills | Cars)                  │
│          ├──────────┬────────────────────────────────────────────┤
│ Nav      │ Filters  │  Main Content Area                        │
│ Groups   │ (280px)  │  (results / dashboard / comparison)       │
│          │          │                                            │
│          │  Resizable│  ┌──────────────────────────────────┐    │
│          │  ←→       │  │  Result Cards (grid/list/map)     │    │
│          │          │  └──────────────────────────────────┘    │
├──────────┴──────────┴────────────────────────────────────────────┤
│  Status Bar (stats, connection, last sync)                       │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Panel Sizing

| Panel | Min | Default | Max | Resizable |
|-------|-----|---------|-----|-----------|
| Sidebar | 180px | 240px | 400px | ✓ (horizontal) |
| Filter Panel | 0px (collapsible) | 280px | 420px | ✓ (horizontal) |
| Main Content | 480px | fluid | — | ✓ |
| Detail Drawer | 0px (slides in) | 360px | 500px | ✓ (horizontal) |

### 1.3 Responsive Breakpoints

| Breakpoint | Behavior |
|------------|----------|
| ≥1200px | Full 3-column layout (sidebar + filters + content) |
| 960–1199px | Sidebar collapses to icon rail (48px), filters slide-over |
| <960px | Sidebar hidden (hamburger toggle), filters as modal overlay |

### 1.4 Z-Index Stack

| Layer | Z-Index | Use |
|-------|---------|-----|
| Base content | 1 | Result cards, tables |
| Sticky filters | 10 | Filter panel sticky header |
| Sidebar | 20 | Always visible in desktop |
| Floating panels | 30 | Comparison tray, chart popout |
| Tooltip | 50 | Rich tooltips |
| Modal overlay | 100 | Dialogs, confirmations |
| Command palette | 200 | Ctrl+K global search |
| Titlebar | 300 | Always on top |

---

## 2. Component Library

### 2.1 Universal Search Bar

```
┌─────────────────────────────────────────────────────────────────┐
│ [▾ CNC] │ 🔍 Haas VF-2 2019...           │ ⌘K  │ [Filters]  │
└─────────────────────────────────────────────────────────────────┘
```

**Spec:**
- Height: `--h-xl` (44px) — hero prominence
- Domain selector: dropdown left of input, shows active domain icon + label
- Input: full-width, transparent bg, `--ink-primary` text
- Keyboard shortcut badge: `⌘K` shown inline (grayed)
- Filter toggle button: shows active filter count badge

**Autocomplete Dropdown:**
```
┌─────────────────────────────────────┐
│ 🔍 SUGGESTIONS                      │
│ ├── CNC Machines                    │
│ │   └── Haas VF-2 Vertical Mill     │
│ ├── Endmills                        │
│ │   └── 1/2" 3-Flute Carbide        │
│ └── Used Cars                       │
│     └── 2020 Toyota Tacoma SR5      │
│                                     │
│ Recent: Haas VF-2 (2h ago)          │
│ Saved: "Under $50k CNC" (pinned)    │
└─────────────────────────────────────┘
```

### 2.2 Domain Tab Switcher

```
┌──────┬──────────┬──────────┐
│ 🔧 CNC │ ⚙ Endmills │ 🚗 Cars │
└──────┴──────────┴──────────┘
```

- Each tab: icon + label + result count badge
- Active tab: `--code-action` bottom border, `--canvas-raised` bg
- Domain-specific accent on hover
- Badge shows result count (e.g., "2,847")
- Keyboard: `Ctrl+1/2/3` to switch domains

### 2.3 Filter Panel

Collapsible left panel. All filters are domain-configurable:

**Universal Filters (all domains):**
- Price range (dual slider with min/max inputs)
- Location (radius picker with map)
- Condition (checkbox group)
- Seller rating (star filter)
- Listed date (dropdown: 24h, 7d, 30d, any)

**CNC-Specific Filters:**
- Machine type (dropdown: VMC, HMC, Lathe, Router)
- Manufacturer (multi-select chips)
- Travel envelope (3-axis range inputs: X/Y/Z)
- Spindle speed range
- Control brand (Haas, Fanuc, Siemens, etc.)
- Year range
- Hour meter range

**Endmill-Specific Filters:**
- Diameter (stepper + preset chips: 1/8, 1/4, 3/8, 1/2, 3/4, 1)
- Flute count (2, 3, 4, 5, 6, 8+)
- Coating (multi-select: TiN, TiAlN, AlCrN, DLC, Uncoated)
- Material (carbide, HSS, cobalt, diamond)
- Type (flat, ball, bull-nose, chamfer, drill)
- Length of cut range
- Shank diameter
- Bulk quantity (toggle: single/box/case)

**Car-Specific Filters:**
- Make/Model (cascading dropdown)
- Year range (slider)
- Mileage range (slider with presets: <30k, <50k, <100k)
- VIN decoder (text input with instant decode)
- Body type (chips: sedan, SUV, truck, coupe, van)
- Drivetrain (FWD/RWD/AWD/4WD)
- Fuel type (gas, diesel, hybrid, electric)
- Accident history (toggle)
- Title status (clean, salvage, rebuilt)

### 2.4 Result Card (Domain-Aware)

**Base Card Structure:**
```
┌─────────────────────────────────────────┐
│ [■ Photo Grid]  │  Title / Name         │
│ [■ or Map]      │  Subtitle / Specs     │
│                 │  ─────────────────     │
│                 │  💰 $XX,XXX            │
│                 │  📍 Location · Distance│
│                 │  ⭐ Seller Rating      │
│                 │  [Deal Quality Badge]  │
│                 │                        │
│                 │  [♥ Save] [⚖ Compare]  │
│                 │  [🔗 Visit Listing]    │
└─────────────────────────────────────────┘
```

**Deal Quality Badge:**
- 🟢 Great Deal — `--code-success` — price >15% below market avg
- 🟡 Fair Price — `--code-warning` — within ±15% of market avg
- 🔴 Above Market — `--code-danger` — price >15% above market avg
- 🔵 New Listing — `--code-action` — listed <24h ago

**CNC Card Variant:**
```
┌─────────────────────────────────────────────────┐
│ [📷 Machine Photos ×4]                          │
│                                                  │
│ Haas VF-2SS Super Speed                         │
│ 2019 · 1,247 hours · Fanuc 31i                 │
│ ─────────────────────────────────────────────── │
│ $52,500                        🟢 Great Deal   │
│ 📍 Dallas, TX · 342 mi away                     │
│ ⭐ 4.8 (23 sales) · Verified Dealer             │
│                                                  │
│ Specs: 30"×16"×20" · 12K RPM · 20T ATC         │
│                                                  │
│ [♥ Save] [⚖ Compare] [🔗 View] [📞 Contact]    │
└─────────────────────────────────────────────────┘
```

**Endmill Card Variant:**
```
┌─────────────────────────────────────────────────┐
│ [📷 Tool Photo]                                  │
│                                                  │
│ Harvey Tool 1/2" 3-Flute TiAlN                  │
│ 0.500" DIA · 1.5" LOC · 3.0" OAL               │
│ ─────────────────────────────────────────────── │
│ $24.95 (box of 10: $198)          🟡 Fair       │
│ 📍 Supplier: Maritool · In Stock                 │
│ ⭐ 4.9 (142 reviews) · Free Ship $50+           │
│                                                  │
│ Coating: TiAlN · HRC: 45-55 · Alti: Steel       │
│                                                  │
│ [♥ Save] [⚖ Compare] [🛒 Add to Cart]           │
└─────────────────────────────────────────────────┘
```

**Car Card Variant:**
```
┌─────────────────────────────────────────────────┐
│ [📷 Vehicle Photos ×6]  [360° view]             │
│                                                  │
│ 2020 Toyota Tacoma TRD Off-Road                  │
│ V6 · 4WD · Automatic · 34,200 mi               │
│ ─────────────────────────────────────────────── │
│ $34,900                        🟢 Great Deal   │
│ 📍 Phoenix, AZ · 128 mi away                    │
│ ⭐ 4.7 (89 sales) · Carfax Verified             │
│                                                  │
│ 🏷 Clean Title · No Accidents · 1 Owner          │
│                                                  │
│ [♥ Save] [⚖ Compare] [🔗 View] [📞 Dealer]     │
└─────────────────────────────────────────────────┘
```

### 2.5 Cross-Domain Comparison Table

```
┌──────────────────────────────────────────────────────────────────────┐
│  ⚖ COMPARISON (3 items)                    [Clear] [Export CSV]     │
├──────────────┬──────────────┬──────────────┬─────────────────────────┤
│  Field        │ Item 1       │ Item 2       │ Item 3                  │
├──────────────┼──────────────┼──────────────┼─────────────────────────┤
│  Name         │ Haas VF-2    │ DMG Mori     │ Mazak VTC-300C         │
│  Price        │ $52,500 🟢   │ $67,000 🔴   │ $58,900 🟡             │
│  Year         │ 2019         │ 2020         │ 2018                    │
│  Hours        │ 1,247        │ 890          │ 2,100                   │
│  Travel       │ 30×16×20     │ 31×20×20     │ 52×28×25                │
│  Spindle      │ 12,000 RPM   │ 15,000 RPM   │ 12,000 RPM              │
│  ATC          │ 20+1         │ 30+1         │ 40+1                    │
│  Location     │ Dallas, TX   │ Chicago, IL  │ Atlanta, GA             │
│  Market Avg   │ $55,000      │ $55,000      │ $55,000                 │
│  Δ vs Avg     │ -4.5% 🟢     │ +21.8% 🔴    │ +7.1% 🟡                │
├──────────────┼──────────────┼──────────────┼─────────────────────────┤
│              │ [♥ Save]     │ [♥ Save]     │ [♥ Save]                │
│              │ [🔗 View]    │ [🔗 View]    │ [🔗 View]               │
└──────────────┴──────────────┴──────────────┴─────────────────────────┘
```

**Comparison Rules:**
- Max 4 items side-by-side
- Drag-drop from result cards to comparison tray
- Floating comparison tray at bottom (collapsible)
- Delta vs market average color-coded (green/amber/red)
- Different domains show different field sets (domain-adaptive)
- Export to CSV/PDF

### 2.6 Dashboard Stats Widgets

```
┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ 📊 Total     │ │ 💰 Avg Price │ │ 🔥 Hot Deals │ │ 👁 Watching   │
│ 12,847       │ │ $47,250      │ │ 23 new       │ │ 8 items      │
│ listings     │ │ (↓2.3%)     │ │ today        │ │ 3 price drops │
└──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘
```

- Stat cards: `--canvas-surface` bg, left colored border (domain accent)
- Trend arrows: green up / red down with percentage
- Sparkline mini-charts in each card
- Click to drill down

### 2.7 Alert Rule Builder

```
┌─────────────────────────────────────────────────────────────┐
│  🔔 ALERT RULES                         [+ New Rule]        │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────┐   │
│  │  WHEN [Haas VF-*] price [drops below] [$50,000]    │   │
│  │  AND [location] [within 500 mi] of [Dallas, TX]     │   │
│  │  THEN [notify me] via [email] + [push]               │   │
│  │  [Edit] [Test] [✕ Delete]                            │   │
│  └─────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  WHEN [new 1/2" endmill] [coating=TiAlN] under [$20]│  │
│  │  THEN [add to watchlist] + [email alert]             │   │
│  │  [Edit] [Test] [✕ Delete]                            │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 2.8 Price Chart

```
┌─────────────────────────────────────────────────┐
│  📈 Price History — Haas VF-2 (90 days)         │
│  ┌─────────────────────────────────────────┐    │
│  │     ╭──╮                                │    │
│  │    ╭╯  ╰──╮    ╭──╮                    │    │
│  │ ╭──╯      ╰────╯  ╰──╮  ← Current     │    │
│  │╭╯                    ╰── $52,500       │    │
│  │───────────────────────── Market Avg ────│    │
│  │  ╰╮                                       │    │
│  │    ╰─────  Min: $42,000                   │    │
│  └─────────────────────────────────────────┘    │
│  30d    60d    90d    ●  Avg: $55,200           │
│  [Export] [Set Alert] [Compare Market]          │
└─────────────────────────────────────────────────┘
```

- SVG line chart on `--canvas-surface`
- Market average: dashed line (`--ink-muted`)
- Price alert threshold: dotted red line
- Hover shows tooltip with exact price + date
- Gradient fill below line with domain accent color

### 2.9 Map View

```
┌─────────────────────────────────────────────────┐
│  🗺 Map View                                    │
│  ┌─────────────────────────────────────────┐    │
│  │          [pin]                           │    │
│  │    [pin]        [pin cluster: 12]        │    │
│  │              [pin]                        │    │
│  │     [pin]              [pin]              │    │
│  └─────────────────────────────────────────┘    │
│  Radius: [100 mi ▾]  [📍 Use My Location]      │
│  Pins colored by deal quality:                  │
│  🟢 Great  🟡 Fair  🔴 Above Market            │
└─────────────────────────────────────────────────┘
```

- Dark map tiles (Mapbox/Leaflet dark theme)
- Cluster markers with count
- Radius circle overlay
- Click pin → mini result card popup
- Heatmap overlay mode for price density

---

## 3. Color Coding System

### 3.1 Domain Accent Colors

| Domain | Accent Color | Token | Use |
|--------|-------------|-------|-----|
| CNC Machines | `--code-tool` | `#ff9a5c` | Tab active, card borders, badges |
| Endmills | `--code-material` | `#5ce1e6` | Tab active, card borders, badges |
| Used Cars | `--code-action` | `#6c8aff` | Tab active, card borders, badges |

### 3.2 Deal Quality Colors

| Quality | Color | Token | Condition |
|---------|-------|-------|-----------|
| Great Deal | Green | `--code-success` | >15% below market avg |
| Fair Price | Amber | `--code-warning` | ±15% of market avg |
| Above Market | Red | `--code-danger` | >15% above market avg |
| New Listing | Blue | `--code-action` | Listed <24h ago |
| Price Drop | Cyan | `--code-info` | Reduced since last seen |

### 3.3 Semantic Color Shopping Mapping

| Token | Shopping Use |
|-------|-------------|
| `--code-action` (Blue) | Primary CTAs, search, navigation, car domain |
| `--code-danger` (Red) | Above-market prices, alerts, remove actions |
| `--code-success` (Green) | Great deals, in-stock, verified sellers |
| `--code-warning` (Amber) | Fair prices, limited stock, expiring deals |
| `--code-info` (Cyan) | Informational badges, price drops, help text |
| `--code-accent` (Purple) | Saved items, watchlists, user collections |
| `--code-tool` (Orange) | CNC domain, machine specs, tool operations |
| `--code-material` (Teal) | Endmill domain, materials, coatings |
| `--code-data` (Pink) | Analytics, metrics, market data |
| `--code-view` (Lime) | Map view, gallery, photo displays |
| `--code-config` (Gold) | Filter parameters, alert config, settings |
| `--code-ai` (Magenta) | AI recommendations, smart suggestions |
| `--code-process` (Indigo) | Workflow steps, comparison pipeline |

---

## 4. Interaction Patterns

### 4.1 Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+K` | Open command palette / global search |
| `Ctrl+1/2/3` | Switch domain (CNC/Endmills/Cars) |
| `Ctrl+F` | Focus filter panel |
| `Ctrl+Shift+C` | Toggle comparison tray |
| `Ctrl+S` | Save current search |
| `Ctrl+E` | Export results (CSV/PDF) |
| `Ctrl+Shift+A` | Open alert builder |
| `/` | Focus search bar (when no input focused) |
| `Escape` | Close overlay / deselect / clear search |
| `J/K` | Navigate results up/down |
| `Enter` | Open selected result |
| `Space` | Toggle comparison on selected result |
| `H` | Toggle heart/save on selected result |
| `Ctrl+M` | Toggle map view |
| `Ctrl+L` | Toggle list/grid view |
| `?` | Show keyboard shortcuts overlay |

### 4.2 Bulk Operations

Select mode (triggered by `Ctrl+A` or checkbox on cards):
- Select all visible / select page / deselect all
- Bulk actions: Save to watchlist, Add to comparison (max 4), Export, Set alert, Email share
- Floating action bar at bottom with selection count

### 4.3 Drag-Drop Comparison

- Drag result card → comparison tray (bottom)
- Visual drop zone with dashed border highlight
- Max 4 items; 5th drop replaces oldest
- Comparison tray: collapsible, shows mini thumbnails
- Drag to reorder within comparison

### 4.4 Context Menu (Right-Click)

On result card:
- Save to Watchlist
- Add to Comparison
- Set Price Alert
- Open Listing in New Tab
- Share Link
- Report Listing
- Copy Specs

On filter:
- Reset This Filter
- Save Filter Preset
- Remove All Filters

### 4.5 Command Palette (Ctrl+K)

```
┌─────────────────────────────────────────────────┐
│  🔍 Type a command or search...                  │
├─────────────────────────────────────────────────┤
│  Recent Searches                                │
│  ├── Haas VF-2                                  │
│  └── 1/2 TiAlN endmill                          │
│                                                  │
│  Saved Searches                                  │
│  ├── Under $50k CNC                              │
│  └── Premium endmills                            │
│                                                  │
│  Commands                                        │
│  ├── Switch to CNC domain           [Ctrl+1]    │
│  ├── Open saved searches            [Ctrl+S]    │
│  ├── Export current results         [Ctrl+E]    │
│  └── Show keyboard shortcuts        [?]         │
└─────────────────────────────────────────────────┘
```

---

## 5. Data Visualization

### 5.1 Price Distribution Chart

Histogram showing price distribution of search results:
- X-axis: price buckets (auto-calculated)
- Y-axis: count of listings
- Highlighted bar for current item's price
- Market average vertical line
- Overlay: user's budget range (shaded area)

### 5.2 Market Trends (Sparklines + Line Charts)

- 30/60/90 day price trends per domain
- Trending up/down indicators
- Volume of new listings over time
- Seasonal patterns (e.g., CNC auctions spike Q4)

### 5.3 Geographic Heat Map

- Price density by region
- Seller concentration dots
- Shipping cost overlay
- Filter by drive-ship radius

### 5.4 Spec Radar Charts

For comparison — overlay up to 4 items on radar/spider chart:
- CNC: travel, speed, power, accuracy, value, age
- Endmills: hardness, coating, flute count, value, availability, reviews
- Cars: mileage, condition, features, value, reliability, safety

---

## 6. Navigation Structure

### Sidebar Navigation

```
┌──────────────────┐
│ SearchForge  v1  │  ← Brand + version
├──────────────────┤
│ 🔍 Search        │  ← Primary (always highlighted)
├──────────────────┤
│ 📊 Dashboard     │  ← Overview stats
│ 🔔 Alerts        │  ← Active alert rules
│ ⚖ Compare       │  ← Comparison table
│ ♥ Watchlist      │  ← Saved items
│ 📈 Trends        │  ← Market analysis
│ 🗺 Map           │  ← Geographic view
├──────────────────┤
│ ──── Recent ──── │
│ Haas VF-2        │
│ 1/2 TiAlN        │
│ Tacoma 2020      │
├──────────────────┤
│ ──── Saved ───── │
│ Under $50k CNC   │
│ Premium endmills │
├──────────────────┤
│ ⚙ Settings       │
│ ❓ Help / ?       │
└──────────────────┘
```

### Status Bar

```
┌──────────────────────────────────────────────────────────────┐
│ 12,847 results │ 23 new today │ Last sync: 2m ago │ 🟢 Live │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. File Structure

```
SearchForge-UI/
├── SPEC.md                    ← This file
├── tokens-searchforge.css     ← Shopping-specific token extensions
├── index.html                 ← Full interactive demo (search + results)
├── components.html            ← **Complete component reference** (HTML + CSS templates for all 10 components)
└── README.md                  ← Quick start guide
```

---

## 8. Implementation Notes

### Token Architecture
- `tokens.css` remains the single source of truth (imported via `<link>`)
- `tokens-searchforge.css` adds shopping-specific variables (domain accents, deal colors)
- All new components use existing tokens — no custom values inline

### Accessibility
- All interactive elements keyboard-accessible
- ARIA labels on icon-only buttons
- Screen reader announcements for filter changes
- High contrast mode support via `prefers-contrast`
- Focus management in modals and command palette

### Performance
- Virtual scrolling for result lists (>100 items)
- Lazy-load images with skeleton placeholders
- Debounced search input (300ms)
- Filter changes: optimistic UI with rollback on error
- Comparison data cached in memory

### State Management
- URL params encode search state (shareable links)
- LocalStorage for: saved searches, watchlist, comparison items, UI preferences
- Session state for: active filters, sort order, view mode
