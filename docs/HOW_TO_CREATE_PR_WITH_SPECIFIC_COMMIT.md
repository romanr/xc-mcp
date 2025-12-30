# How to Create a Pull Request with Only a Specific Commit

This guide explains different scenarios for creating a pull request with only specific commits, excluding others.

## Current Situation Analysis

Based on the repository state:
- **Current branch**: `copilot/create-pull-request-with-commit`
- **Main branch**: Points to commit `58cfa96`
- **Current branch has**:
  - Commit `58cfa96`: "fix: #91 : SDK version issue with zod" (already on main)
  - Commit `00c0297`: "Initial plan" (not on main)

## Scenario 1: The Commit is Already on Main Branch

**Problem**: The commit `58cfa96` is already on the `main` branch, so there's nothing to create a PR for.

**Solution Options**:

### Option A: Remove the Empty Commit
If you want to clean up the current branch and align it with main:

```bash
# Reset current branch to main
git reset --hard origin/main

# Force push to update remote branch (use --force-with-lease for safety)
git push --force-with-lease origin copilot/create-pull-request-with-commit
```

This will make your branch identical to main (just contains the fix commit).

### Option B: Create PR for the "Initial plan" Commit Only
If you want to keep the "Initial plan" commit and create a PR for it:

```bash
# Your branch is already ahead of main by this commit
# Simply create a PR from copilot/create-pull-request-with-commit to main
# The PR will only show the difference (the "Initial plan" commit)
```

## Scenario 2: Cherry-pick a Specific Commit to New Branch

If you want to create a PR with **only** commit `58cfa96` on a fresh branch (ignoring that it's already on main):

```bash
# Create a new branch from main
git checkout -b feature/specific-fix main

# Cherry-pick the specific commit
git cherry-pick 58cfa96

# Push the new branch
git push origin feature/specific-fix

# Create PR from feature/specific-fix to main (or other target branch)
```

## Scenario 3: Create PR with Specific Commit from History

If you have a branch with multiple commits and want to create a PR with only one of them:

### Method 1: Interactive Rebase
```bash
# Start interactive rebase from the base commit
git rebase -i <base-commit-sha>

# In the editor:
# - Keep (pick) the commit you want
# - Drop or remove lines for commits you don't want

# Force push (if branch is already pushed) - use --force-with-lease for safety
git push --force-with-lease origin your-branch-name
```

### Method 2: Create New Branch with Cherry-pick
```bash
# Create new branch from target base
git checkout -b new-branch-name <target-base-branch>

# Cherry-pick only the commit you want
git cherry-pick <commit-sha>

# Push new branch
git push origin new-branch-name

# Create PR from new-branch-name to target branch
```

### Method 3: Reset and Selective Commit
```bash
# Soft reset to base commit (keeps changes in working directory)
git reset --soft <base-commit-sha>

# Unstage everything
git reset

# Stage only files related to the specific commit
git add <files-for-specific-commit>

# Commit with same message as original
git commit -m "Your commit message"

# Force push if needed (use --force-with-lease for safety)
git push --force-with-lease origin your-branch-name
```

## Scenario 4: Squash Multiple Commits into One for PR

If you want to combine multiple commits into one for a cleaner PR:

```bash
# Interactive rebase from base
git rebase -i <base-commit-sha>

# In the editor, change 'pick' to 'squash' or 's' for commits to combine
# Keep 'pick' for the first commit, use 'squash' for others

# Edit the commit message in the next screen

# Force push (use --force-with-lease for safety)
git push --force-with-lease origin your-branch-name
```

## Best Practices

1. **Always check branch state first**:
   ```bash
   git status
   git log --oneline --graph --all -10
   ```

2. **Verify commit content before pushing**:
   ```bash
   git show <commit-sha>
   git diff <base-branch>..HEAD
   ```

3. **Use `--force-with-lease` instead of `--force`** for safer force pushes:
   ```bash
   git push --force-with-lease origin your-branch-name
   ```

4. **Create PR from GitHub CLI** (if installed):
   ```bash
   gh pr create --base main --head your-branch-name --title "Your PR Title" --body "PR Description"
   ```

## Current Recommendation

Based on your current situation where commit `58cfa96` is already on `main`:

1. **If you want to keep the current branch clean**: Reset to main
   ```bash
   git reset --hard origin/main
   git push --force-with-lease origin copilot/create-pull-request-with-commit
   ```

2. **If you want to add new changes**: Keep working on current branch
   ```bash
   # Make your changes
   git add .
   git commit -m "Your new changes"
   git push origin copilot/create-pull-request-with-commit
   ```

3. **If you want to cherry-pick to a different base branch**:
   ```bash
   # Checkout different base branch
   git checkout <different-base-branch>
   
   # Create new branch
   git checkout -b feature/cherry-picked-fix
   
   # Cherry-pick the commit
   git cherry-pick 58cfa96
   
   # Push
   git push origin feature/cherry-picked-fix
   ```

## Troubleshooting

### "Commit already exists on target branch"
- The commit is already merged. You may need to revert it first, or create a new commit with the same changes.

### "Conflicts during cherry-pick"
- Resolve conflicts manually:
  ```bash
  # Edit conflicting files
  git add <resolved-files>
  git cherry-pick --continue
  ```

### "Force push rejected"
- Someone else may have pushed changes:
  ```bash
  # Use force-with-lease to be safer
  git push --force-with-lease origin your-branch-name
  
  # If still rejected, fetch and verify
  git fetch origin
  git log origin/your-branch-name
  ```

## Additional Resources

- [Git Cherry-pick Documentation](https://git-scm.com/docs/git-cherry-pick)
- [Git Rebase Interactive Documentation](https://git-scm.com/docs/git-rebase#_interactive_mode)
- [GitHub Pull Request Documentation](https://docs.github.com/en/pull-requests)
