# Intermediate Comand
# Intermediate Git Commands

Below are intermediate-level commands organized by the same categories, going beyond the basics.

---

## Repository Setup

**`git init --bare <repo.git>`**
Create a repository **without a working directory**. Used for central/shared servers where nobody edits files directly.
```bash
git init --bare project.git
```

**`git clone --depth 1 <url>`**
**Shallow clone** — fetch only the latest commit history (faster, smaller). Useful for CI/CD.
```bash
git clone --depth 1 https://github.com/user/repo.git
```

**`git clone --branch <branch> <url>`**
Clone a specific branch instead of the default.
```bash
git clone --branch develop https://github.com/user/repo.git
```

**`git remote -v`**
List remotes with their URLs (fetch and push).
```bash
git remote -v
```

**`git remote set-url origin <new-url>`**
Change the URL of an existing remote (e.g., switching from HTTPS to SSH).
```bash
git remote set-url origin git@github.com:user/repo.git
```

---

## Branch

**`git switch <branch>`**
Modern replacement for `git checkout <branch>` — switch branches (safer, clearer intent).
```bash
git switch feature-login
```

**`git switch -c <branch>`**
Create **and** switch to a new branch in one step (equivalent to old `checkout -b`).
```bash
git switch -c hotfix/payment
```

**`git switch -`**
Switch back to the **previous** branch instantly.
```bash
git switch -
```

**`git branch -vv`**
List local branches with their **upstream tracking** info and last commit.
```bash
git branch -vv
```

**`git branch -m <old> <new>`**
Rename a branch that is **not** currently checked out.
```bash
git branch -m old-name new-name
```

**`git branch --merged`** / **`git branch --no-merged`**
Show branches already merged into the current branch (safe to delete) / not yet merged.
```bash
git branch --merged
```

---

## Commit

**`git commit -am "message"`**
Stage **tracked** modified files and commit in one step (does NOT include new/untracked files).
```bash
git commit -am "Refactor auth module"
```

**`git commit --amend --no-edit`**
Add currently staged changes to the **last commit** without changing its message.
```bash
git add forgotten-file.js
git commit --amend --no-edit
```

**`git reset --soft HEAD~1`**
Undo the last commit but **keep changes staged** (great for re-committing differently).
```bash
git reset --soft HEAD~1
```

**`git reset --mixed HEAD~1`** (default)
Undo the last commit, keep changes in working directory, **unstage** them.
```bash
git reset HEAD~1
```

**`git revert <commit-hash>`**
Create a **new commit** that undoes a previous commit — safe for shared/public history (unlike reset).
```bash
git revert a1b2c3d
```

**`git cherry-pick <commit-hash>`**
Apply a **single specific commit** from another branch onto the current branch.
```bash
git cherry-pick a1b2c3d
```

**`git cherry-pick <hash1>^..<hash2>`**
Cherry-pick a **range** of commits.
```bash
git cherry-pick abc123^..def456
```

---

## Tag

**`git tag -a v1.1.0 <commit-hash> -m "message"`**
Tag a **specific past commit** (not just HEAD).
```bash
git tag -a v1.0.1 9f8e7d6 -m "Hotfix release"
```

**`git show v1.0.0`**
Show the tag's metadata and the commit it points to.
```bash
git show v1.0.0
```

**`git push origin <tagname>`**
Push a **single** tag to remote (tags are not pushed by default).
```bash
git push origin v1.0.0
```

**`git push origin --tags`**
Push **all** local tags to remote.
```bash
git push origin --tags
```

**`git tag -d <tagname>`**
Delete a local tag.
```bash
git tag -d v0.9.0
```

**`git push origin --delete <tagname>`**
Delete a tag on the remote.
```bash
git push origin --delete v0.9.0
```

**`git describe --tags`**
Show the most recent tag reachable from the current commit (useful for versioning).
```bash
git describe --tags
```

---

## Stashing (Intermediate Essential)

**`git stash`**
Temporarily save uncommitted changes so you can switch branches cleanly.
```bash
git stash
```

**`git stash push -m "wip: login form"`**
Stash with a **descriptive message**.
```bash
git stash push -m "wip: login form"
```

**`git stash list`**
List all stashes (most recent first).
```bash
git stash list
```

**`git stash pop`**
Reapply the most recent stash **and remove it** from the stash list.
```bash
git stash pop
```

**`git stash apply stash@{2}`**
Apply a specific stash but **keep it** in the list.
```bash
git stash apply stash@{2}
```

**`git stash drop stash@{0}`**
Delete a specific stash without applying.
```bash
git stash drop stash@{0}
```

---

## Configuration

**`git config --global alias.<short> "<command>"`**
Create a **shortcut** for a long command.
```bash
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --graph --all"
# Now use: git st  /  git lg
```

**`git config --global core.editor "vim"`**
Set the default text editor Git uses for commit messages and rebase.
```bash
git config --global core.editor "code --wait"
```

**`git config --global pull.rebase true`**
Make `git pull` use **rebase** instead of merge by default (cleaner linear history).
```bash
git config --global pull.rebase true
```

**`git config --global --unset <key>`**
Remove a specific config setting.
```bash
git config --global --unset pull.rebase
```

**`git config --global --edit`**
Open the **global config file** directly for manual editing.
```bash
git config --global --edit
```

---

## Rebase & History Rewriting (Intermediate)

**`git rebase -i HEAD~3`**
**Interactive rebase** — squash, reword, reorder, edit, or drop the last 3 commits. Opens an editor with a todo list (`pick`, `squash`, `reword`, `drop`, `edit`).
```bash
git rebase -i HEAD~3
```

**`git rebase --continue`** / **`git rebase --abort`**
After resolving a conflict during rebase, continue or abort the whole operation.
```bash
git rebase --continue
git rebase --abort
```

**`git rebase --onto <newbase> <upstream> <branch>`**
Move a branch onto a **new base**, keeping only commits **not** in `<upstream>`.
```bash
git rebase --onto main dev1 feature
```

**`git rebase -i --autosquash HEAD~5`**
Auto-arrange `fixup!`/`squash!` commits so they merge into their target commit automatically.
```bash
git commit --fixup a1b2c3d
git rebase -i --autosquash HEAD~5
```

**`git reflog`**
Show the **full log of every HEAD move** (commits, resets, rebases, checkouts). Your safety net after a mistake.
```bash
git reflog
```

**`git reset --hard HEAD@{3}`**
Restore the repo to a state captured in `reflog` (recover "lost" commits after a bad rebase/reset).
```bash
git reset --hard HEAD@{3}
```

---

## Merging (Intermediate)

**`git merge --no-ff <branch>`**
Merge **always creating a merge commit**, even when a fast-forward is possible — preserves branch history.
```bash
git merge --no-ff feature-login
```

**`git merge --squash <branch>`**
Combine all commits of a branch into a **single set of staged changes** (no commit yet) — then commit manually.
```bash
git merge --squash feature-login
git commit -m "Add feature-login"
```

**`git merge --abort`**
Abort a merge in progress and return to the pre-merge state.
```bash
git merge --abort
```

**`git mergetool`**
Open a visual merge tool to resolve conflicts.
```bash
git mergetool
```

---

## Inspecting & Comparing (Intermediate)

**`git log --oneline --graph --all`**
Visual, compact **branch graph** of the whole history.
```bash
git log --oneline --graph --all
```

**`git log --author="Alice" --since="2 weeks ago"`**
Filter commits by author and date.
```bash
git log --author="Alice" --since="2 weeks ago"
```

**`git log -S "functionName"`**
Find commits that **added or removed** a specific string (pickaxe search).
```bash
git log -S "validateToken"
```

**`git diff --staged`**
Show differences between the **staging area and last commit** (what will be committed).
```bash
git diff --staged
```

**`git diff main..feature`**
Compare two branches.
```bash
git diff main..feature
```

**`git blame <file>`**
Show **who last changed each line** of a file, with commit hash and date.
```bash
git blame index.html
```

**`git show <commit>:<path>`**
Show a file's content **as it was** at a specific commit.
```bash
git show a1b2c3d:src/app.js
```

---

## Cleaning & Removing (Intermediate)

**`git clean -n`**
**Dry-run** — list untracked files that would be removed (always run first!).
```bash
git clean -n
```

**`git clean -fd`**
Actually **delete untracked files and directories**.
```bash
git clean -fd
```

**`git rm --cached <file>`**
Stop tracking a file but **keep it on disk** (e.g., after adding it to `.gitignore`).
```bash
git rm --cached .env
```

**`git restore --staged <file>`**
Unstage a file (modern replacement for `git reset HEAD <file>`).
```bash
git restore --staged index.html
```

**`git restore <file>`**
Discard **working directory** changes to a file (dangerous — irreversible).
```bash
git restore index.html
```

---

## Publish (Intermediate)

**`git push origin --delete <branch>`**
Delete a branch on the remote.
```bash
git push origin --delete feature-login
```

**`git push --force-with-lease`**
Force-push **safely** — refuses if someone else pushed to the remote since your last fetch (preferred over `--force`).
```bash
git push --force-with-lease origin feature-login
```

**`git push origin <local>:<remote>`**
Push a local branch to a **differently named** remote branch.
```bash
git push origin local-feature:remote-feature
```

**`git fetch --prune`**
Fetch remote updates **and delete** local remote-tracking branches that no longer exist on the server.
```bash
git fetch --prune
```

**`git pull --rebase`**
Fetch remote changes and **rebase** your local commits on top (instead of creating a merge commit).
```bash
git pull --rebase origin main
```

**`git push -u origin --all`**
Push **all** local branches to remote and set upstream tracking.
```bash
git push -u origin --all
```

---

## Submodules & Worktrees (Intermediate+)

**`git submodule add <url> <path>`**
Add another repository as a **submodule** inside your project.
```bash
git submodule add https://github.com/lib/library.git vendor/library
```

**`git submodule update --init --recursive`**
Initialize and fetch **all submodules** (needed after cloning a repo with submodules).
```bash
git submodule update --init --recursive
```

**`git worktree add ../hotfix-dir hotfix`**
Check out a branch into a **separate directory** while keeping the main working tree intact — work on two branches simultaneously without stashing.
```bash
git worktree add ../hotfix-dir hotfix
```

---

## Useful Bisect (Debugging)

**`git bisect start`** / **`git bisect good <hash>`** / **`git bisect bad <hash>`**
Binary-search through history to find the commit that **introduced a bug**.
```bash
git bisect start
git bisect bad                 # current commit is broken
git bisect good v1.0.0         # this old tag worked
# Git checks out a middle commit — test it, then mark good/bad, repeat
git bisect reset               # finish and return to original state
```

---

These cover the "intermediate" gap between daily basics and advanced workflows (interactive rebase, reflog recovery, force-with-lease, worktrees, bisect). Let me know if you'd like an **advanced** tier (filter-branch, `git replace`, packfiles, hooks, `git notes`, etc.).


# Advance