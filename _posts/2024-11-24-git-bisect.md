---
title: "Git Trick: Using git bisect to Find Bugs Efficiently"
date: 2024-11-24
categories: [development]
tags: [git, backend]
---

#### What Is `git bisect`?  
`git bisect` is a powerful debugging tool that helps you identify the specific commit where a bug was introduced. It uses a binary search algorithm, making the process significantly faster than manually checking commits.

---

#### How It Works:  

1. **Start Bisecting**  
   Run the following command to start:
   ```bash
   git bisect start
   ```

2. **Mark the Good and Bad Commits**  
   - Identify a known "bad" commit (where the bug exists):
     ```bash
     git bisect bad
     ```
   - Identify a known "good" commit (before the bug was introduced):
     ```bash
     git bisect good <commit-hash>
     ```

3. **Test Intermediate Commits**  
   Git automatically checks out an intermediate commit between the "good" and "bad" points. You test it and mark it:
   - If the commit has the bug:
     ```bash
     git bisect bad
     ```
   - If the commit works correctly:
     ```bash
     git bisect good
     ```

4. **Repeat Until Found**  
   Git continues narrowing down until it identifies the problematic commit.

5. **Finish the Bisect**  
   Once done, reset back to your original branch:
   ```bash
   git bisect reset
   ```

---

#### Why It's a Must-Know for Experts:  
- **Efficiency**: Pinpoint the problematic commit in large codebases with thousands of commits.  
- **Team Collaboration**: Quickly identify when and where bugs were introduced in a shared repository.  
- **Automation**: Combine with scripts to automatically run tests on each commit, reducing manual effort.

---

Here’s how to use `git bisect` in a **real-world case** to find the exact commit that introduced a bug

---

### Scenario:  
You're working on a web application, and you notice the **login feature** is broken. It was working a week ago, but now it fails to authenticate users. The codebase has hundreds of commits since then, and you need to find which commit introduced the bug.

---

### Steps to Use `git bisect`:

#### 1. **Ensure the Repository is Clean**  
Before starting, ensure your working directory has no uncommitted changes:  
```bash
git status
```
If there are changes, either stash them or commit them.

---

#### 2. **Start Bisecting**  
Run:  
```bash
git bisect start
```

---

#### 3. **Mark the Buggy (Bad) Commit**  
Identify a commit where the bug exists (e.g., the latest commit on the main branch):  
```bash
git bisect bad
```

---

#### 4. **Mark the Last Known Good Commit**  
Find a commit from a week ago when you know the login feature worked:  
```bash
git bisect good <commit-hash>
```

> **Tip**: Use `git log` or a graphical tool to identify an older commit where everything worked correctly.

---

#### 5. **Test Intermediate Commits**  
Git will now check out a commit halfway between the "good" and "bad" commits.  
- Test the **login feature**:
  - If the feature works (good):
    ```bash
    git bisect good
    ```
  - If the feature is broken (bad):
    ```bash
    git bisect bad
    ```

---

#### 6. **Repeat**  
Git will continue to check out commits in the middle of the remaining range. You test each commit and mark it as "good" or "bad." This process continues until Git narrows down to a single commit.

---

#### 7. **Identify the Problematic Commit**  
Git will eventually output something like this:  
```
The first bad commit is:
commit abc123def456
Author: Developer Name
Date: Tue Nov 21 15:34:22 2024 +0300
    Refactored login logic
```

---

#### 8. **Investigate the Commit**  
You can now inspect the commit to identify what caused the bug:
```bash
git show abc123def456
```

---

#### 9. **Reset Bisect**  
Once done, return to your original branch:
```bash
git bisect reset
```

---

### Real-World Enhancements:

1. **Automate Testing**  
   If the issue can be detected by a script (e.g., a failed test), automate the process:  
   ```bash
   git bisect run ./test_login.sh
   ```

2. **Collaborate with the Team**  
   Share the problematic commit details with your team to fix the issue or understand the changes.

3. **Document the Finding**  
   Add comments to the bug tracker or create a pull request for the fix referencing the problematic commit.

---

### Example Output:  

Let’s say the buggy commit introduced a typo in the authentication function:  
```diff
- if (user.password == storedPassword) {
+ if (user.password = storedPassword) {
```

After finding the issue, you can quickly fix it and prevent further regressions.

---

Using `git bisect` ensures you pinpoint bugs **faster** and more **systematically**, even in large, complex codebases.
