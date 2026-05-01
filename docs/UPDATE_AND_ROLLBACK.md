# Updating and rolling back safely

Use a branch so the current working version stays untouched until the replacement has been tested.

## 1. Create a test branch

```bash
git checkout main
git pull
git checkout -b card-cleanup-controls-hacs
```

## 2. Copy in the updated files

Copy these files/folders from the update package into the repository:

```text
README.md
CHANGELOG.md
hacs.json
info.md
elegoo_saturn_ultra_card.jinja
lovelace/elegoo_saturn_ultra_card.yaml
docs/UPDATE_AND_ROLLBACK.md
```

Do not delete your `assets/` folder.

## 3. Review the diff

```bash
git status
git diff -- README.md lovelace/elegoo_saturn_ultra_card.yaml
```

## 4. Commit the test version

```bash
git add README.md CHANGELOG.md hacs.json info.md elegoo_saturn_ultra_card.jinja lovelace/elegoo_saturn_ultra_card.yaml docs/UPDATE_AND_ROLLBACK.md
git commit -m "Clean up Elegoo card controls and add HACS metadata"
```

## 5. Test in Home Assistant

Paste/include `lovelace/elegoo_saturn_ultra_card.yaml` in the same dashboard location as before.

Test these states before merging:

- Printer off.
- Printer idle.
- Printer actively printing.
- Printer paused.
- Stop confirmation prompt.
- Power confirmation prompt.
- Filename label still appears correctly.

## 6. Merge if it works

```bash
git checkout main
git merge card-cleanup-controls-hacs
git push
```

## 7. Roll back if it does not work

If you have not merged yet:

```bash
git checkout main
git branch -D card-cleanup-controls-hacs
```

If you already merged locally but have not pushed:

```bash
git checkout main
git reset --hard HEAD~1
```

If you already pushed and want a clean public rollback:

```bash
git log --oneline
git revert <commit_sha>
git push
```

`git revert` is safer than rewriting history after a push because it creates a new commit that undoes the change.
