**Merging** is the process of taking the contents of a source branch and integrating them into a target branch. Git tries to automatically combine the files and history of both branches.

## Types of merging

1. **Fast-Forward Merging:**

```bash
git checkout main
git merge feature-branch
```

If `main` hasn’t moved since `feature-branch` branched off, Git will just move the pointer forward — no new commit is created.

```mathematica
A---B---C (main)
         \
          D---E (feature-branch)

After fast-forward merge:

A---B---C---D---E (main)
```

2. **Three way merge:**

```bash
git checkout main
git merge feature-branch
```

Git looks at:
- The common ancestor
- The tip of `main`
- The tip of `feature-branch`

And tries to auto-merge. A new **merge commit** is created.

```mathematica
A---B---C---F (main)
     \     /
      D---E (feature-branch)

After merge:

A---B---C---F (main)
     \     / 
      D---E
```

3. **Squash merge:**

```bash
git checkout main
git merge --squash feature-branch
git commit
```

The history looks cleaner, but lose the individual commit granularity from the feature branch.

4. **Rebase:** This is not technically merging. Will cover it later.

![[Screenshot 2025-04-09 at 6.26.47 PM.png]]
## Merge conflicts

