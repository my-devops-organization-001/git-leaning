# Git learning roadmap: from beginner to expert

Think of Git as a set of layers. Learn them in order: each layer explains the next.

## 1. The mental model

Start here; it makes the commands easier to understand.

- **Repository(Re: again, Ponenre: put) store**: a project’s Git data and history [data and history].  this .git folder
- **Working tree(files or folder)**: the files you’re currently editing.
- **Staging area (index)**: the proposed contents of your next commit.
- **Commit**: a snapshot plus a link to its parent commit(s).
- **Branch**: a movable name pointing to a commit.
- **HEAD**: your current checkout—usually a branch, sometimes a specific commit.
- **The three states**: modified, staged, committed.

**Practice:** make a repository, edit a file, inspect `git status`, stage part of it, commit, and inspect the result.

```
Your version:   file change → commit, tag → history → data → Repository
Git's version:  working tree → index (stage) → commit → object store → refs

tag is for release or publish
```
## 2. Everyday local workflow

Learn to make and inspect commits safely.

- Create or clone: `git init`, `git clone`
- Inspect: `git status`, `git diff`, `git diff --staged`, `git log`
- Stage and commit: `git add`, `git commit`
- Undo uncommitted changes: `git restore`
- Unstage: `git restore --staged`
- Ignore files: `.gitignore`

**Practice:** make small, focused commits with clear messages. Before each commit, review exactly what is staged.

## 3. History and references

Understand how Git names and connects points in history.

- Commit IDs and parent relationships
- Branches and tags
- Relative references: `HEAD~`, `HEAD^`
- Compare commits and branches: `git show`, `git diff`, `git log`
- Search history: `git grep`, `git log -S`, `git blame`

**Practice:** draw a small commit graph by hand, then verify it with `git log --graph --oneline --all`.

## 4. Branching and merging

Branches are lightweight pointers; merging combines histories.

- Create and switch branches: `git branch`, `git switch`
- Merge: `git merge`
- Fast-forward vs. merge commits
- Conflicts: inspect, resolve, stage, and complete the merge
- Abort safely: `git merge --abort`
- Delete merged branches

**Practice:** create two branches, change the same lines on both, merge them, and resolve the conflict.

## 5. Remotes and collaboration

Learn how local history relates to shared repositories.

- Remotes: `git remote`
- Fetch and inspect: `git fetch`
- Integrate changes: `git pull`
- Publish branches: `git push`
- Track upstream branches
- Pull requests and review workflows
- Handle non-fast-forward push errors

**Practice:** collaborate with a second clone or a partner: fetch, review, merge, and push changes.

## 6. Rewriting and rearranging history

These tools are powerful; first learn which history is safe to rewrite.

- Amend the latest commit: `git commit --amend`
- Move commits: `git rebase`
- Select commits: `git cherry-pick`
- Edit a sequence: `git rebase -i`
- Undo published changes safely: `git revert`

**Rule of thumb:** rewriting local, unpublished work is usually fine; avoid rewriting shared history unless the team agrees.

## 7. Recovery

Expertise includes recovering from mistakes.

- Find previous references: `git reflog`
- Recover commits or branch pointers
- Restore a file from a commit
- Choose between `restore`, `reset`, and `revert`
- Understand reset modes: `--soft`, `--mixed`, `--hard`

**Practice:** create a disposable repository, make mistakes intentionally, and recover using the reflog. Never experiment with destructive commands in a valuable working tree.

## 8. Internals

Learn what Git stores and how its model works.

- Objects: blobs, trees, commits, annotated tags
- The index and its role in building commits
- References, remote-tracking branches, and `HEAD`
- Content-addressing and object IDs
- Packfiles and object storage
- Detached HEAD

**Practice:** inspect `.git` in a throwaway repository and use `git cat-file` and `git ls-tree`.

## 9. Advanced tools and repository care

- Partial staging: `git add -p`
- Worktrees: `git worktree`
- Bisecting regressions: `git bisect`
- Large files and Git LFS
- Submodules and, where appropriate, subtree workflows
- Hooks and automation
- Repository maintenance, integrity checks, and performance basics

## 10. Becoming expert

For every command, be able to explain:

1. Which data it changes: working tree, index, commit graph, or refs?
2. Is it reversible? How?
3. Does it rewrite history?
4. Is that history shared?
5. How would you inspect the result before publishing?

