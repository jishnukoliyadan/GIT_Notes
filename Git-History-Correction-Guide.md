# Git History Correction Guide

Correct accidental or incorrect author/committer information in Git repositories.  

## 1. Bulk History (conditional) Rewrite

#### Option 1 : Legacy Version :

```bash
# https://stackoverflow.com/a/750182

git filter-branch --env-filter '
OLD_EMAIL="old@example.com"
NEW_NAME="Your Name"
NEW_EMAIL="correct@example.com"
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]; then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]; then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

#### Option 2 : Alternative Using `git filter-repo` :

``` bash
# https://gist.github.com/rm3l/0ef83820ab8731fd67db97ab9118d752

pip install git-filter-repo
git filter-repo --force --commit-callback '
    OLD_EMAIL = b"your-old-email@example.com"
    NEW_NAME = b"Your Correct Name"
    NEW_EMAIL = b"your-correct-email@example.com"

    if commit.author_email == OLD_EMAIL : 
        commit.author_name = NEW_NAME
        commit.author_email = NEW_EMAIL

    if commit.committer_email == OLD_EMAIL :
        commit.committer_name = NEW_NAME
        commit.committer_email = NEW_EMAIL
'
```

## 2. Bulk History (unconditional) Rewrite

#### Option 1 : Legacy Version :

```bash
git filter-branch --env-filter '
export GIT_AUTHOR_NAME="New Name"             # Override author name for ALL commits
export GIT_AUTHOR_EMAIL="new@example.com"     # Override author email for ALL commits
export GIT_COMMITTER_NAME="New Name"          # Override committer name for ALL commits
export GIT_COMMITTER_EMAIL="new@example.com"  # Override committer email for ALL commits
' --tag-name-filter cat -- --branches --tags --all
```

#### Option 2 : Alternative Using `git filter-repo` :

``` bash
pip install git-filter-repo
git-filter-repo --name-callback 'return name.replace(b"Old Username", b"New Username")'
git-filter-repo --email-callback 'return email.replace(b"old@example.com", b"correct@example.com")'
```

## 3. Change Author for the Latest Commit : Author

If only need to modify the author information for the most recent commit, use:

```bash
git commit --amend --author="New Name <new@example.com>" --no-edit
```

## 4. Change Author for the Latest Commit : Author & Committer

If only need to modify the Author and Committer information for the most recent commit, use:

#### Option 1 :

```bash
export GIT_AUTHOR_NAME="New Name"
export GIT_AUTHOR_EMAIL="new@example.com"
export GIT_COMMITTER_NAME="New Name"
export GIT_COMMITTER_EMAIL="new@example.com"
git commit --amend --no-edit
```

#### Option 2 :

```bash
# https://stackoverflow.com/a/750191

git filter-branch -f --env-filter "
    GIT_AUTHOR_NAME='Newname'
    GIT_AUTHOR_EMAIL='new@email'
    GIT_COMMITTER_NAME='Newname'
    GIT_COMMITTER_EMAIL='new@email'
  " HEAD
```

## Verify Changes

```bash
git log -1 --pretty=fuller
```

## Important Notes

- Force Push Required after rewriting history:

    ```bash
    git push --force --all
    ```
    > If you're working on a **shared repository**, always ensure that others are aware before force pushing changes.
- Backup First : Create a backup branch before making destructive changes.
- Prefer `git-filter-repo` over `filter-branch` for better performance and safety.

## Resources

- [Git Documentation](https://git-scm.com/docs/git-filter-branch)
- [git-filter-repo : Tutorial](https://github.com/newren/git-filter-repo)
- [git-filter-repo : Tutorial](https://til.simonwillison.net/git/git-filter-repo)
- [git-filter-repo : CALLBACKS](https://htmlpreview.github.io/?https://github.com/newren/git-filter-repo/blob/docs/html/git-filter-repo.html#CALLBACKS)