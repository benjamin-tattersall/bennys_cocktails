# Benny's Cocktails — Claude Code Instructions

## Project Overview
Single-file HTML PWA cocktail recipe book. Originally forked from Benny's Recipes v1.43 (a general recipe app). GitHub Gist backend, offline support, dark theme with customisable accent colour. iPhone-primary, desktop-responsive. Oriented toward personal use and bar staff.

**Owner:** Benny (graphic design background, works in hospitality)

## File Structure
```
bennys-cocktails/
  CLAUDE.md              ← This file (read automatically by Claude Code)
  index.html             ← Production (Baked Gist: 46caec02a3b745a1554de6553a390226)
  dev.html               ← Development (Baked Gist: 760345d2785176782856adb3325c2b74)
  docs/
    CONTEXT.md           ← Full project context, backlog, architecture, history
    transcripts/         ← Chat transcripts from the Claude.ai build sessions
```

## Development Workflow

### Branch Strategy
- `main` — production. Always stable. Friends use this. Never commit directly to main.
- `feature/<name>` — all development work happens here (e.g. `feature/c141-folders`)
- When Benny approves a feature: merge PR to main → push to prod → push to remote

### Making Changes
1. All changes are made to `dev.html` on a feature branch — NEVER edit index.html directly
2. Benny tests dev.html on desktop + iPhone (frequently uses incognito windows for viewer testing)
3. When Benny says "push to prod" → Claude:
   a. Merges the feature branch PR to main
   b. Produces `index.html` from `dev.html` — swapping BAKED_GIST_ID only
   c. Commits both files to main
   d. Pushes to origin/main (`git push origin main`)
4. The ONLY difference between the two files is the BAKED_GIST_ID constant

### Delivery Checklist (EVERY change)
- [ ] Version bump: update `const APP_VERSION`
- [ ] CHANGELOG entry with `userNotes` array AND `viewerNotes` array (even if empty `[]`)
- [ ] Parse test: extract JS from `<script>` block, wrap in IIFE, run `node --check`
- [ ] Consider dual-view impact (admin vs viewer)
- [ ] Mark new UI elements with `class="admin-only"` where appropriate
- [ ] Update `docs/CONTEXT.md` with changes, backlog updates, testing notes
- [ ] When pushing to prod: produce BOTH index.html and dev.html, then `git push origin main`

### Parse Test Command
```bash
# Windows-safe: write temp file to repo dir (Bash tool context)
node -e "
const fs = require('fs');
const c = fs.readFileSync('C:/Repos/bennys_cocktails/dev.html', 'utf8');
const js = c.slice(c.indexOf('<script>') + 8, c.lastIndexOf('</script>'));
fs.writeFileSync('C:/Repos/bennys_cocktails/pf_tmp.js', '(function() {\n' + js + '\n});\n');
" && node --check "C:/Repos/bennys_cocktails/pf_tmp.js" && echo "PASS" || echo "FAIL"
rm -f "C:/Repos/bennys_cocktails/pf_tmp.js"
```
Note: `/tmp` does not exist on this Windows setup — always use the repo dir for the temp file.

## Key Architecture

### Single-file PWA
- All CSS, JS, and HTML in one file (~496KB, ~9100 lines)
- No build step, no dependencies, no framework
- Two copies: index.html (prod) + dev.html (dev) — identical except BAKED_GIST_ID

### Data Storage
- GitHub Gist backend: `recipes.json`, `settings.json`, `bin.json`, `backup.txt`, `backup.csv`
- localStorage cache for offline support
- `buildSettingsObj()` / `applySettingsObj()` handle settings read/write

### Access Model
- `VIEW_ONLY` flag controls entire access mode
- `.admin-only` CSS class hides elements for viewers
- `applyAccessMode()` toggles visibility
- `applyViewerInfoContent()` rewrites info tabs for viewer context
- BAKED_GIST_ID: hardcoded Gist ID for default viewer database
- Access codes: `[{code, label}]` in settings.json, validated by `connectAsViewer()`

### Critical Patterns (bugs will occur if violated)
- **Confirm dialogs**: `showConfirm()` selectors MUST be scoped to `#confirm-overlay` — the section-name-overlay shares the `.confirm-overlay` CSS class
- **HTML export**: `</script>` tags inside the template literal MUST be escaped as `<\/script>` — the browser's HTML parser terminates the outer script block otherwise
- **iOS selects**: Must use `-webkit-appearance:none` + custom SVG chevron + explicit padding for proper height
- **Settings variable scoping**: `const` inside `if` blocks is block-scoped — referencing outside causes silent ReferenceError
- **String replacement**: Unicode escapes (`\u2715` vs `&#x2715;` vs literal `✕`) cause mismatches — always use `str_replace` or line-based editing with exact content verification
- **Data migration**: Defensive/additive only — new fields default to null, old fields never deleted, settings merge on load

### Accent Colour System
```javascript
const ACCENT_PALETTE = [
  { name: 'Deep Blue',  accent: '#4080c8', dim: '#3068a8', light: '#1a2535', text: '#70a8e0' },
  { name: 'Copper',     accent: '#c87040', dim: '#a85830', light: '#3a2a20', text: '#e8a070' },
  { name: 'Emerald',    accent: '#40a868', dim: '#308850', light: '#1a3525', text: '#70d898' },
  { name: 'Purple',     accent: '#8858c8', dim: '#7040a8', light: '#2a1a35', text: '#b888e8' },
  { name: 'Rose',       accent: '#c85070', dim: '#a84058', light: '#351a25', text: '#e87898' },
  { name: 'Amber',      accent: '#c89840', dim: '#a87830', light: '#352a1a', text: '#e8c070' },
  { name: 'Teal',       accent: '#40a8a8', dim: '#308888', light: '#1a3535', text: '#70d8d8' },
  { name: 'Slate',      accent: '#6880a0', dim: '#506888', light: '#1a2030', text: '#90a8c8' },
];
```

## Benny's Preferences & Communication Style
- Provides numbered notes for feedback; "smash it out" signals readiness for a batch of work
- Values clean mobile UI — no overflow, no button displacement, consistent heights
- Tests on both desktop and iPhone Safari; uses incognito windows for viewer testing
- Has a graphic design background — collaborative icon/design work benefits from rendering visuals for review
- Prefers discussing design decisions before implementation for complex features
- Wants comprehensive context files maintained — gets frustrated when detail is lost
- Appreciates honest assessment when something isn't working vs trying incremental patches
