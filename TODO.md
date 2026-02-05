# Zotero Watch Folder - TODO

## Required Before Release

### Icons - COMPLETE ✓
- [x] Create `content/icons/watch-folder-16.png` - 16x16 icon for preference pane
- [x] Create `content/icons/watch-folder-48.png` - 48x48 icon for add-ons manager
- [x] Create `content/icons/watch-folder-96.png` - 96x96 icon for add-ons manager
- [x] Create `content/icons/watch-folder.svg` - Source SVG file

**Design:** Teal folder icon with crow's eye - representing intelligent watching/monitoring.

---

## Phase 1 Complete

- [x] Infrastructure (manifest, bootstrap, prefs, localization)
- [x] F1.1 Watch Configuration (preferences UI)
- [x] F1.2 Auto-Import (polling, scanning, importing, tracking)
- [x] F1.3 Auto-Metadata (queue, throttle, _needs-review tag)
- [x] F1.4 Auto-Rename (template patterns, sanitization)
- [x] F1.5 First Run (detection, prompt, batch import)
- [x] Integration: Post-import actions (leave/delete/move)
- [x] Integration: Auto-rename after metadata retrieval

---

## Phase 2 Complete

### Collection ↔ Folder Mirroring
- [x] F2.1 Collection → Folder Sync (collectionSync.mjs, pathMapper.mjs)
- [x] F2.2 Item Movement Sync (collectionWatcher.mjs)
- [x] F2.3 Folder → Collection Sync (folderWatcher.mjs)
- [x] F2.4 Conflict Resolution (conflictResolver.mjs, syncState.mjs)

**New Modules (6 files):**
- `collectionSync.mjs` - Main sync coordinator
- `syncState.mjs` - State persistence
- `collectionWatcher.mjs` - Zotero notifier observer
- `folderWatcher.mjs` - Disk polling
- `pathMapper.mjs` - Collection ↔ folder mapping
- `conflictResolver.mjs` - Conflict handling

---

## Phase 3 Complete

### Advanced Features
- [x] F3.1 Smart Rules Engine (smartRules.mjs)
- [x] F3.2 Duplicate Detection (duplicateDetector.mjs)
- [x] F3.3 Bulk Operations (bulkOperations.mjs)

**New Modules (3 files):**
- `smartRules.mjs` - Rule evaluation and execution engine
- `duplicateDetector.mjs` - Pre-import duplicate detection (DOI, ISBN, title, hash)
- `bulkOperations.mjs` - Mass reorganize, metadata retry, rules application

---

## All Phases Complete!

The plugin now includes:
- **Phase 1**: Core watch folder functionality (auto-import, metadata, rename)
- **Phase 2**: Collection ↔ folder mirroring (bidirectional sync)
- **Phase 3**: Advanced features (smart rules, duplicate detection, bulk ops)
