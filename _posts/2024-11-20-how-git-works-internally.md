---
title: "How Git Works Internally: Step-by-Step with Examples"
date: 2024-11-20
categories: [development]
tags: [git, backend]
---

Git is a tool many of us use every day, but most people don’t know how it actually works under the hood. It’s not just about commands like `git add` or `git push`. There’s a whole system behind it, and understanding this can make you a better developer.

In this post, I’ll explain Git step-by-step in simple terms, with examples to show how it works internally.

---

## **1. Git Stores Snapshots, Not Changes**

### What Most People Think:
When you commit changes, Git saves the differences (deltas) between the old version and the new one.

### What Really Happens:
Git doesn’t store differences. Instead, it saves a complete **snapshot** of your project at the time of the commit.

If a file hasn’t changed, Git doesn’t duplicate it—it just points to the same file from the previous snapshot.

#### **Example:**
Let’s say you have three files in your project:

```
file1.txt  
file2.txt  
file3.txt  
```

You make your first commit. Git saves the content of all three files.

Now you edit `file2.txt` and commit again. Git creates a new snapshot, but only the changed file (`file2.txt`) gets a new entry. The others just point back to the earlier snapshot.

---

## **2. Git Uses a Content-Addressable System**

When Git stores a file or commit, it gives it a unique name based on its content. This is done using a hash function (SHA-1). Think of it like a fingerprint for your data.

#### **Example:**
Run this command in any Git repository:
```bash
git hash-object your_file.txt
```

This will return a long string like:
```
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

That’s the file’s “name” in Git. If you change even one character in the file and run the command again, the hash will be completely different.

This is how Git ensures that your files are stored safely and can’t be tampered with.

---

## **3. The Three Key Areas of Git**

When you’re working in Git, your files move through three main areas:

- **Working Directory**: Where you edit your files.
- **Staging Area**: Where you prepare files for a commit (this is what `git add` does).
- **Repository**: Where Git stores the committed snapshots.

#### **Example:**
1. You create a file:
   ```bash
   echo "Hello Git" > file.txt
   ```

2. The file is in your working directory. Add it to the staging area:
   ```bash
   git add file.txt
   ```

3. Now commit it to the repository:
   ```bash
   git commit -m "Added file.txt"
   ```

Each step is important. Without adding the file (`git add`), Git won’t include it in the next snapshot.

---

## **4. Git’s Object Model**

Git stores everything (files, folders, commits) as objects. There are four main types:

- **Blob**: Stores file content.
- **Tree**: Represents a directory (points to blobs and other trees).
- **Commit**: Points to a tree and contains metadata (author, message, etc.).
- **Tag**: Points to a commit and gives it a readable name (like `v1.0`).

#### **Example:**
Let’s create a simple Git repository and see how objects are stored:

1. Initialize a repository:
   ```bash
   git init
   ```

2. Create a file and commit it:
   ```bash
   echo "Git Internals" > file.txt
   git add file.txt
   git commit -m "First commit"
   ```

3. Check the `.git/objects` folder:
   ```bash
   ls .git/objects
   ```

You’ll see files with strange names. These are the blob, tree, and commit objects that Git created for your snapshot.

---

## **5. How Git Handles Branches**

Branches in Git are just pointers to commits. They aren’t copies of your files—they’re lightweight labels that move as you commit new changes.

#### **Example:**
1. Create a new branch:
   ```bash
   git branch new-feature
   ```

2. Switch to it:
   ```bash
   git checkout new-feature
   ```

3. Make a change and commit it. The `new-feature` branch now points to this new commit, while the `main` branch stays where it was.

You can visualize branches as a graph. Each commit links to its parent, forming a tree-like structure.

---

## **6. How Git Handles Merges**

When you merge two branches, Git creates a new commit that combines changes from both branches.

#### **Example:**
1. Switch to the main branch:
   ```bash
   git checkout main
   ```

2. Merge the new feature branch:
   ```bash
   git merge new-feature
   ```

If there are conflicts (e.g., if both branches changed the same line in a file), Git will ask you to resolve them manually.

---

## **7. Understanding Git’s History**

Git’s history is like a graph, not a straight line. Every commit is a node, and the edges are the links between commits.

You can see this graph with:
```bash
git log --graph --oneline
```

This shows how branches and merges form a tree-like structure.

---

## **Why Understanding Git Internals Matters**

When you understand how Git works, you can troubleshoot problems, resolve conflicts, and work more efficiently. For example:

- Knowing about the staging area helps you avoid accidental commits.
- Understanding snapshots and hashes helps you trust Git’s integrity.
- Knowing branches are just pointers makes them less intimidating.

Git isn’t magic—it’s a simple system with some powerful ideas. Once you get the hang of it, you’ll never look at `git commit` the same way again.

## Resources to Learn More

If you’re excited to dig deeper into Git, here are some resources to guide you:

- **Official Git Documentation**:  
  The ultimate source for everything Git:  
  [https://git-scm.com/doc](https://git-scm.com/doc)

- **Pro Git Book**:  
  A free book covering Git basics to advanced topics:  
  [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)

- **Interactive Git Tutorial**:  
  Practice Git commands in your browser:  
  [https://learngitbranching.js.org/](https://learngitbranching.js.org/)

- **Git Internals PDF**:  
  A deep dive into Git’s internals by Scott Chacon:  
  [https://github.com/pluralsight/git-internals-pdf](https://github.com/pluralsight/git-internals-pdf)

---