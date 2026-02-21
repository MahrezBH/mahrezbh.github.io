---
title: "Stop Using git push --force"
date: 2026-01-09
categories: [Git, Developer Productivity]
tags: [git, workflow, code-review]
---

If you are still typing `git push --force` after a rebase, you are a ticking time bomb for your team's codebase. It’s the nuclear option of Git, and in 2026, relying on it is a rookie mistake.

As a code reviewer—and someone who has seen countless "I accidentally deleted my team's work" panics while climbing the Stack Overflow ranks—the standard `--force` flag makes my eye twitch. We have to rewrite history locally to keep commit trees clean, but pushing that history shouldn't mean playing Russian roulette with the remote branch.

Here is the blueprint for how you should be force-pushing today.

## The 2-Phase Evolution of the Force Push

### Phase 1: Understanding the "Blind Overwrite" (The Problem)
The standard `--force` flag does not care what is on the remote server. It does a blind overwrite. It tells the server: *"Make the remote look EXACTLY like my local machine, no matter what."*

Imagine this scenario:
1. You fetch `feature-branch` and start an interactive rebase locally to squash some messy commits.
2. Meanwhile, your teammate urgently pushes a critical hotfix to `feature-branch` on the remote.
3. You finish your rebase and run `git push --force`.

**Result:** You just permanently vaporized your teammate's hotfix. Git didn't warn you because you told it not to.

---

### Phase 2: The Conditional Overwrite (The Solution)
Enter the modern standard: `git push --force-with-lease`. 

This is the "thinking engineer's force push." It acts as a safety valve by doing a quick background check before executing the push. It verifies that the remote branch still looks exactly the way it did the last time you fetched it.

* **If the remote is untouched:** Git allows the force push. Your beautifully rebased history safely updates the remote.
* **If a teammate pushed new code:** Git **rejects** your push. It fails safely, letting you know there is new work you need to fetch and integrate before trying again.

**The Implementation:**

```bash
# ❌ Old & Dangerous
git push origin feature-branch --force

# ✅ 2026 Standard
git push origin feature-branch --force-with-lease

```

{% include warning.html content="**Critical**: For `--force-with-lease` to protect you, you must not run an automated background `git fetch` script that silently updates your local tracking branches. The lease is based on your *known* state of the remote." %}

---

## What the Official Git Docs Say

If you want to go straight to the source, the [official Git documentation for git-push](https://git-scm.com/docs/git-push#Documentation/git-push.txt---force-with-lease) explicitly outlines this safeguard.

According to the docs, `--force-with-lease` protects you from losing data when someone else has updated the branch:

> *"This option allows you to say that you expect the history you are updating is what you rebased and want to replace. If the remote ref still points at the commit you specified, you can be sure that no other people did anything to the ref. It is like taking a 'lease' on the ref without explicitly locking it, and the remote ref is updated only if the 'lease' is still valid."*

---

## Critical Lessons for 2026

The jump to `--force-with-lease` is about **team safety** and **predictability**.

### My New Push Protocols:

1. **Burn `--force` from your memory:** The only time `--force` is acceptable is if you are 100% solo on a private repository, and even then, it's a terrible habit to build.
2. **Alias the Lease:** You should never have to type that massive flag out. Add this to your `~/.gitconfig` today:

```ini
[alias]
    pf = push --force-with-lease

```

Now, simply typing `git pf` gives you all the power of rewriting history without the risk of destroying your teammates' work.
