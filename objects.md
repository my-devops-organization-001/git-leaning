# Git Objects and Refs — Explained

Git's entire repository is just **four types of objects** stored in `.git/objects/`, plus **refs** (named pointers). Everything else (branches, tags, history) is built from these.

---

## The Four Object Types

| Object | What it stores | Analogy |
|--------|---------------|---------|
| **Blob** | File **contents** (no name, no metadata) | The raw bytes of a file |
| **Tree** | A **directory listing** (names → blobs/trees) | A folder |
| **Commit** | A **snapshot pointer** + metadata | A save point |
| **Tag** (annotated) | A **labeled pointer** with message | A signed bookmark |

All objects are:
- **Content-addressed** — named by the SHA-1 (or SHA-256) hash of their contents
- **Immutable** — changing content creates a *new* object with a *new* hash
- **Stored once** — identical content is deduplicated automatically

---

## 1. Blob (Binary Large OBject)

**Stores:** Just the file's contents. **No filename, no path, no permissions.**

```
$ git cat-file -p <blob-hash>
hello world
```

What it *looks like* internally (Git prepends a header before hashing and storing):

```
blob 12\0hello world\n
```

- Header: `blob <size>\0`
- Then the raw file bytes.

Two files with identical content → **same blob, stored once**, even if in different directories or with different names.

---

## 2. Tree

**Stores:** A directory — a list of entries, each mapping a **name** to a **blob or subtree**, plus a **mode** (file type/permissions).

```
$ git cat-file -p <tree-hash>
100644 blob a1b2c3d4...    README.md
100644 blob e5f6g7h8...    main.py
040000 tree 9i0j1k2l...    src
```

Each line = `<mode> <type> <hash>\t<name>`

Common modes:
| Mode | Meaning |
|------|---------|
| `100644` | Regular file |
| `100755` | Executable file |
| `120000` | Symlink |
| `040000` | Subdirectory (tree) |

- A tree **is** a directory. It points to blobs (files) and other trees (subdirs).
- Trees **do not** store `..` or the parent's name — they're pure directory listings.
- This is why Git doesn't really track "folders" — folders are just trees.

---

## 3. Commit

**Stores:** A pointer to **one root tree** (the snapshot) + metadata + parent commit(s).

```
$ git cat-file -p <commit-hash>
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent 3c4e9cd789d88d8d89c1073707c3585e41b0e614
author Alice <alice@example.com> 1700000000 +0000
committer Alice <alice@example.com> 1700000000 +0000

Add login feature
```

Fields:
- **tree** — hash of the root tree (the full snapshot)
- **parent** — hash of previous commit (0 for the first, 2+ for merges)
- **author** — who wrote it + timestamp
- **committer** — who committed it (can differ, e.g. rebased/applied patches)
- **message** — the commit message

Key insight: **a commit does not store a diff.** It stores a *complete snapshot* (via one tree hash). Diffs are computed on demand.

---

## 4. Annotated Tag

**Stores:** A pointer to a commit/object + tagger info + message (optionally GPG-signed).

```
$ git cat-file -p <tag-hash>
object 3c4e9cd789d88d8d89c1073707c3585e41b0e614
type commit
tag v1.0
tagger Alice <alice@example.com> 1700000000 +0000

Release version 1.0
```

- **Lightweight tag** = just a ref pointing at a commit (no object created).
- **Annotated tag** = a real object (shown above) + a ref pointing to it.

---

## How objects chain together

```
commit  ──tree──►  root tree
   │                 ├── blob   (README.md)
   │                 ├── blob   (main.py)
   │                 └── tree   (src/)
   │                       ├── blob (app.py)
   │                       └── blob (util.py)
   └──parent──► previous commit ──► ... (history)
```

Each commit → one tree → many blobs/subtrees. History is the **parent chain** between commits.

---

## Refs — Named Pointers into the Object Graph

A **ref** is simply a file containing a **commit (or object) hash**. Refs are what make the object graph *human-usable* — without them, you'd have only a pile of hashes.

Location: `.git/refs/` and `.git/packed-refs`

### Types of refs

| Ref | File | Points to | Moves? |
|-----|------|-----------|--------|
| **Branch** | `.git/refs/heads/main` | A commit | ✅ Yes — advances on each commit |
| **Remote branch** | `.git/refs/remotes/origin/main` | A commit | ✅ On fetch |
| **Tag** | `.git/refs/tags/v1.0` | Commit or tag object | ❌ Fixed (unless forced) |
| **HEAD** | `.git/HEAD` | A branch *or* a commit | ✅ Follows current branch |

### What they look like

A **branch ref** is literally a file with one hash:

```
$ cat .git/refs/heads/main
3c4e9cd789d88d8d89c1073707c3585e41b0e614
```

A **lightweight tag ref**:

```
$ cat .git/refs/tags/v0.9
3c4e9cd789d88d8d89c1073707c3585e41b0e614
```

**HEAD** (normally points to a branch, i.e. "symbolic ref"):

```
$ cat .git/HEAD
ref: refs/heads/main
```

**Detached HEAD** (points directly at a commit):

```
$ cat .git/HEAD
3c4e9cd789d88d8d89c1073707c3585e41b0e614
```

---

## Putting it all together

```
REFS (human names)              OBJECTS (content-addressed data)
─────────────────               ────────────────────────────────
HEAD ──► refs/heads/main ──► commit C ──tree──► tree ──► blobs
                              │
refs/heads/feature ─────────► commit B ──tree──► tree ──► blobs
                              │
                              └─parent──► commit A ──► ...
refs/tags/v1.0 ─────────────► commit A
```

- **Refs** are the *entry points* (branches, tags, HEAD, remotes).
- **Objects** are the *data* (blobs, trees, commits, annotated tags).
- Everything is reachable by walking hashes from a ref.

---

## Why this design matters

1. **Deduplication** — identical file content = one blob, no matter how many commits reference it.
2. **Cheap branching** — a branch is a 41-byte file. Creating one is instant.
3. **Immutability** — objects never change; history is tamper-evident (hashes chain).
4. **Snapshots, not diffs** — each commit is a full tree; Git computes diffs lazily and efficiently.
5. **Integrity** — a corrupted object breaks its hash, so Git detects it.

---

## Handy inspection commands

```bash
git cat-file -t <hash>     # object type (blob/tree/commit/tag)
git cat-file -p <hash>     # pretty-print contents
git cat-file -s <hash>     # size in bytes
git rev-parse HEAD         # resolve a ref to a hash
git ls-tree HEAD           # list a tree
git show --stat HEAD       # commit + changed files
git for-each-ref           # list all refs
```

**One-line summary:** *Refs are named pointers; commits are snapshots of trees; trees are directories of blobs; blobs are file contents. Walk from a ref → commit → tree → blobs, and you've read the entire repository.*