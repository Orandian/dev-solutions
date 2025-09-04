# Branching & Merging

**Problem**  
`error: Your local changes to the following files would be overwritten by merge`

**Solution**
```bash
# Create a new branch
git branch {branchName}
```

```bash
# Switch to a branch
git checkout {branchName}
```

```bash
# Merge a remote branch into your current branch
git merge origin/{branchName}
```
