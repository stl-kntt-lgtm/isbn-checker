# ISBN Checker — Agent Instructions

## Sources of truth

- **`README.md`** — architecture, schema, constraints. Read first.
- The code itself (4 × ~1811-line single-file HTML apps in this repo).

## Setup

No build, no install, no tests, no linter. Open any `.html` directly in a browser (file:// or static server).

## Hard constraint

**All 4 HTML files must stay structurally identical.** Only these differ per file:
- `<title>`
- `DB_NAME` constant (`MustangDB` / `JaguarDB` / `NanoDB` / `SynthbeeDB`)
- Display name in headings and PWA manifest

**Edit `Mustang.html` first** (it's the primary/lead file), then replicate every change to the other 3. Use diff to verify they stay in sync.

## Key conventions

| What | Detail |
|------|--------|
| Globals | `$`-prefix: `$_tabs`, `$_sortState`, `$_lastCheckIsbns`, `$_debouncedRenderTable`, etc. |
| Notifications | `showToast(msg, type)` — never `alert()` |
| Undo | `pushUndo(records, desc)` / `performUndo()` — stack max 20 |
| Backup | `localStorage` key `transmission_backup`, keeps 10 snapshots, tagged per app |
| Theme | `localStorage` key `isbn_theme` |
| Open Library | `https://openlibrary.org/api/books?bibkeys=ISBN:{isbn}&format=json&jscmd=data` — title at `data[key].title`. Batch 10, delay 200ms. |
| Dates | `normalizeDate()`: 4-digit year with both parts ≤12 → assumes MM/DD/YYYY. `toISODate()`: fixes YYYY-DD-MM → YYYY-MM-DD when month > 12. |
| Export All | `openNamedDB(app + 'DB')` — reads all 4 DBs, not just current |
| Conflict modal | Shown during CSV import for duplicate ISBNs (Add Date / Overwrite / Skip / Skip All) |
| Keyboard | `Ctrl+Enter` check ISBNs, `Ctrl+F` focus DB filter, `Ctrl+E` export CSV |

## Per-app IndexedDB

| App | DB name | Version | Store | keyPath |
|-----|---------|---------|-------|---------|
| Mustang | `MustangDB` | 2 | `isbns` | `isbn` |
| Jaguar | `JaguarDB` | 2 | `isbns` | `isbn` |
| Nano | `NanoDB` | 2 | `isbns` | `isbn` |
| Synthbee | `SynthbeeDB` | 2 | `isbns` | `isbn` |

Index: `dates` on `dates[].date` (multiEntry).

Record shape: `{ isbn: string, title?: string, dates: Array<{ date: string, added: string }> }`.
