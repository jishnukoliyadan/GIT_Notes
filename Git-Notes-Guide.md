# Git Notes Guide

Git notes allow you to add additional information (metadata, annotations, comments, build results, etc.) to commits without changing the commit history. These notes are stored in a separate namespace.

## Basic Commands

- By default, notes are stored under the `commits` namespace.

  ### 1. Add a Note

  ```bash
  git notes add                     # Add note interactively
  git notes add -m "Your note here" # Add note with direct message
  ```

  ### 2. Edit / Show / Remove Notes (Latest Commit)

  ```bash
  git notes edit   # Edit for HEAD
  git notes show   # Show for HEAD
  git notes remove # Remove from HEAD
  ```

  ### 3. Add / Edit / Show / Remove Notes (Specific Commit)

  ```bash
  # For specific commits (using commit SHA)

  git notes add <commit-id>
  git notes edit <commit-id>
  git notes show <commit-id>
  git notes remove <commit-id>
  ```

## Checking Git Notes in Repositories

### 1. Local Repo

- #### 1.1. List references using `Git`

  - Displays references available in a local repository along with the associated commit IDs.

  - `git notes` namespaces will lists under `refs/notes/` entries in the output.

    ```bash
    # List all references in the local repo, including notes
    git show-ref

    # List only references related to notes
    git for-each-ref refs/notes/
    ```

- #### 1.2. List notes only references using `ls`

  - Exploring inside the `.git/` directory, specifically under `.git/refs/notes/`.

  - This will show the files or references stored in that directory.

    ```bash
    ls .git/refs/notes/
    ```

### 2. Remote Repo

- If the README or documentation doesn't mention Git Notes, we can still check if they exist in the remote.
- If the below returns output, the remote repository has notes under one or more namespaces.

  ```bash
  git ls-remote origin "refs/notes/*"
  ```


## Working with Namespaced Notes

- By default, notes are stored under the `commits` namespace.

- We can define custom namespaces using the `--ref` option to to organize notes.

  ### 1. Add Notes in a Custom Namespace

  ```bash
  git notes --ref <namespace> add -m "build pass"

  # Use a custom namespace "review"
  git notes --ref review add -m "review note for commit" <commit-id>
  # OR
  git notes --ref refs/notes/review add -m "review note for commit" <commit-id>
  ```

  ### 2. Show / Remove Notes in a Custom Namespace

  ```bash
  # Show namespaced notes
  git notes --ref review show <commit-id>

  # Remove from namespaced notes
  git notes --ref review remove <commit-id>
  ```

## Fetch Notes from Remote

- Git does **not fetch notes by default**. To fetch notes :

  ```bash
  git fetch origin refs/notes/commits:refs/notes/commits # Fetch default namespace
  git fetch origin refs/notes/review:refs/notes/review   # Fetch custom namespace
  git fetch origin "refs/notes/*:refs/notes/*"           # Fetch all note namespaces
  ```

## Pushing Notes to Remote

- Like tags, Git Notes are **not pushed by default**. We must explicitly push them :

  ```bash
  git push origin refs/notes/commits  # Push default namespace
  git push origin refs/notes/review   # Push custom namespace
  git push origin "refs/notes/*"      # Push all note namespaces
  ```

## Viewing Notes

- To view notes in our log :

  ```bash
  git log --show-notes        # Shows notes from default namespace
  git log --show-notes=review # Shows notes from custom namespace
  git log --show-notes="*"    # Shows notes from all namespaces
  ```

## Resources

- [Official Git documentation for `git notes`](https://git-scm.com/docs/git-notes)
- [topheman/git-notes-md](https://gist.github.com/topheman/ec8cde7c54e24a785e52#file-git-notes-md)

**[View more Git CheatSheets](https://github.com/jishnukoliyadan/GIT_Notes)**

