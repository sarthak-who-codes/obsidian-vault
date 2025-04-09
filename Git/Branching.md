Git branching is a way to **create separate lines of development** in your project without affecting the main codebase.

Commands:

```bash
# Create a new branch
git branch feature-xyz

# Switch to that branch
git checkout feature-xyz

# Create and switch in one command
git checkout -b feature-xyz

# Merge a branch into your current branch
git merge feature-xyz

# Delete a branch after merge
git branch -d feature-xyz
```

## Under the hood

A branch is just a named [pointer](https://en.wikipedia.org/wiki/Pointer_\(computer_programming\)#:~:text=A%20pointer%20is%20a%20programming,than%20storing%20the%20data%20itself.) to a specific commit. When you create a branch, you are creating a new pointer to a specific commit. The commit that the branch points to is called the tip of the branch

[![Image](https://storage.googleapis.com/qvault-webapp-dynamic-assets/course_assets/iH1kl8l.png)](https://storage.googleapis.com/qvault-webapp-dynamic-assets/course_assets/iH1kl8l.png)

 > **Tips:** The latest commit on whatever branch you're looking at.

> **Merge base:** Best common ancestor.

> **Orphan branch:** Branch which does not have a parent.

#### Visualizing branches

This visualization approach is followed by `git` is self. Use `man git-rebase` to see.

```bash
    D - E  other_branch
  /
A - B - C  main
```

`A` is best common ancestor here.

## Creating & Switching branches

To see all the available branches use:

```bash
git branch
```

To create a new branch use: 

```bash
# Old and doesn't automatically switch to latest branch
git branch new_branch_name

# Older but automatically switches to new branch
git checkout -b new_branch_name

# Newer way and automatically switches to new brach
git switch -c new_banch_name
```

Switch between branches

```bash
# Older way
git checkout branch_name

# Nower way
git switch branch_name
```

### Notes:

- If you have uncommitted changes that conflict with the branch you want to switch to, Git might stop you or ask you to stash changes.
- You can stash them with `git stash`, switch branches, and then use `git stash pop` to bring them back.

