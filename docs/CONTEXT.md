# Benny's Cocktails — Full Project Context

**APP_VERSION:** 5.5 | ~496KB | ~9100 lines
**Recipes:** 125 published + 13 binned
**Settings pages:** 9 (General, Tags, Units, Garnishes, Glasses, Base Spirits, Techniques, Rims, Access)

---

## Complete Version History (55 versions)

### Foundation & Core (v0.1–0.5)
Fork from Benny's Recipes v1.43. Dark theme (#242018 paper, #e8e0d8 ink). Cocktail data model: spirit, technique, glass, frozen glass, ice, garnish, standardDrinks, scalable, allowedScales. 5× scaling with per-ingredient overrides. Filter overhaul: Base Spirit, Std Drinks range, Technique, Glass — with cross-dim dimming (unavailable options greyed). Smart group-by with tag category ordering. 6-page settings: Tags, Units, Garnishes, Glasses, Base Spirits, Techniques. Prep field fully removed, view meta restructured. Critical save crash fixed (orphaned prep references).

### Settings & Data (v0.6–0.8)
Settings modal with dropdown navigation in header bar. Base Spirit + Technique as managed lists (add/rename/delete/reorder). Default data sync: 57 spirit tags from Benny's 125 recipes, Flamed garnishes, Origin tags, expanded flavour/dietary. Default merge on load (new defaults merge into saved settings without overwriting). View polish: padding, group-by ordering, scale note spacing.

### Glass Icons (v0.9–1.2)
18→34 hand-designed SVG glass silhouettes. GLASS_ICON_MAP with icon picker modal in Settings → Glasses. Single-row recipe cards (name left, glass icon right). All settings pages converted from pills to individual rows with ▲▼ reorder arrows.

### Features & Polish (v1.3–1.8)
Scale order ½×→5× (with 4×/5× in view). Alphabetical card display. Default ml unit. Method override fallback (shows original text, not "does not scale"). Tags removed from recipe view (filter/group only). Tag sort by settings order on recipe open. "Variation of" recipe linking with auto Variants display. Status badge moved to edit form header. Info modal fully rewritten for cocktail context. iOS select height fix (-webkit-appearance:none + custom SVG chevron).

### Settings Rows & Sync (v1.9–2.4)
All settings pages converted to row layout with .stg-item class + data-name attribute for reliable element matching. Tag dedup on render. Gist data sync expanded defaults. Drafts toggle: ON=drafts only, OFF=published only (exclusive, not inclusive). Move tag button styled + "Choose new section…" text. Section name modal replaces browser prompt().

### Accent & Rim (v2.5–2.9)
Deep blue accent (#4080c8) replacing original copper. Method step numbers: white on solid accent. Confirm dialog system fix: querySelector scoped to #confirm-overlay (section-name-overlay was intercepting). Rim as separate field from garnish: edit form dropdown, view meta row, Settings → Rims page with full CRUD + reorder. refreshEditFormDropdowns() syncs dropdowns when settings change.

### Glass Refinement (v3.0–3.1)
Collaborative design pass with Benny: Rocks/Double Rocks squared walls (DR has lip line), Collins fully squared, Coupe/Nick&Nora/Snifter closed tops with rim lines, Wine proper U-shape bowl, Glencairn symmetric tulip with flared base, Margarita rounded lip, Copper Mug squared, Irish Coffee pedestal base, Hurricane/Grappa symmetric with both walls. Removed Zombie and Cordial. 32 icons total.

### Viewer Mode (v3.2–3.5)
Admin/Viewer access model with tabbed Connect modal. Multiple access codes with labels [{code, label}] — create, edit label, revoke. VIEW_ONLY flag hides .admin-only elements. Viewer-specific info modal content (applyViewerInfoContent). What's New: viewerNotes field, disabled for viewers. No-credentials state: view-only with seed samples, setup modal opens to Viewer tab. connectAsViewer validates against accessCodes array in settings.json. Cache clearing on revoked codes (clearViewerSession).

### General Settings (v3.6–4.1)
Custom app name (max 16 chars). Responsive mobile font scaling: window.innerWidth <= 600, formula max(0.45, 7/nameLen). 8 accent colour palette persisted to Gist. Settings: General tab first (default on open), single-section tabs borderless (CSS approach for Spirits/Techniques/Rims/Glasses). Settings footer removed. What's New disabled for viewers.

### Security & Access (v4.2–4.8)
Baked-in Gist ID (46caec02a3b745a1554de6553a390226) for simplified viewer login — viewers only need access code, not Gist ID. Database dropdown in viewer login (default vs custom). adminGistId stored in settings.json. Admin + viewer disconnect buttons with proper cache clearing (reset name/accent to defaults). Clone tool: copies all published recipes + full settings to target Gist.

### Dev Workflow & Exports (v4.9–5.5)
Dev/prod two-file setup with different baked Gist IDs. Clone tool enhanced with buildSettingsObj() for full settings copy. Info modal comprehensive rewrite (all 7 admin tabs + 4 viewer tabs). C117: Enter=new step wired up. C75: All exports updated for cocktail data model — TXT, MD, CSV (with UTF-8 BOM), HTML (complete rewrite as standalone viewer with embedded JSON, glass icons, scaling, substitutes, search). Alphabetical sorting in all bulk exports.

---

## Current Data Model
```json
{
  "id": timestamp,
  "name": "string",
  "status": "draft|published",
  "tags": ["Bourbon", "Classic", "Sour"],
  "spirit": "Bourbon",
  "technique": "Stirred",
  "scalable": true,
  "allowedScales": { "half": true, "double": true, "triple": true, "quadruple": false, "quintuple": false },
  "glass": "Rocks",
  "glassFrozen": true,
  "ice": "Large Cube",
  "rim": "Salt Rim",
  "garnish": "Orange Peel, Luxardo Cherry",
  "standardDrinks": 1.4,
  "equipment": ["Shaker", "Jigger"],
  "notes": "string",
  "variationOf": recipeId,
  "ingredients": [{
    "title": "Section Name",
    "items": [{
      "name": "Bourbon",
      "amount": 60,
      "unit": "ml",
      "note": "preferably high-rye",
      "optional": false,
      "noscale": false,
      "scaleOverrides": { "double": 100, "triple": { "amount": 150, "unit": "ml" } },
      "substitutes": [{
        "name": "Rye Whiskey",
        "amount": null,
        "unit": null,
        "note": "spicier profile",
        "group": "Rye Version",
        "noscale": false,
        "scaleOverrides": {}
      }]
    }]
  }],
  "method": [{
    "title": "Section Name",
    "steps": [{
      "text": "Stir all ingredients with ice",
      "note": "30 seconds",
      "noscale": false,
      "scaleOverrides": { "triple": "Split across two mixing glasses" }
    }]
  }]
}
```

---

## Complete Feature Set
- Recipe CRUD with draft/publish, undo/redo (per-session), duplicate
- 32 SVG glass icons with picker + frozen glass toggle (blue tint + snowflake)
- Scaling ½×–5× with per-ingredient and per-step overrides
- Ingredient substitutes with groups/modes (e.g. "Low ABV Version", "Virgin/Mocktail")
- "Variation of" recipe linking with auto-discovery of variants
- Rim as separate field from garnish with managed options
- Search by name, multi-dimensional filter (Tags, Base Spirit, Std Drinks, Technique, Glass), group-by
- 9 settings pages with row-based management (add/rename/delete/reorder)
- Custom app name (max 16 chars, responsive mobile scaling) + 8 accent colours
- Viewer mode with access codes (read-only, viewer-specific info + What's New)
- Admin + viewer disconnect with proper cleanup
- Clone tool (all published recipes + full settings to friend's Gist)
- Export: TXT, MD, CSV (UTF-8 BOM), HTML standalone viewer, JSON backup
- Info modal with admin + viewer versions (7 tabs admin, 4 tabs viewer)
- Dev/prod two-file workflow with baked Gist IDs
- Offline support with localStorage cache + sync queue

---

## Access System

### Connection States
| State | How | What they see |
|---|---|---|
| No credentials | First visit or after disconnect | VIEW_ONLY, seed sample cocktails, setup modal |
| Admin | Gist ID + GitHub token | Full edit, settings, access codes, clone tool, export |
| Viewer | Access code only (baked Gist ID used) | Read-only browse, search, filter, scale, substitutes |

### Access Code Management
- Admin creates codes in 🔑 → Admin tab → "Viewer access codes"
- Each code: `{code: "bar2025", label: "Staff"}`
- Codes stored in settings.json on Gist
- Viewer connects: 🔑 → Viewer tab → enter code → connectAsViewer validates against settings.json
- Revoke: removes code, viewer sees "Invalid or revoked access code" on next load, cache cleared
- Multiple codes work independently

### Clone Tool
- Admin-only, in 🔑 → Admin tab → "Clone recipes to another database"
- Enter target Gist ID + target GitHub token
- Copies: all published recipes (IDs randomised) + full settings (buildSettingsObj)
- Does NOT copy: access codes, drafts, bin
- One-way copy — recipient owns their independent copy

---

## Backlog — Prioritised

### Immediate Fixes
| Task | Description |
|---|---|
| **C140** | MD export: spacing between recipes not visible in rendered view — needs different approach (horizontal rule or HTML `<br>`). Ingredients header (`###`) still shows underline + larger font than Method header — both should match. |

### PRIORITY: Auth & Database Overhaul
| Task | Description |
|---|---|
| **C138** | **Auth system overhaul** — Replace GitHub tokens with proper 3rd-party auth (email/password login). Current system requires sharing GitHub tokens which gives access to ALL Gists on that account. Security risk: Benny's Gist ID is baked into the code, and tokens are shared. End goal: users have secure individual logins, no GitHub knowledge required. Consider: Supabase Auth, Firebase Auth, Auth0. |
| **C139** | **Database migration** — Move from GitHub Gist to secure cloud-hosted database. Users should have private storage only they can access. Eliminate "create a Gist" friction. Consider: Supabase (PostgreSQL + row-level security), Firebase (Firestore), PocketBase. Migration path needed for existing Gist users. |

### Tier 1 — Data Model
| Task | Description | Effort |
|---|---|---|
| **C130** | Spirit tag categories — group spirit tags under base spirit parents (White Rum, Dark Rum under Rum). Eventually infer base spirit from tags. Eventually link to ingredients for auto-detection. Needs hierarchical tag UI in settings. | L |
| **C133** | Units settings expansion — add Volume (ml, oz, tsp, tbsp, cup, L) and Mass (g, kg) categories with enable/disable toggles. Users turn off units they never use. Custom units section stays. | M |
| **C137** | Garnish modifiers — flamed/dehydrated toggles instead of duplicate garnish entries. Needs C64 (structured garnishes) first. Options: toggle buttons, text suffix ("Orange Peel — flamed"), icon prefix. | L |

### Tier 2 — UX Features
| Task | Description | Effort |
|---|---|---|
| **C141** | Folders/Collections — user-created groups on home screen (e.g. "House Cocktails", "Summer Menu"). Horizontal folder chips above card grid. Recipes can be in multiple folders. Settings → Folders page. Different from tag filtering — deliberate user-curated groupings. Useful for venues organising menu sections. | L |
| **C142** | "What Can I Make?" / Stock Mode — user sets up available spirits/liqueurs in Settings → My Bar. Filter view: "Can make" (all ingredients available), "Missing 1-2" (almost there). Highlights which specific ingredient is missing. Needs fuzzy matching of ingredient names to stock list. | L |
| **C83** | Ingredient-as-tag toggle — per-ingredient toggle in edit form marks ingredient as filterable tag. Needs design discussion: which tag category do auto-tagged ingredients go into? Auto-sync when renamed/removed? | M |

### Tier 3 — Polish & Refinement
| Task | Description | Effort |
|---|---|---|
| **C76** | Info modal — Benny to review all tabs after v5.1 comprehensive rewrite. Both admin + viewer versions. | M |
| **C129** | Glass icon further refinement — Glencairn specifically needs work. Others to review at small card size. Collaborative with Benny's design input. | M |
| **C64** | Garnish structured items — make garnishes objects with properties (substitutes, notes, optional, modifiers) instead of plain strings. Prerequisite for C137 (garnish modifiers). | L |
| **C110** | Garnish quantities (e.g. "3 × Lemon Wedge") | M |

### Known Bugs
| Issue | Status |
|---|---|
| Tag double-up | Mitigated with dedup on render. Root cause in addFormTag may still create duplicates. |
| Name/accent lag on connect/disconnect | KNOWN, minor — slight delay before custom name/accent appear after connecting |

---

## Testing Checklist
| Test | Status |
|---|---|
| HTML export: standalone viewer loads, recipes clickable, scaling works | ✅ VERIFIED |
| Enter=new step in method | NEEDS TEST |
| CSV em dash display in Excel | NEEDS TEST |
| MD formatting (spacing, header consistency) | NEEDS FIX (C140) |
| 16-char app name iPhone | ✅ VERIFIED |
| Desktop app name stays full size | ✅ VERIFIED |
| Settings tabs functional after border changes | NEEDS TEST |
| Viewer code create/connect/revoke cycle | ✅ PARTIALLY VERIFIED |
| Clone tool (recipes + settings) | ✅ VERIFIED |
| Admin disconnect → revert to defaults | ✅ VERIFIED |

---

## Future Ideas

### Near-Term Possibilities
- **Card colour accents** — by base spirit or user-defined
- **Sections toggle** — settings option to show/hide ingredient/method section buttons
- **Recipe versions** — full linked variants beyond "Variation of" (add/remove ingredients)

### Big Features
- **Glass Volume & Dilution Calculator** — set glass volumes per venue, dynamic total volume with dilution (shaking ~25-30%, stirring ~15-20%) and ice displacement
- **Ingredient/Brand Library** — tiered Brand→Type (e.g. "Smirnoff" → Vodka), per-ingredient ABV, auto-calculate standard drinks, managed stock list, recipe costing
- **Spirit tag → Base spirit inference** — C130 evolution, eventually link to ingredients for auto-detection
- **Community recipe database** — shared repository users can browse and add to their own collection

### Platform & Scaling
- **Proper auth system (C138)** — 3rd party auth, passwords, user accounts
- **Cloud database (C139)** — Firebase/Supabase/PocketBase, private per-user storage
- **App store presence** — iPhone/Android installable via proper PWA or native wrapper

### Exploration
- Bar Mode / House Cocktails tabs
- Build Queue (multi-select + combined prep view)
- Non-Alcoholic filter split (0 std drinks vs 0.25–1)
- Dark/Light Mode Toggle
- Prebatch Mode (calculate large batch quantities from single serve)

---

## Design Decisions (for reference)

### Base Spirit vs Spirit Tags
Two separate systems exist: Base Spirit = broad classification for the recipe overall ("This is a Vodka cocktail"). Spirit tags = granular per-ingredient tagging (White Rum, Dark Rum, Bourbon). They're managed as independent lists. C130 proposes merging them.

### Garnish System
Currently garnishes are plain strings in a comma-separated field, with managed categories in settings (Citrus, Fruit, Herbs, Dehydrates, Flamed, Other). C64 proposes making them structured objects. C137 proposes adding modifiers to eliminate duplicate entries like "Flamed Orange Peel".

### Variation Linking
"Variation of" field links a recipe to its parent. The parent recipe auto-discovers children and shows them in a "Variants" section. Substitutes with groups handle ingredient swaps within a recipe. Full recipe versions (different ingredients/method) would need a more complex system.

### Data Migration Strategy
No migration scripts — defensive/additive approach. Fields read with fallbacks (e.g. `r.equipment || r.utensils || []`). New fields default to null/undefined. Old fields never deleted. Settings merge on load (new defaults appear for existing users). Gist files are forward-compatible across all 55 versions.

---

## Architecture Notes

### Core Structure
- Single HTML file with all CSS/JS/HTML embedded (~496KB, ~9100 lines)
- Two copies: index.html (prod Gist) + dev.html (dev Gist) — identical except BAKED_GIST_ID
- Fonts: Playfair Display (serif, headings) + Instrument Sans (sans, body) via Google Fonts
- CSS variables for theming: --ink, --paper, --card, --border, --accent (4 variants)

### Settings System
- `buildSettingsObj()` serialises all settings to a JSON object
- `applySettingsObj(s)` loads settings from JSON, with fallbacks for missing fields
- Saved to: localStorage (`cocktails_settings_cache`) + Gist (`settings.json`)
- Default merge: new defaults (e.g. new garnish categories) merge into saved settings on load
- `refreshEditFormDropdowns()` rebuilds managed dropdowns when settings change

### Glass Icon System
- `GLASS_ICONS`: object mapping icon names to SVG path strings (32 icons)
- `GLASS_ICON_MAP`: object mapping glass names to icon keys (user-configurable)
- `rawGlassIcon(iconKey, size)`: renders an SVG element for any icon
- Icon picker modal in Settings → Glasses

### View & Filter System
- `render()`: builds card grid from filtered/grouped recipes
- `viewRecipe(id)`: opens recipe view modal with full meta, ingredients, method
- Filter state: activeTags, activeSpirits, activeStdDrinks, activeTechniques, activeGlasses
- Cross-dimensional dimming: unavailable filter options greyed out
- Group-by: any tag category, Base Spirit, Glass, Technique, Std Drinks

### Export System (v5.4+)
- HTML export: template literal generator that builds a self-contained viewer
- Embeds: recipe JSON, GLASS_ICONS JSON, GLASS_ICON_MAP JSON, CSS, viewer JS
- Features: search, card grid, recipe view, scaling, substitutes, tick-off
- `</script>` inside template MUST be escaped as `<\/script>`
- Other formats: TXT, MD (### for subtitles), CSV (UTF-8 BOM), JSON backup

### Offline & Sync
- localStorage caches: recipes, settings, bin, pending sync queue
- `pendingSync` array stores changes made offline
- `syncToGist()` processes queue when online
- Offline banner shows when navigator.onLine is false

---

## Key Learnings & Gotchas

### String Replacement
Unicode escapes (`\u2715` vs `&#x2715;` vs literal `✕`) cause mismatches in Python string replacement. Always use `str_replace` tool or line-based editing with exact content from the file. Never assume what the file contains — view it first.

### Template Literal Escaping
`</script>` inside a JS template literal breaks the browser's HTML parser (it terminates the outer `<script>` block before JS runs). Must escape as `<\/script>`. The backslash is ignored by JS but prevents HTML parser matching.

### Confirm Dialogs
When the section-name-overlay was given `class="confirm-overlay"` (same as the confirm dialog), `querySelector('.confirm-btns')` found the wrong element. All showConfirm selectors MUST be scoped to `#confirm-overlay`.

### Settings Variable Scoping
`const` inside `if` blocks is block-scoped in JavaScript. Referencing outside the block causes a ReferenceError, silently caught by try/catch, producing mysterious failures. This caused the "Connection failed" bug in v4.3.

### iOS Safari Selects
iOS Safari ignores `height` on native `<select>` elements. Fix: `-webkit-appearance:none` + `appearance:none` + custom SVG chevron as background-image + explicit padding.

### GitHub Token Security
Sharing a GitHub personal access token gives the recipient access to ALL Gists on that account (not just one). This is a fundamental security issue with the current auth model. Benny's Gist ID is baked into the HTML source. This drove the decision to pursue C138/C139 (auth overhaul).
