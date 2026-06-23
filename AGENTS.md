# OpenCode Session Context — ISBN Checker

## Goal
Build and maintain four brand-specific transmission checker apps (Mustang, Jaguar, Nano, Synthbee) with a shared codebase.

## Constraints & Preferences
- All four HTML files must stay structurally identical for easy syncing.
- No external libraries or build tools.
- Prefer minimal, self-contained implementations.

## Progress
### Done
- Undo stack with toast notifications (replaced all `alert()` calls).
- Click-to-copy on `.isbn-cell` (guarded by `isValidIsbn(normalizeIsbn(txt))` so date cells aren't copied).
- "Export All Apps" button that reads all 4 IndexedDB databases into one CSV (`App,ISBN,Date,Date Added`).
- Import accepts `ISBN,Date`, `App,ISBN,Date`, and `App,Date,ISBN` column orders.
- Auto-backup to `localStorage` (`transmission_backup` key, keeps last 10 snapshots) triggered by every `dbPut`, `dbPutBatch`, `dbDelete`, `dbClear`.
- Backup banner (`#backupBanner`) shows per-app backup via `getLatestBackup()`, which filters by current app name. Restore removes the backup entry. Dismiss stores timestamp in `sessionStorage` so it stays hidden for the session.
- `dbPut(record, skipBackup)` accepts optional `skipBackup` flag to suppress backup during restore.
- Look Up Titles button in all three panels (Database, Check ISBN, Browse by Date). Title column added to all three tables.
- `lookupTitles()` uses Open Library API (`jscmd=data`, batches of 10, 200ms delay) and stores title in the IndexedDB record.
- Context-aware lookup: Check tab uses current check results, Browse by Date uses current date's ISBNs, Database tab uses all records.
- `toISODate()` normalizes dates in all exports.
- Top 10 Transmission Dates removed from Stats tab.
- Tooltips added to stat cards and Monthly Breakdown header.
- Duplicate `id="undoBtn"` in database panel removed.
- `deleteAllForDate()` now calls `saveBackup()` and `updateStorageMeter()`.
- `isbn-cell` class removed from Monthly Breakdown month cells.
- Filter input debounced (250ms debounce via `debouncedRenderTable`).
- Storage meter refreshed after `dbPut`, `dbDelete`, `dbClear`, `dbPutBatch`, and `deleteAllForDate`.
- CSV import now collects `oldRecords` before modifications and pushes undo entry.
- Batch date field no longer resets to today if user already set a date.
- Dead CSS removed (`.toast-container`, stale `.toast` rules with `@keyframes toast-in`).
- `normalizeDate()` now handles DD/MM/YYYY correctly: checks if either part exceeds 12 to disambiguate.
- Files moved to `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\`.
- Repository uploaded to GitHub.
- `AGENTS.md` renamed to `README.md`.

### In Progress
- (none)

### Blocked
- (none)

## Key Decisions
- Each app has a separate IndexedDB database (`MustangDB`, `JaguarDB`, `NanoDB`, `SynthbeeDB`) with the same store name `isbns` and version `2`.
- Export All opens each named DB directly via `openNamedDB()` rather than reusing the current app's connection.
- Backup uses `localStorage` for persistence even if IndexedDB is cleared.
- Open Library chosen because it requires no API key.
- Title lookup limits batches to 10 with 200ms delay to be polite to the API.

## Next Steps
- (none)

## Critical Context
- Open Library API endpoint: `https://openlibrary.org/api/books?bibkeys=ISBN:{isbn}&format=json&jscmd=data`
- Response format gives title at `data[key].title` (not `data[key].details.title`).
- `lookupTitles(isbns)` accepts optional ISBN array; otherwise falls back to `window._lastCheckIsbns` (set in `checkIsbn()`), then to panel-aware fallback (browse-by-date uses current date's ISBNs, else all DB).
- `normalizeDate()` handles `YYYY-MM-DD`, `MM/DD/YYYY`, `DD/MM/YYYY`, and two-digit year formats. When year is 4 digits and both parts ≤ 12, assumes MM/DD/YYYY.
- `toISODate()` in exports tries to fix YYYY-DD-MM → YYYY-MM-DD when month > 12.
- Backup entries are tagged with `app` name; `getLatestBackup()` filters by `getCurrentAppName()` to prevent cross-app contamination.
- `dbPut(record, skipBackup)` — `skipBackup=true` prevents backup creation (used during restore).
- `sessionStorage` key `dismissed_backup_{ts}` prevents dismissed backup banner from reappearing during session.
- All files live in `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\`.

## Relevant Files
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\Mustang.html`: primary app (all features listed)
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\Jaguar.html`: identical structure, same features
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\Nano.html`: identical structure, same features
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\Synthbee.html`: identical structure, same features
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\index.html`: simple landing page linking to all 4 apps
- `C:\Users\Windows 10\Documents\_VSCode\TransmissionChecker\README.md`: agent instructions (renamed from AGENTS.md)
