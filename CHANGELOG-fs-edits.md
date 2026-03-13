# Changelog – fs-edits branch

Log of changes made:

---

## Change 1: Add prefix parsing helpers

**Files modified:** `layouts/_default/example-viz.html`

**Changes:**
- Added `COLUMN_PREFIXES` constant: `dim::`, `med::`, `desc::`, `res::`
- Added `parseColumnPrefix(header)` – returns `{ type, displayName, key }` or `null`
- Added `getDisplayName(header)` – returns display name (strips prefix)
- Added `dimKey(dim)` – returns `dim.key` or `dim.name` for row lookups

**Behavior:** No change yet; helpers are unused.

---

## Change 2: Support `dim::` in analyzeDimensions


**Files modified:** `layouts/_default/example-viz.html`

**Changes:**
- Updated `analyzeDimensions` to check for `dim::` prefix first; if present, include as dimension
- Columns with `med::`, `desc::`, `res::` are excluded when prefix is present
- Dimensions now have `name` (display) and `key` (for row lookup)
- Added `key` to `mergeValueSpaces` output
- Replaced all row lookups to use `dimKey(dim)` instead of `dim.name` (dummy rows, drag-drop, filters, color/shape, modal)

**Behavior:** Columns with `dim::` prefix (e.g. `dim::Function`) are treated as dimensions. Display shows name without prefix. Existing CSVs without prefixes work unchanged.

---

## Change 3: Support `med::` for media columns

**Files modified:** `layouts/_default/example-viz.html`

**Changes:**
- Updated `isMediaRelatedColumn` to return true when header has `med::` prefix
- Updated `detectMediaColumns` to detect `med::` prefixed columns and add them to mediaColumns (using full header as key for row lookup)
- Media type inferred from display name (e.g. `med::audio_url`, `med::image_url`)
- Updated `getRowMedia` label to use `getDisplayName()` for cleaner display in media section

**Behavior:** Columns with `med::` prefix (e.g. `med::audio_url`, `med::image_url`) are treated as media. Audio/image/video show in the media section when clicking a point. Heuristic detection (audio_url, image_url, etc.) still works as fallback.

---

## Change 4: Support `desc::` and `res::`

**Files modified:** `layouts/_default/example-viz.html`

**Changes:**
- Added `shouldExcludeFromTooltip(key)` – excludes `desc::`, `res::`, `med::`, and heuristic reserved/media
- Added `shouldExcludeFromModalTable(key)` – excludes `res::` and `med::` only (`desc::` shown in modal)
- Added `getModalTitleFromRow(row)` – finds title/name from any column (including `res::title`, `res::name`)
- Updated tooltip (dummy and normal) to use `shouldExcludeFromTooltip` and `getDisplayName` for labels
- Updated modal to use `getModalTitleFromRow`, `shouldExcludeFromModalTable`, and `getDisplayName` for column labels
- Updated `validateAnchors` to use `shouldExcludeFromTooltip`

**Behavior:** 
- `desc::` columns (gloss, transcription, id): Excluded from dimensions and tooltip; shown in modal table
- `res::` columns (title, description): Excluded from dimensions, tooltip, and modal table; `res::title` and `res::name` used for modal header

---

## Change 5: Apply same changes to index.html

**Files modified:** `layouts/index.html`

**Changes:** Mirrored all Steps 1–4 in the full-editor layout (index.html): prefix helpers, dim::, med::, desc::, res::, row lookups, tooltip, modal.

---

## Change 6: Fallback for gloss/transcription/translation columns

**Files modified:** `layouts/_default/example-viz.html`, `layouts/index.html`

**Changes:**
- Added `DESC_LIKE_COLUMNS`: `['gloss', 'glossing', 'transcription', 'transcript', 'translation', 'meaning', 'description', 'text', 'sentence']`
- In `analyzeDimensions` fallback: exclude these column names from dimensions (even when values are not all unique)
- In `shouldExcludeFromTooltip` fallback: exclude these from tooltip

**Behavior:** Columns named gloss, glossing, transcription, transcript, translation, meaning, description, text, sentence (without prefix) are treated like `desc::` columns: excluded from dimensions and tooltip, shown in modal. Explicit prefix (`dim::gloss`) always wins over fallback.

---

## Change 7: Change behavior of the Reset All option

**Files modified:** `layouts/_default/example-viz.html`, `layouts/index.html`, `layouts/shortcodes/visualizer-test.html`, `layouts/_default/full-visualizer.txt`

**Changes:**
- Added confirmation dialog before `resetAllChanges()` runs
- User must confirm with "OK" to revert; "Cancel" aborts and keeps changes

**Behavior:** Clicking "Reset All" now shows a popup: "Reset all changes? All your modifications will be reverted and cannot be undone. Are you sure you want to continue?" This prevents accidental loss of work.

---

## Change 8: Support multiple delimiters (tab, semicolon, pipe)

**Files modified:** `layouts/index.html`, `layouts/_default/example-viz.html`, `layouts/shortcodes/visualizer-test.html`, `layouts/_default/full-visualizer.txt`, `tools/local-visualizer.html`, `public/examples/*/index.html`

**Changes:**
- Added `detectDelimiter(lines)` – scans the first non-empty line and picks the delimiter with the highest count outside quotes; candidates: `,`, `\t`, `;`, `|`
- Replaced `parseCSVLine(line)` with `parseDelimitedLine(line, delimiter)` – parses using the given delimiter instead of hardcoded comma
- Updated `parseCSV(text)` to detect delimiter first, then parse all lines with it; handles empty input; normalizes line endings (`\r\n` → `\n`)

**Behavior:** Uploaded files can use comma, tab, semicolon, or pipe as the field separator. The app auto-detects from the header row. Export still uses comma-separated format.

---

## Change 9: Stop YouTube/video playback when modal closes

**Files modified:** `layouts/index.html`, `layouts/_default/example-viz.html`, `layouts/shortcodes/visualizer-test.html`, `layouts/_default/full-visualizer.txt`, `tools/local-visualizer.html`, `public/examples/*/index.html`

**Changes:**
- In `closeModal()`, added logic to clear the `src` of any YouTube iframes inside the modal, in addition to pausing `video` and `audio` elements

**Behavior:** Closing the record modal no longer leaves YouTube or other embedded videos playing in the background. Video and audio playback stops when the modal is closed.

---

## Change 10: Save grid snapshot as PNG

**Files modified:** `layouts/index.html`, `layouts/_default/example-viz.html`, `layouts/shortcodes/visualizer-test.html`, `layouts/_default/full-visualizer.txt`, `tools/local-visualizer.html`

**Changes:**
- Added html2canvas (CDN) for DOM-to-image capture
- Added "Snapshot" button in viz-actions and floating controls
- Added `downloadSnapshot()` – captures the panel grid as PNG and triggers download

**Behavior:** Users can save a snapshot of the current grid visualization as a PNG image. Works in both edit and view modes. Requires network for html2canvas CDN on first use.