# Git Object Inspection and Storage

## Task 1: Git Object Model Exploration

### Get commit hash
```bash
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git log --oneline -1
696e891 (HEAD -> feature/lab2, origin/feature/lab2) git add test.txt
```
### Get tree hash from commit
```bash
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git cat-file -p HEAD
tree e87c1f8927c6667e5de7c301eefaa73e2141c4b7
parent d4001fe1e61cda3a47a3e24b198281cfe42dee6a
author Lev Permiakov <Leo2004201441@gmail.com> 1770634566 +0300
committer Lev Permiakov <Leo2004201441@gmail.com> 1770634566 +0300
gpgsig -----BEGIN SSH SIGNATURE-----
...
-----END SSH SIGNATURE-----
```
### Get blob hash from tree
```bash
PS> git cat-file -p e87c1f8927c6667e5de7c301eefaa73e2141c4b7
040000 tree 8ac324ada09a1d7c1179c28d628ac2cde2bbb1e4    .github
100644 blob 6e60bebec0724892a7c82c52183d0a7b467cb6bb    README.md
040000 tree a1061247fd38ef2a568735939f86af7b1000f83c    app
040000 tree a6933b56ef49e0e12f3009c663d66bba814c6bb3    labs
040000 tree d3fb3722b7a867a83efde73c57c49b5ab3e62c63    lectures

PS> git cat-file -p fc8af692c15796735f083d42dc5be3c8d1076470
100644 blob 3f3f005b29247e51a4f4d6b8ce07b67646cd6074    text.txt

PS> git cat-file -p 3f3f005b29247e51a4f4d6b8ce07b67646cd6074
Test content
```

## Description of object types

#### Blob object - stores the contents of the file (only data, without name and path).

#### Tree — represents a directory and contains links to large binary objects and other trees.

#### Commit — captures the status of the project, referring to the tree, the parent commit, and the author's metadata.

## How Git stores repository data

#### Git stores all data as objects in a directory.git/objects. Each object is identified by a SHA-1 hash and is immutable. Files are stored as blobs, directories are stored as trees, and the change history is stored through commits, which link everything into a graph.

## Examples of Git objects
#### Blob example
```bash
git cat-file -p 3f3f005b29247e51a4f4d6b8ce07b67646cd6074
```

#### Output:
```bash
Test content
```

#### Tree example
```bash
git cat-file -p fc8af692c15796735f083d42dc5be3c8d1076470
```

#### Output:
```bash
100644 blob 3f3f005b29247e51a4f4d6b8ce07b67646cd6074    text.txt
```
#### Commit example
```bash
git cat-file -p HEAD
```

#### Output (excerpt):
```bash
tree e87c1f8927c6667e5de7c301eefaa73e2141c4b7
parent d4001fe1e61cda3a47a3e24b198281cfe42dee6a
author Lev Permiakov <Leo2004201441@gmail.com> 1770634566 +0300
committer Lev Permiakov <Leo2004201441@gmail.com> 1770634566 +0300
gpgsig -----BEGIN SSH SIGNATURE-----
 U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAgjfAspa17s50zzV0cjD8w2O1T3d
 uypGdCFT4DWX4TsyYAAAADZ2l0AAAAAAAAAAZzaGE1MTIAAABTAAAAC3NzaC1lZDI1NTE5
 AAAAQLVtczUxZIOkYbqmhcYoRoX1sK++ko3PhxN2T1PMPuPtFcPxZqDHPfyEUzgGl8NGPI
 kn7DwfrDIVUvH+MBDIPw8=
 -----END SSH SIGNATURE-----
```

## Task 2 — Reset and Reflog Recovery

### Practice Branch Setup

```powershell
git switch -c git-reset-practice
"First commit" | Out-File file.txt -Encoding utf8
git add file.txt
git commit -m "First commit"
"Second commit" | Out-File file.txt -Append -Encoding utf8
git add file.txt
git commit -m "Second commit"
"Third commit" | Out-File file.txt -Append -Encoding utf8
git add file.txt
git commit -m "Third commit"
```
### Soft Reset
```bash
git reset --soft HEAD~1
git log --oneline
```
Output:
```
25ca22e (HEAD -> git-reset-practice) Second commit
5d09bf4 First commit
```
#### Effect:

Commit history moved back by one commit, Index (staging area) kept changes staged, Working tree unchanged

### Hard Reset
```
git reset --hard HEAD~1
git log --oneline
```
Output:
```
5d09bf4 (HEAD -> git-reset-practice) First commit
```
#### Effect:

Commit history moved back by one commit, Index cleared, Working tree reverted to the committed state
### Inspecting Reflog
```
git reflog
```
Output:
```
5d09bf4 HEAD@{0}: reset: moving to HEAD~1
25ca22e HEAD@{1}: reset: moving to HEAD~1
35e36ef HEAD@{2}: commit: Third commit
25ca22e HEAD@{3}: commit: Second commit
5d09bf4 HEAD@{4}: commit: First commit
```
### Recovery Using Reflog
```
git reset --hard 696e891
```
Output:
```
HEAD@{0}: reset: moving to 696e891
```

This demonstrates that even after destructive operations like git reset --hard, Git allows recovery of previous states using reflog, which tracks all HEAD movements locally.

### Analysis
- git reset --soft only moves HEAD and preserves both the index and working tree.

- git reset --hard rewrites history and discards all uncommitted changes.

- git reflog acts as a safety net, enabling recovery of commits that are no longer visible in git log.

- Using reflog hashes, the repository can be restored to any recent state.

## Task 3 — Visualize Commit History

### Commands Used

```bash
git switch -c side-branch
echo "Branch commit" >> history.txt
git add history.txt && git commit -m "Side branch commit"
git switch -
git log --oneline --graph --all
```
### Commit Graph Snippet
```
* 25731af (feature/lab2) Side branch commit
* 696e891 (HEAD -> side-branch, origin/feature/lab2, git-reset-practice) git add test.txt
* d4001fe Add test file
* e1f8ced (origin/main, origin/HEAD, main) docs: feat task 2
*   9c0a334 Merge pull request #1 from group06854/feature/lab1
|\
| * 2e298ed (origin/feature/lab1, feature/lab1) feat: add task 1 and part of 2 task
| * 6e57609 "docs: add commit signing summary"
| * 74cba37 "docs: add commit signing summary"
| * 7d11d1c "docs: feat commit signing summary"
| * 7c9c964 "docs: add commit signing summary"
|/
* 69e0abd docs: fix point
* 4ff0120 docs: add PR template
* d6b6a03 Update lab2
* 87810a0 feat: remove old Exam Exemption Policy
* 1e1c32b feat: update structure
* 6c27ee7 feat: publish lecs 9 & 10
* 1826c36 feat: update lab7
* 3049f08 feat: publish lec8
```
### Commit Messages List

- Side branch commit

- git add test.txt

- Add test file

- docs: feat task 2

- Merge pull request #1 from group06854/feature/lab1

- feat: add task 1 and part of 2 task

- docs: add commit signing summary

- docs: fix point

- docs: add PR template

- Update lab2

### Reflection

The commit graph clearly shows branch divergence and merge points, helping to understand parallel development and how different features are integrated into the main history.
## Task 4 — Tagging Commits

### Commands Used

```powershell
git tag v1.0.0
git push origin v1.0.0
```
Output:
```
[new tag]         v1.0.0 -> v1.0.0
```
### Tags and Associated Commits
```
git show-ref --tags
```

Output:
```
70afc31c405095e8cac88555e115793f7fd2dcb3 refs/tags/v1.0.0
```

v1.0.0 → commit 70afc31c405095e8cac88555e115793f7fd2dcb3

### Tags Matter

Git tags are used to mark important points in repository history, such as releases.
They are commonly used for versioning, triggering CI/CD pipelines, and generating release notes.

## Task 5 — git switch vs git checkout vs git restore

```bash
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> echo "initial content" > demo.txt
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git add demo.txt
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git commit -m "Add demo.txt"
[cmd-compare 5cb6460] Add demo.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 demo.txt
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git restore demo.txt                 # discard working tree changes
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git restore --staged demo.txt        # unstage (keep working tree)
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git restore --source=HEAD~1 demo.txt
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git status
On branch cmd-compare
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    demo.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        labs/submission2.md

no changes added to commit (use "git add" and/or "git commit -a")
PS C:\Users\LevPe\Desktop\Innopolis\3 course\DEVOPS\DevOps-Intro> git branch
* cmd-compare
  feature/lab1
  feature/lab2
  git-reset-practice
  main
  side-branch
```
### Reflection
Git switch is recommended for safely navigating branches. It is modern, clear, and avoids the confusion of older commands like git checkout.

## Task 6 — GitHub Community Engagement

Why starring repositories matters: shows project support, helps open-source developers, and allows them to track interesting projects. How following developers helps: helps keep up to date with updates, learn from experienced people, and simplifies team interaction.