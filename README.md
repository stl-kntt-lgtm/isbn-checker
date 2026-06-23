## Structure

- `index.html` — landing page linking to 4 app files.
- `Mustang.html`, `Jaguar.html`, `Nano.html`, `Synthbee.html` — four identical apps.
- No build tools, no package.json, no tests, no external libraries. Pure vanilla HTML/CSS/JS.

## Hard Constraint

**All 4 app HTML files must stay structurally identical** for easy syncing. Every edit made to one must be made to all four. The only intentional differences are:
- `<title>` text (e.g. `Mustang Transmission Checker`)
- `DB_NAME` constant (e.g. `MustangDB`)
- App display name in the UI

## IndexedDB Schema

- Each app uses a separate DB: `MustangDB`, `JaguarDB`, `NanoDB`, `SynthbeeDB` (all version `2`).
- Single object store `isbns` with keyPath `isbn`.
- Index on `dates[].date` named `dates` (multiEntry).
- Record shape: `{ isbn: string, title?: string, dates: Array<{ date: string, added: string }> }`.

## Key Conventions

- `$`-prefixed global variables: `$_tabs`, `$_sortState`, `$_lastCheckIsbns`, etc.
- Toast replaces all `alert()` calls.
- ISBNs displayed in cells with class `isbn-cell` — click-to-copy handler guards with `isValidIsbn(normalizeIsbn(txt))`.
- Open Library API for title lookup: `https://openlibrary.org/api/books?bibkeys=ISBN:{isbn}&format=json&jscmd=data` — batches of 10 with 200ms delay.
- Backup stored in `localStorage` key `transmission_backup` (last 10 snapshots).
- Export All opens each named DB directly via `openNamedDB(app + 'DB')`.
