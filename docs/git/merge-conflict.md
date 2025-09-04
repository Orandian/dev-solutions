# Fixing Merge Conflict in Git

**Problem**  
`error: Your local changes to the following files would be overwritten by merge`

**Solution**
```bash
git stash
git pull origin main
git stash pop
# Resolve any merge conflicts if they arise
```