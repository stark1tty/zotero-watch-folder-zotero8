# Zotero Watch Folder - Test Plan

Complete test cases to verify all plugin functionality.

## Setup

### Test Environment
1. Create test folders:
```bash
mkdir -p ~/ZoteroWatchTest/inbox
mkdir -p ~/ZoteroWatchTest/mirror
mkdir -p ~/ZoteroWatchTest/processed
```

2. Download sample PDFs for testing (use any academic PDFs with DOIs):
   - PDF with DOI (e.g., from arXiv or any journal)
   - PDF without DOI (any random PDF)
   - Duplicate PDF (copy of one you'll import first)

3. Open Zotero Error Console: `Tools` → `Developer` → `Error Console`

---

## Phase 1: Core Watch Folder

### Test 1.1: Basic Configuration
- [ ] Open `Edit` → `Settings` → `Watch Folder`
- [ ] Verify the crow-eye icon appears in the preferences pane
- [ ] Verify all preference fields are visible:
  - Enable Watch Folder checkbox
  - Source Folder path with Browse button
  - Target Collection field
  - Poll Interval field
  - File Types field
  - Import Mode dropdown
  - Post-Import Action dropdown
  - Auto-Retrieve Metadata checkbox
  - Auto-Rename checkbox
  - Rename Pattern field

**Expected:** All UI elements render correctly.

---

### Test 1.2: Enable Watch Folder
- [ ] Set Source Folder to `~/ZoteroWatchTest/inbox`
- [ ] Set Target Collection to `TestImports`
- [ ] Set Poll Interval to `5` seconds
- [ ] Set File Types to `pdf`
- [ ] Set Import Mode to `Copy file to Zotero storage`
- [ ] Set Post-Import Action to `Leave file in place`
- [ ] Enable Auto-Retrieve Metadata
- [ ] Enable Auto-Rename
- [ ] Check "Enable Watch Folder"
- [ ] Click OK to save

**Expected:**
- Settings save without error
- Debug console shows: `[WatchFolder] Plugin started successfully`
- Debug console shows: `[WatchFolder] Started watching folder`

---

### Test 1.3: Auto-Import PDF
- [ ] Copy a PDF (with DOI if possible) to `~/ZoteroWatchTest/inbox`
- [ ] Wait 5-10 seconds

**Expected:**
- Debug console shows: `[WatchFolder] Found X new file(s)`
- Debug console shows: `[WatchFolder] Imported: filename.pdf`
- Item appears in Zotero under "TestImports" collection
- Original PDF remains in inbox folder

---

### Test 1.4: Metadata Retrieval
- [ ] Wait for metadata retrieval (up to 60 seconds)
- [ ] Check the imported item in Zotero

**Expected:**
- If DOI found: Item has title, authors, publication info
- If no DOI: Item tagged with `_needs-review`
- Debug console shows: `[WatchFolder] Queued item X for metadata retrieval`
- Debug console shows: `[WatchFolder] Recognition completed` or `timed out`

---

### Test 1.5: Auto-Rename After Metadata
- [ ] Check the attachment filename after metadata is retrieved

**Expected:**
- If metadata found: File renamed to pattern (e.g., `Author - 2024 - Title.pdf`)
- Debug console shows: `[WatchFolder] Renamed: "old.pdf" → "new.pdf"`

---

### Test 1.6: Post-Import Action - Delete
- [ ] Change Post-Import Action to `Delete file`
- [ ] Copy another PDF to inbox
- [ ] Wait for import

**Expected:**
- PDF imported to Zotero
- Original file DELETED from inbox folder
- Debug console shows: `[WatchFolder] Deleted source file`

---

### Test 1.7: Post-Import Action - Move
- [ ] Change Post-Import Action to `Move to folder`
- [ ] Set the move destination to `~/ZoteroWatchTest/processed`
- [ ] Copy another PDF to inbox
- [ ] Wait for import

**Expected:**
- PDF imported to Zotero
- Original file MOVED to `processed` folder
- Debug console shows: `[WatchFolder] Moved source file`

---

### Test 1.8: File Type Filtering
- [ ] Set File Types to `pdf`
- [ ] Copy a `.txt` file to inbox
- [ ] Wait 10 seconds

**Expected:**
- TXT file is NOT imported
- TXT file remains in inbox

---

### Test 1.9: Import Mode - Linked
- [ ] Change Import Mode to `Link to file in current location`
- [ ] Copy a PDF to inbox
- [ ] Wait for import

**Expected:**
- Item appears in Zotero
- Attachment is a LINK (not stored in Zotero storage)
- Original file in inbox is the actual file used

---

### Test 1.10: First Run Detection
- [ ] Disable Watch Folder
- [ ] Copy 3 PDFs to inbox
- [ ] Change Source Folder to a different path, then back to inbox
- [ ] Enable Watch Folder

**Expected:**
- Dialog appears asking to import existing files
- All 3 files are imported as a batch
- Debug console shows: `[WatchFolder] First run handled, imported 3 files`

---

## Phase 2: Collection ↔ Folder Sync

### Test 2.1: Enable Collection Sync
- [ ] In Settings → Watch Folder, find Collection Sync section
- [ ] Set Mirror Path to `~/ZoteroWatchTest/mirror`
- [ ] Create a collection called "MirrorTest" in Zotero
- [ ] Set Mirror Root Collection to "MirrorTest"
- [ ] Enable Bidirectional Sync
- [ ] Enable Collection Sync

**Expected:**
- Settings save without error
- Debug console shows: `[WatchFolder] Collection sync service initialized`

---

### Test 2.2: Collection → Folder Sync
- [ ] Create a sub-collection under "MirrorTest" called "Papers"
- [ ] Wait 10 seconds

**Expected:**
- Folder `~/ZoteroWatchTest/mirror/Papers` is created
- Debug console shows collection sync activity

---

### Test 2.3: Item → Folder Sync
- [ ] Drag an item with a linked file into "MirrorTest/Papers"
- [ ] Wait 10 seconds

**Expected:**
- The linked file appears in `~/ZoteroWatchTest/mirror/Papers/`

---

### Test 2.4: Folder → Collection Sync
- [ ] Create a folder `~/ZoteroWatchTest/mirror/NewFolder`
- [ ] Wait 10-15 seconds

**Expected:**
- Collection "NewFolder" appears under "MirrorTest" in Zotero
- Debug console shows: `[WatchFolder] Created collection from folder`

---

### Test 2.5: File → Item Sync
- [ ] Copy a PDF to `~/ZoteroWatchTest/mirror/NewFolder/`
- [ ] Wait 10-15 seconds

**Expected:**
- Item appears in Zotero under "MirrorTest/NewFolder"
- File is linked (not copied)

---

### Test 2.6: Conflict Detection
- [ ] Modify a synced file on disk
- [ ] Modify the same item's metadata in Zotero
- [ ] Wait for sync

**Expected:**
- Conflict is detected
- Resolution applied based on settings (Zotero wins / Disk wins / Newest wins)
- Debug console shows: `[WatchFolder] Conflict detected`

---

## Phase 3: Advanced Features

### Test 3.1: Duplicate Detection - DOI
- [ ] Import a PDF that has a DOI
- [ ] Note the DOI value
- [ ] Try to import another PDF with the SAME DOI

**Expected:**
- Second import is SKIPPED
- Debug console shows: `[WatchFolder] DOI match: 10.xxxx/xxxxx`
- Debug console shows: duplicate check result

---

### Test 3.2: Duplicate Detection - Title
- [ ] Import a PDF with a unique title
- [ ] Try to import another PDF with a very similar title (85%+ match)

**Expected:**
- Second import detected as potential duplicate
- Depending on settings: skipped or tagged with `_duplicate`
- Debug console shows title similarity percentage

---

### Test 3.3: Smart Rules - Create Rule
- [ ] Enable Smart Rules in preferences
- [ ] Create a rule via code (until UI is built):

```javascript
// Run in Zotero's Error Console (Tools → Developer → Run JavaScript)
const { getSmartRulesEngine } = ChromeUtils.importESModule(
  "chrome://zotero-watch-folder/content/smartRules.mjs"
);
const engine = getSmartRulesEngine();
await engine.init();

engine.addRule({
  name: "AI Papers to AI Collection",
  enabled: true,
  priority: 10,
  conditions: [
    { field: "title", operator: "contains", value: "artificial intelligence" }
  ],
  actions: [
    { type: "addToCollection", value: "AI Research" },
    { type: "addTag", value: "ai-paper" }
  ]
});

await engine.saveRules();
```

**Expected:**
- Rule is saved to preferences
- Debug console confirms rule added

---

### Test 3.4: Smart Rules - Rule Execution
- [ ] Import a PDF with "artificial intelligence" in the title
- [ ] Wait for import and rule processing

**Expected:**
- Item is automatically added to "AI Research" collection
- Item has tag `ai-paper`
- Debug console shows: `[WatchFolder] Rule "AI Papers to AI Collection" matched`

---

### Test 3.5: Smart Rules - Skip Import
- [ ] Create a rule with `skipImport` action for files containing "draft" in filename
- [ ] Try to import a file named `draft-paper.pdf`

**Expected:**
- File is NOT imported
- Debug console shows: `[WatchFolder] Skip import triggered by rule`

---

### Test 3.6: Bulk Operations - Reorganize
```javascript
// Run in Zotero's Error Console
const { reorganizeAll } = ChromeUtils.importESModule(
  "chrome://zotero-watch-folder/content/bulkOperations.mjs"
);

// Dry run first
const dryResult = await reorganizeAll({
  dryRun: true,
  onProgress: (p) => Zotero.debug(`Progress: ${p.current}/${p.total} - ${p.currentItem}`)
});
Zotero.debug(`Would rename ${dryResult.success} files`);
```

**Expected:**
- Dry run shows what files would be renamed
- No actual changes made

---

### Test 3.7: Bulk Operations - Retry Metadata
```javascript
// Run in Zotero's Error Console
const { retryAllMetadata } = ChromeUtils.importESModule(
  "chrome://zotero-watch-folder/content/bulkOperations.mjs"
);

const result = await retryAllMetadata({
  dryRun: false,
  onProgress: (p) => Zotero.debug(`Progress: ${p.current}/${p.total}`)
});
Zotero.debug(`Retried ${result.success} items`);
```

**Expected:**
- Items with `_needs-review` tag are queued for metadata retry
- Progress is reported

---

### Test 3.8: Bulk Operations - Apply Rules
```javascript
// Run in Zotero's Error Console
const { applyRulesToAll } = ChromeUtils.importESModule(
  "chrome://zotero-watch-folder/content/bulkOperations.mjs"
);

const result = await applyRulesToAll({
  dryRun: true,
  onProgress: (p) => Zotero.debug(`${p.current}/${p.total}: ${p.currentItem}`)
});
Zotero.debug(`Rules would affect ${result.success} items`);
```

**Expected:**
- Shows which existing items would match rules
- Dry run makes no changes

---

## Edge Cases

### Test E.1: Special Characters in Filename
- [ ] Import a PDF with special characters: `Test (2024) [Final] - Résumé.pdf`

**Expected:**
- File imports successfully
- Filename is sanitized if needed

---

### Test E.2: Very Long Filename
- [ ] Import a PDF with a very long filename (200+ characters)

**Expected:**
- File imports successfully
- Filename is truncated to max length (150 by default)

---

### Test E.3: Empty Folder
- [ ] Point watch folder to an empty directory
- [ ] Wait 10 seconds

**Expected:**
- No errors
- Debug console shows scan completed with 0 files

---

### Test E.4: Rapid Multiple Files
- [ ] Copy 5 PDFs to inbox simultaneously

**Expected:**
- All 5 files are imported
- No duplicates
- No crashes

---

### Test E.5: Plugin Disable/Enable
- [ ] Disable the watch folder
- [ ] Add files to inbox
- [ ] Re-enable watch folder

**Expected:**
- Files added while disabled are detected and imported
- No duplicate imports

---

### Test E.6: Zotero Restart
- [ ] With watch folder enabled, restart Zotero
- [ ] Check if watching resumes

**Expected:**
- Plugin loads on startup
- Watching resumes automatically
- Debug console shows: `[WatchFolder] Plugin started successfully`

---

## Cleanup

After testing, clean up:
```bash
rm -rf ~/ZoteroWatchTest
```

And remove test collections from Zotero.

---

## Test Results Summary

| Phase | Test | Status | Notes |
|-------|------|--------|-------|
| 1.1 | Configuration UI | ⬜ | |
| 1.2 | Enable Watch | ⬜ | |
| 1.3 | Auto-Import | ⬜ | |
| 1.4 | Metadata Retrieval | ⬜ | |
| 1.5 | Auto-Rename | ⬜ | |
| 1.6 | Post-Import Delete | ⬜ | |
| 1.7 | Post-Import Move | ⬜ | |
| 1.8 | File Type Filter | ⬜ | |
| 1.9 | Linked Import | ⬜ | |
| 1.10 | First Run | ⬜ | |
| 2.1 | Enable Sync | ⬜ | |
| 2.2 | Collection → Folder | ⬜ | |
| 2.3 | Item → Folder | ⬜ | |
| 2.4 | Folder → Collection | ⬜ | |
| 2.5 | File → Item | ⬜ | |
| 2.6 | Conflict Detection | ⬜ | |
| 3.1 | Duplicate DOI | ⬜ | |
| 3.2 | Duplicate Title | ⬜ | |
| 3.3 | Smart Rules Create | ⬜ | |
| 3.4 | Smart Rules Execute | ⬜ | |
| 3.5 | Smart Rules Skip | ⬜ | |
| 3.6 | Bulk Reorganize | ⬜ | |
| 3.7 | Bulk Retry Metadata | ⬜ | |
| 3.8 | Bulk Apply Rules | ⬜ | |
| E.1 | Special Characters | ⬜ | |
| E.2 | Long Filename | ⬜ | |
| E.3 | Empty Folder | ⬜ | |
| E.4 | Rapid Files | ⬜ | |
| E.5 | Disable/Enable | ⬜ | |
| E.6 | Zotero Restart | ⬜ | |

**Legend:** ⬜ Not tested | ✅ Pass | ❌ Fail
