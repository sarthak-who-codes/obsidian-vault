Git commands are divided into 2 parts:

1. Porcelain
2. Plumbing

### Porcelain

The porcelain commands are the ones that we use most often as a developer. Some porcelain commands are:

- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`
- `git log`

### Plumbing

Plumbing commands include:

- `git apply`
- `git commit-tree`
- `git hash-object`

---
## Status

A file can be in one of [several states](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#_the_very_basics) in a Git repository. Here are a few important ones:

- `untracked`: Not being tracked by Git
- `staged`: Marked for inclusion in the next commit
- `committed`: Saved to the repository's history

The `git status` command shows you the current state of your repo. It will tell you which files are untracked, staged, and committed.

---
## Staging and Committing

Staging is the stage before commit. If this was not exist, then all the files will be committed which is not ideal in many cases.

To stage code, use:

```bash
git add <file-name> # "." if you want to stage all files
```

Committing is taking a snapshot of repository. Here's how to do it:

```bash
git commit -m "your message"
```

---
## Logging

A git repo is a log list of commits where every commit represents full state of a repository at a given point in time.

The [git log](https://git-scm.com/docs/git-log) command shows a history of the commits in a repository:
- Who made a commit
- When the commit was made
- What was changed

Every commit has a unique commit hash

**Commands:**

```bash
git log

git log --oneline # Less verbose

git --no-page log -n <number-of-logs> # --no-pager prints the output in terminal
```

### Hashes

Each commit has (almost)unique has.

While commit hashes _are_ derived from their content changes, there's [also some other stuff](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects#_git_commit_objects) that affects the end hash. For example:

- The commit message
- The author's name and email
- The date and time
- Parent (previous) commit hashes

Q. *"Can two commits share same hash?"*

Theoretically it's possible with old SHA-1 has and google has demonstrated collision attack called ***"SHAttered"*** in 2017. But practically, is extremely unlikely.

So basically answers is **NO** with a catch.


#### What in those object files

If you `cat` any of the files, it will print binary.

```bash
proxy-server git:(main) ✗ cat .git/objects/43/dff3400a9a49dcc7a4bc8096c6fcf083d4aade 
xmQMO1�̯����-�
             BbB�<x�(��v��n۴]>4�w�Q���yof޼�U�W0��7�=�X�d
�X}<�-��Lx8�U$+Z�2����
f,7�04���G      �[    ����T'��#}w�'�F�Zt
�d��SP"� �k�bA��<JU�Z�g6�*-�t~�k�P��d#�x�8�W�����B�y�9:��;1p��rs���R�!��9ŵ��A[���%�u~�m���Q����!c����
                                                                                                     S�L�F�vV�tD�QV}D�!Z�%��nv���~�L���1���ۊ�W�C�$�CZ�.ս�F\�% 
```


We have to use `xxd` command to print.

```bash
➜  proxy-server git:(main) ✗ xxd .git/objects/43/dff3400a9a49dcc7a4bc8096c6fcf083d4aade > tmp.txt 
➜  proxy-server git:(main) ✗ cat tmp.txt
00000000: 7801 6d51 4d4f 0231 10f5 ccaf 98f4 c0c9  x.mQMO.1........
00000010: 2dbb 0b18 4262 42a2 1e3c 78e2 2892 9476  -...BbB..<x.(..v
00000020: 80ca 6edb b45d 3e34 fc77 fb01 51a3 97b6  ..n..]>4.w..Q...
00000030: 796f 66de bcd7 55a3 5730 9e94 379f 3d00  yof...U.W0..7.=.
00000040: a258 8b64 0ac4 587d 3c15 0eed 1e2d b98d  .X.d..X}<....-..
00000050: 4c78 38a9 5524 2b5a d232 a302 1db7 d2f8  Lx8.U$+Z.2......
00000060: 0b93 c196 c954 2795 c023 7d77 19f5 2793  .....T'..#}w..'.
00000070: 46b7 5a74 0d66 2c37 bb30 34aa 0715 8fce  F.Zt.f,7.04.....
00000080: 4709 e45b 0d0b f264 adb6 5350 1a22 01ce  G..[...d..SP."..
00000090: 2097 6b89 6241 a0df 073c 4a0f 559a 145a   .k.bA...<J.U..Z
000000a0: 9d67 36f5 2a2d 1084 747e f06b 8150 b2ea  .g6.*-..t~.k.P..
000000b0: 6423 e278 ef38 1406 e8b5 57e0 fe02 1707  d#.x.8....W.....
000000c0: e6f9 168a 42ab 79c7 393a 17d6 f83b 3114  ....B.y.9:...;1.
000000d0: 70ad d672 739f afe0 52ab 0521 c1c5 39c5  p..rs...R..!..9.
000000e0: b5c3 d341 5b11 adbd be25 8475 7eab 6d94  ...A[....%.u~.m.
000000f0: c9de 1bc9 51b9 14c9 f3fc 2163 86f1 1ddb  ....Q.....!c....
00000100: e00b 53e1 4cb5 4699 7656 9574 44b3 5112  ..S.L.F.vV.tD.Q.
00000110: 567d 4483 215a c525 fe88 6e16 0376 83b8  V}D.!Z.%..n..v..
00000120: 6b14 59d6 35ad 86b4 ca1f 15a3 753c 9b4b  k.Y.5.......u<.K
00000130: ec1d 0df4 d57e ea4c ff98 b831 9dd0 fadb  .....~.L...1....
00000140: 8af8 57ee 43a7 2497 435a 8f2e d5bd f317  ..W.C.$.CZ......
00000150: 465c a50f                                F\..
```


We have built-in command for printing these files luckily.

```bash
git cat-file -p <hash>
```

**Tip:** Use `git log -1` to get the hash of your last commit.

---

## Trees and Blob

Lets see what `git cat-file -p` prints

```bash
➜  proxy-server git:(main) ✗ git cat-file -p 5c992c5                                        
tree 2d84a61be439fc2aefc22fb3d3b6c1cdabb578fe
parent c2947467f2254baaadedfb1dd80957f135a7de46
author sarthak-who-codes <sarthak.roy@gmail.co> 1742752763 +0530
committer sarthak-who-codes <sarthak.roy@gmail.com> 1742752763 +0530

refactored file structure, implemented storing logs in user provided file location
```

---
## Storing data

Git stores an entire _snapshot_ of files on a _per-commit_ level. This was a surprise to me! I always assumed each commit only stored the _changes_ made in that commit.

#### Optimization

While it's true that Git stores entire snapshots, it _does_ have some performance optimizations so that your `.git` directory doesn't get too unbearably large.

- Git [compresses and packs](https://git-scm.com/book/en/v2/Git-Internals-Packfiles) files to store them more efficiently.
- Git deduplicates files that are the same across different commits. If a file doesn't change between commits, Git will only store it once.

---
## Config

Set global config

```bash
git config --global user.name "<username>"

git config --global user.email "your_email@example.com"
```

The `--get` flag is useful for getting a single value.

```bash
git config --get <key>
```

Keys are in the format `<section>.<keyname>`. For example:

- `user.name`
- `webflyx.ceo`

Use `--unset` flag to remove a configuration value. For example:

```bash
git config --unset <key>
```

