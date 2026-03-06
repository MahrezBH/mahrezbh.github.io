---
title: "I Accidentally Dropped My Git Stash: Recovering Lost Code with Dangling Commits"
date: 2026-03-06
categories: [Git, Developer Productivity]
tags: [git, debugging, code-recovery]
---

I ran into a Git nightmare recently, and it’s a scenario I feel obligated to share because it will happen to you eventually. 

I was deep into a ticket, about 90% done with the feature. Suddenly, a higher-priority task dropped on my desk. I did the standard routine: I stashed my updates to clean my working directory and jumped over to the urgent fire. 

A few days later, the fire was out, and I went to resume my 90%-finished ticket. I checked my stash list and realized that at some point during the chaos... I had accidentally deleted the stash. 

Don't judge. We’ve all been there. 

Before you accept defeat and rewrite a week's worth of code, you need to understand one of the most powerful hidden gems in Git's architecture.

## The Invisible Safety Net: Dangling Commits

Git is fundamentally a hoarder. It almost never deletes your data immediately. 

When you create a branch and then delete it, or when you create a stash and run `git stash drop` (or `clear`), Git doesn't actually erase the code from your hard drive. Instead, it just removes the *pointer* to that code. The commit itself is still fully intact, but it is now "orphaned." 

In Git terminology, this is known as a **Dangling Commit**.



Because it no longer has a branch or a stash reference pointing to it, this commit becomes invisible to standard commands like `git log`. But it is still there, floating in the void of your `.git` directory.

Here is the exact blueprint for how I rescued my task and how you can rescue yours.

---

## The 3-Phase Rescue Operation

### Phase 1: Finding the Ghost (`git fsck`)
Since `git log` won't help you find a dangling commit, we have to bypass the standard history and ask Git to check its internal file system directly. 

We do this using the File System Check (`fsck`) command with the `--lost-found` flag.

**The Command:**
```bash
git fsck --lost-found
```

**The Output:**
Git will spit out a list of hashes that are no longer reachable. It will look something like this:

```text
Checking object directories: 100% (256/256), done.
dangling commit 8a9b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b
dangling blob 1f2e3d4c5b6a79887766554433221100ffeeddcc
dangling commit 5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d
```

You are looking for the `dangling commit` lines. To figure out which one is your lost stash, you can inspect them one by one using `git show <hash>`.

```bash
git show 8a9b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b
```

Once you see your lost code on the screen, copy that hash. You’ve found it.

---

### Phase 2: The Resurrection

Now that we have the exact commit reference, we just need to re-attach it to our active workflow. You have two options here: you can turn it into a new branch, or you can turn it back into a stash.

Since it was originally a stash, let's put it right back where it belongs.

**The Command:**

```bash
git stash store -m "restored updates: thank god" <your-commit-hash>
```

Just like that, if you run `git stash list`, your code is safely back in the queue. You just saved yourself hours of rewriting logic from scratch.

---

### Phase 3: Understanding the Time Limit

There is a catch. Dangling commits don't live forever.

Git has an internal Garbage Collector (`git gc`) that runs periodically in the background. Its job is to permanently delete these orphaned commits to save disk space.

By default, Git retains unreachable commits for roughly 30 to 90 days. If you realize you dropped a stash two months ago, you might be out of luck. However, as a senior engineer, you can control this retention period.

If you want a longer safety net, you can extend the expiration limit globally:

```bash
git config --global gc.reflogExpire "180 days"
```

---

## Critical Lessons

The panic of losing code is usually worse than the reality. The core philosophy of Git is immutability—it tries very hard to prevent you from shooting yourself in the foot.

### My Recovery Protocols:

1. **Never panic-rewrite:** If you drop a stash, delete an unmerged branch, or botch a hard reset, stop typing. The code is still there.
2. **Master `git fsck`:** `git reflog` is great for things you *committed* and lost, but `git fsck --lost-found` is the ultimate tool for recovering stashes and deeply orphaned data.
3. **Store it immediately:** Once you find the hash in the void, immediately tag it, branch it, or `stash store` it before the garbage collector wakes up.
