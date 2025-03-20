# Git Notes Migration Guide

- This document explains how to migrate `git notes` after rewriting commit history using `git-filter-repo`. 
- The accompanying script transfers notes to new commit SHAs while cleaning up orphaned notes.

## Prerequisites

1. `git-filter-repo` must have been run previously
2. Commit map file must exist at `.git/filter-repo/commit-map`
3. `git notes` must have been used in the repository


## When to Use

- After running `git-filter-repo` that changed commit SHAs.
- When we need to preserve `git notes` across associated commits.
- When `git notes` appear orphaned after repository cleanup.

## Script Implementation

#### 1. Backup Current Notes

First, create a temporary backup of existing notes:
```bash
git notes list > git-notes.txt
```

#### 2. Load Commit Mapping

Create associative array from `git-filter-repo`'s commit-map:

```bash
declare -A COMMIT_MAP
while read -r OLD NEW; do
  COMMIT_MAP["$OLD"]="$NEW"
done < .git/filter-repo/commit-map
```

#### 3. Note Migration Logic

Core migration process with error handling:

```bash
while read -r NOTE_BLOB OLD_COMMIT; do
  NEW_COMMIT="${COMMIT_MAP[$OLD_COMMIT]}"
  if [ -n "$NEW_COMMIT" ]; then
    NOTE_CONTENT=$(git show "$NOTE_BLOB")
    # Attaching the note to the new commit SHA
    echo "$NOTE_CONTENT" | git notes add --force -F - "$NEW_COMMIT"
    # Removes the note from the old commit to avoid duplication
    git notes remove "$OLD_COMMIT" 2>/dev/null
  fi
done < git-notes.txt
```

#### 4. Cleanup

Remove temporary files and verify:

```bash
rm git-notes.txt
git notes list | wc -l
```

## Full Script

Save this as `migrate-notes.sh`:

```bash
#!/bin/bash

# ========================================================================
# Title : Git Notes Migration Script
# Purpose : Migrate git notes after history rewriting with git-filter-repo
# Repository : https://github.com/jishnukoliyadan/GIT_Notes
# License : MIT License
# Usage : ./migrate-notes.sh
# ========================================================================

# Backup current notes to file
NOTE_COUNT_INIT=$(git notes list | wc -l)
BACKUP_FILE="notes-backup-$(date '+%Y%m%d%H%M').txt"
git notes list >"$BACKUP_FILE"

# Map old commit SHAs to new commit SHAs
declare -A COMMIT_MAP
while read -r OLD NEW; do
    COMMIT_MAP["$OLD"]="$NEW"
done <.git/filter-repo/commit-map

# Migrate git notes new commit SHAs
echo "                  Old       New        Blob"
while read -r NOTE_BLOB OLD_COMMIT; do
    NEW_COMMIT="${COMMIT_MAP[$OLD_COMMIT]}"
    if [ -n "$NEW_COMMIT" ]; then
        NOTE_CONTENT=$(git show "$NOTE_BLOB")
        # Attaching the note to the new commit SHA
        echo "$NOTE_CONTENT" | git notes add --force -F - "$NEW_COMMIT"
        # Removes the note from the old commit to avoid duplication
        git notes remove "$OLD_COMMIT" 2>/dev/null
        echo "Migrated note: ${OLD_COMMIT:0:7} -> ${NEW_COMMIT:0:7} -> ${NOTE_BLOB:0:7}"
    fi
done <"$BACKUP_FILE"

# Cleanup temporary files
rm "$BACKUP_FILE"
NOTE_COUNT_FINAL=$(git notes list | wc -l)
echo "Migrated $NOTE_COUNT_INIT/$NOTE_COUNT_FINAL notes."

```

## Usage Instructions

1. Make script executable:

   ```bash
   chmod +x migrate-notes.sh
   ```

2. Run migration:

   ```bash
   ./migrate-notes.sh
   ```

3. Verify results:

   ```bash
   git log --show-notes
   ```
