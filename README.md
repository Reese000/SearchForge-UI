# SearchForge-UI — Design Tokens & Component Library

> **UI architecture and static component library** for the SearchForge / Universal Shopping Search Engine.
> Built on the Reese Design System v1.2 dark theme.

---

## What's Here

| File | Lines | Purpose |
|------|-------|---------|
| `SPEC.md` | 577 | Full UI architecture spec — layout, components, interactions, responsive breakpoints |
| `tokens.css` | 575 | Base design system tokens (Reese Design System v1.2) |
| `tokens-searchforge.css` | 967 | Shopping-specific token extensions (domain colors, deal badges, layout vars) |
| `components.html` | 1,932 | Complete UI component reference — every component with HTML templates + CSS |
| `index.html` | 1,644 | Interactive demo — full app shell with all components working |

**Total: ~5,800 lines of UI specification and reference code.**

## Quick Start

```bash
cd SearchForge-UI
python -m http.server 8765
# Open http://localhost:8765
```

## Interactive Features (in demo)

- **Ctrl+K** — Command palette with search, recent, saved, and commands
- **Ctrl+1/2/3** — Switch domain tabs (CNC / Endmills / Cars)
- **/** — Focus search bar
- **?** — Keyboard shortcuts overlay
- **Click result card** — Opens detail drawer with specs, price chart, dealer info
- **Right-click card** — Context menu (save, compare, alert, share)
- **Filter chips** — Toggle active/inactive with click
- **View toggle** — List/Grid/Map view modes
- **Comparison tray** — Fixed bottom bar for item comparison

## Design System Integration

SearchForge extends the existing Reese Design System:

```html
<link rel="stylesheet" href="tokens.css">                    <!-- Base tokens -->
<link rel="stylesheet" href="tokens-searchforge.css">        <!-- Shopping extensions -->
```

### Domain Accent Colors

| Domain | Color | Token |
|--------|-------|-------|
| CNC Machines | Orange `#ff9a5c` | `--sf-domain-cnc` |
| Endmills | Teal `#5ce1e6` | `--sf-domain-endmill` |
| Used Cars | Blue `#6c8aff` | `--sf-domain-car` |

### Deal Quality Colors

| Quality | Color | Condition |
|---------|-------|-----------|
| 🟢 Great Deal | `--code-success` | >15% below market avg |
| 🟡 Fair Price | `--code-warning` | ±15% of market avg |
| 🔴 Above Market | `--code-danger` | >15% above market avg |
| 🔵 New Listing | `--code-action` | Listed <24h ago |
| 🔷 Price Drop | `--code-info` | Reduced since last seen |

## Relationship to Other Repos

| Repository | Relationship |
|------------|-------------|
| **SearchForge** | Sibling design-docs repo. Together they form the complete planning package for the product. Should be merged into SearchForge as a `ui/` subfolder. |
| **Universal-Shopping-Search** | The implementation. Uses these design tokens and component patterns via the Jinja2 + HTMX frontend. |

## Status

**Design: COMPLETE.** Static HTML/CSS reference — no JavaScript framework, no build tooling. Ready to be consumed by the implementation's Jinja2 templates.

*Created: 2026-07-11*
