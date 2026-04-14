---
name: cleanup-scratch
description: Scan a scratch directory for stale files, categorize by type, report size, and delete with confirmation
user_invocable: true
---

# Cleanup Scratch

Scan a scratch/temp directory for stale files, categorize them, and delete with confirmation.

## Configuration

```
SCRATCH_DIR = ~/scratch/
STALE_DAYS  = 14
```

Update `SCRATCH_DIR` to your temp/scratch directory path.

## Process

### 1. Scan for stale files

Find files older than `STALE_DAYS`:

```bash
find "$SCRATCH_DIR" -type f -mtime +$STALE_DAYS -printf '%T@ %s %f\n' | sort -n
```

### 2. Categorize

Group by extension:

| Category | Extensions |
|----------|-----------|
| Screenshots | `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp` |
| Scripts | `.py`, `.js`, `.ts`, `.sh`, `.bash` |
| Data | `.json`, `.jsonl`, `.csv`, `.yaml`, `.yml` |
| Documents | `.md`, `.html`, `.txt`, `.pdf` |
| Other | everything else |

### 3. Report

Show total count and size per category:

```
Stale files in ~/scratch/ (older than 14 days):

Screenshots:  12 files,  45 MB
Scripts:       3 files,  12 KB
Data:          8 files, 2.1 MB
Documents:     5 files,  89 KB
Other:         2 files, 340 KB

Total: 30 files, 47.5 MB
```

### 4. Confirm before deleting

Ask the user which categories to delete. Never delete without confirmation.

### 5. Delete and report

```
Deleted 12 screenshots, 8 data files.
Freed 47.1 MB.
Kept 10 files (scripts, documents, other).
```

## Rules

- **Always confirm** before deleting anything.
- If a file looks valuable (non-trivial script, recent data), flag it and suggest moving it to a permanent location instead of deleting.
- Report sizes in human-readable format (KB, MB, GB).
- If the scratch directory doesn't exist or is empty, say so and stop.
