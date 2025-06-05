# Git Large File Storage (LFS) Setup Guide

Git LFS (Large File Storage) is a Git extension for managing large files, such as datasets, media, and model files, without bloating your Git repository.

## Why Use Git LFS?

- GitHub blocks files over 100MB and warns for files over 50MB.
- LFS stores large files outside the main repository while keeping lightweight pointers.
- Keeps cloning and pulling fast and efficient.

## Installation

- **On Ubuntu / Debian :**

  ```bash
  sudo apt install git-lfs
  ```

- **On macOS :**
  ```bash
  brew install git-lfs
  ```

## Setup in Our Repository

1. **Initialize Git LFS:**

   ```bash
   git lfs install
   ```

2. **Track File Types (e.g., CSV, pickle):**

   ```bash
   git lfs track "*.csv"
   ```

   > This command creates or updates a .gitattributes file to include tracking for .csv files.

3. **Add Your Large Files:**

   ```bash
   git add .gitattributes
   git add processed.csv
   ```

   > Commits both the tracking info and the file pointer.

4. **Commit the Changes:**

   ```bash
   git commit -m "Add large file using Git LFS"
   ```

5. **Push to GitHub:**
   ```bash
   git push origin <branch-name>
   ```

## Cloning & Pulling with LFS

When cloning a repo with LFS files:

```bash
git clone https://github.com/your-username/your-repo.git
cd your-repo
git lfs pull
```

## Untracking a File (if needed)

To stop tracking a file with Git LFS:

```bash
git lfs untrack "*.csv"          # Stop tracking .csv files
git add .gitattributes           # Stage the tracking change
git rm --cached processed.csv    # Unstage the file so it loses LFS tracking
git add processed.csv            # Re-add as a regular Git file
git commit -m "Remove CSV from LFS and track as regular file"
```

## Notes

- GitHub provides **1 GB** of free LFS storage and **1 GB/month** of bandwidth.
- For large datasets or media, consider compressing files or using external cloud storage if you hit the limit.
- Use `git lfs ls-files` to check LFS-tracked files.
- All collaborators must have Git LFS installed to properly clone or pull large files.

## Resources

- [Git LFS Official Site](https://git-lfs.github.com/)
- [GitHub Docs: Managing Large Files](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github)

**[View more Git CheatSheets](https://github.com/jishnukoliyadan/GIT_Notes)**
