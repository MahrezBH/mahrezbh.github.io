---
title: "Git Worktrees: Stop Stashing and Stop Breaking Your Flow"
date: 2026-01-05
categories: [Git, Developer Productivity]
tags: [git, workflow, code-review]
---

If you’re still using `git stash` every time an urgent code review interrupts your workflow, you’re leaving productivity and sanity on the table. Moving away from the "stash dance" isn't just a minor optimization, it’s a fundamental shift in how we handle context switching and environment stability.

As a code reviewer, I’ve seen how context switching can destroy momentum. Here is the blueprint for how you should be handling urgent PRs.

## The 3-Phase Workflow Evolution

### Phase 1: Bypassing the Stash Dance
We’ve finally moved past the era of waiting for our IDEs to re-index. When you stash and switch branches, your local server crashes, your node_modules/dependencies often get confused, and your mental model breaks. Worktrees fix this by letting you check out a branch into a completely isolated directory.

**The Old Way (The Stash Dance):**
```bash
# ❌ Old & Disruptive
git stash push -m "WIP: halfway through feature"
git checkout urgent-pr-branch
# ... Wait for IDE to freeze and re-index ...
npm install # Oops, dependencies changed
# ... Do review ...
git checkout feature-branch
git stash pop

```

**The New Way (Parallel Universes):**

```bash
# ✅ Standard
git worktree add ../urgent-pr-review urgent-pr-branch
# Open ../urgent-pr-review in a second IDE window. 
# Original workspace remains 100% untouched.

```

{% include tip.html content="**Pro Tip**: By using `../`, you place the new directory right next to your current project folder, keeping your workspace organized without accidentally nesting git repositories." %}

---

### Phase 2: True Isolation vs. The Alternatives

For years, if we wanted isolation, we either cloned the repo a second time (which wastes disk space and time) or just suffered through stashing. Worktrees share the same hidden `.git` folder under the hood, meaning they are incredibly fast and lightweight.

**The Comparison:**

| Method | Context Loss | Speed to Setup | Best Use Case |
| --- | --- | --- | --- |
| **Git Stash** | High (IDE re-indexes) | Fast | Pausing to pull updates on the *same* branch |
| **Second Clone** | None | Slow | Completely isolated client projects |
| **Git Worktree** | None | **Instant** | Urgent PR reviews & hotfixes |

---

### Phase 3: The Teardown

When you are done with a `stash`, you have to worry about merge conflicts when you `pop` it back. With a worktree, when the review is over, you simply throw the temporary desk in the trash.

**Implementation:**

```bash
# 1. You finished the review in the second IDE window.
# 2. Close that window.
# 3. From your original, untouched terminal:
git worktree remove ../urgent-pr-review

```

{% include warning.html content="**Critical**: Never put a worktree folder *inside* your current project directory unless it is explicitly added to your `.gitignore`. Otherwise, your primary IDE will try to index the nested project, causing massive lag." %}

---

## Critical Lessons for 2026

The jump to Git Worktrees is about **isolation** and **maintaining flow state**. If you're reviewing code today, don't let someone else's urgent PR break your local environment.

### My New Review Protocols:

1. **Never Stash for PRs:** I strictly reserve `git stash` for minor tweaks or pulling changes on my current branch. If the branch name changes, I use a worktree.
2. **Keep It Sibling:** Always use relative paths (`../folder-name`) to spawn the worktree as a sibling to your main project, not a child.
3. **Prune Regularly:** If you accidentally delete a worktree folder using your file explorer instead of the git command, run `git worktree prune` to clean up Git's internal tracking.
