# Week 4: Git & GitHub Advanced Challenge 

This challenge covers advanced Git concepts essential for real-world DevOps workflows. By the end of this challenge, you will:  

- Understand how to work with Pull Requests effectively.  
- Learn to undo changes using Reset & Revert.  
- Use Stashing to manage uncommitted work.  
- Apply Cherry-picking for selective commits.  
- Keep a clean commit history using Rebasing.  
- Learn industry-standard Branching Strategies.  

## **Topics Covered**  
1. Pull Requests – Collaborating in teams.  
2. Reset & Revert – Undo changes safely.  
3. Stashing – Saving work temporarily.  
4. Cherry-picking – Selecting specific commits.  
5. Rebasing – Maintaining a clean history.  
6. Branching Strategies – Industry best practices.  

## **Challenge Tasks**  

### **Task 1: Working with Pull Requests (PRs)**  

- Steps to create a PR.
  - For an existing issue, comment to claim it and avoid duplicate work. For new features, create an issue first to discuss the approach with the team.
  - Follow the project’s development setup guide to ensure your environment is ready.
  - Address a single issue per PR. Break changes into logical commits for easier review.
  - Use tools like eslint for automated style checks. Also, follow the project’s code style guidelines (if any).
  - Before putting up a PR, always test that your changes are working, so that you don't have to go back and fix things later.

- Best practices for writing PR descriptions.

  The PR description should include,
  - The issue that the PR addresses.
  - The changes made.
  - How the changes are tested.
  - Any areas needing special attention.

  Be concise but thorough to help reviewers understand your work. Make sure to use the project’s PR template to ensure you include all necessary details.

- Handling review comments.  
  - Address feedback with additional commits.
  - Always make sure test changes again after incorporating feedback.
  - To squash or amend commits (if necessary), use `git rebase -i`.
  - Resolve merge conflicts before merging.

---

### **Task 2: Undoing Changes – Reset & Revert**  

1. Create and modify a file.  
   ```bash
   echo "Wrong code" >> wrong.txt
   git add .
   git commit -m "Committed by mistake"
   ```  
2. Soft Reset (keeps changes staged).  
   ```bash
   git reset --soft HEAD~1
   ```  
3. Mixed Reset (unstages changes but keeps files).  
   ```bash
   git reset --mixed HEAD~1
   ```  
4. Hard Reset (removes all changes).  
   ```bash
   git reset --hard HEAD~1
   ```  
5. Revert a commit safely.  
   ```bash
   git revert HEAD
   ```  
**Differences between `reset` and `revert`**

| Feature          | `git reset` | `git revert` |
|-----------------|------------|-------------|
| Purpose         | Moves the HEAD and branch pointer to a previous commit, effectively removing commits from history. | Creates a new commit that undoes the changes of a previous commit, preserving history. |
| History Impact  | Rewrites commit history (potentially dangerous in shared repositories). | Maintains commit history intact, making it safer for collaboration. |
| Options         | `--soft`, `--mixed`, `--hard` determine how much is undone. | No options, it always creates a new commit reversing the changes. |
| Collaboration   | Not recommended for shared branches as it rewrites history. | Safe to use on shared branches since it does not modify past commits. | 

- When to use each method.  
  - **Reset** is used to clean up local commits. But not recommended for shared branches.
  - **Revert** is used to undo changes made by a commit without modifying history. It is safe to use on shared branches.

---

### **Task 3: Stashing - Save Work Without Committing**  
**Scenario:** You need to switch branches but don’t want to commit incomplete work.  

1. Modify a file without committing.  
   ```bash
   echo "Temporary Change" >> temp.txt
   git add temp.txt
   ```  
2. Stash the changes.  
   ```bash
   git stash
   ```  
3. Switch to another branch and apply the stash.  
   ```bash
   git checkout main
   git stash pop
   ``` 

**Difference between `git stash pop` and `git stash apply`:** 
- `git stash pop`: Applies the stashed changes and removes them from the stash list.
- `git stash apply`: Applies the stashed changes but keeps them in the stash list.

---

### **Task 4: Cherry-Picking - Selectively Apply Commits**  
**Scenario:** A bug fix exists in another branch, and you only want to apply that specific commit.  

1. Find the commit to cherry-pick.
   ```bash
   git log --oneline
   ```  
2. Apply a specific commit to the current branch.  
   ```bash
   git cherry-pick <commit-hash>
   ```  
3. Resolve conflicts if any.  
   ```bash
   git cherry-pick --continue
   ```  

**How cherry-picking is used in bug fixes :**
- Hotfixes – Applying a critical bug fix to multiple branches.
- Moving a specific commit to another branch without merging unrelated changes.


**Risks of cherry-picking :**  
- The same commit appears in multiple branches with different commit hashes, making history harder to track.
- Cherry-picked commits might depend on previous commits that are not included, leading to incomplete changes or broken functionality.

---

### **Task 5: Rebasing - Keeping a Clean Commit History**  
**Scenario:** Your branch is behind the main branch and needs to be updated without extra merge commits.  

1. Fetch the latest changes.  
   ```bash
   git fetch origin main
   ```  
2. Rebase the feature branch onto main.  
   ```bash
   git rebase origin/main
   ```  
3. Resolve conflicts and continue.  
   ```bash
   git rebase --continue
   ```  
 
**Difference between `merge` and `rebase`**  

Both of these commands are designed to integrate changes from one branch into another branch—they just do it in very different ways.

| Feature           | `git merge` | `git rebase` |
|------------------|------------|--------------|
| **Commit History** | Preserves the original history with a merge commit | Creates a linear history by rewriting commits |
| **Use Case** | When maintaining complete history with all branches is important | When a clean and linear history is desired |
| **Commit Hashes** | Existing commits remain unchanged | Commit hashes change due to history rewriting |
| **Effect on Collaboration** | Safe for team collaboration, does not rewrite shared history | Can cause issues if used on shared branches (forces history rewriting) |


- Best practices for rebasing.  
  - Never use it on public branches.
  - Don't use it unless you know what you are doing.

---

### **Task 6: Branching Strategies Used in Companies**  
**Scenario:** Understand real-world branching strategies used in DevOps workflows.  

1. Research and explain Git workflows:  
   - Git Flow (Feature, Release, Hotfix branches).  
   - GitHub Flow (Main + Feature branches).  
   - Trunk-Based Development (Continuous Integration).  

2. Simulate a Git workflow using branches.  
   ```bash
   git branch feature-1
   git branch hotfix-1
   git checkout feature-1
   ```  

**Which strategy is best for DevOps and CI/CD.**  
Both GitHub Flow and Trunk-Based development are good for DevOps and CI/CD.


**Pros and cons of different workflows.**  
- **Git Flow**
   #### **Pros:**
   - Well-organized workflow for large teams.  
   - Ensures a stable `main` branch.  
   - Ideal for versioned software releases.  

   #### **Cons:**
   - Can be complex with many branches.  
   - Slow for fast-moving CI/CD environments.

- **GitHub Flow**
   #### **Pros:**
   - Simpler and faster than Git Flow.  
   - Encourages rapid releases.  
   - Best suited for web applications and continuous deployment.  

   #### **Cons:**
   - No dedicated `develop` branch—every merge affects `main`.  
   - Lacks structured release/hotfix handling.

- **Trunk-Based Development**
   #### **Pros:**
   - Faster development and deployment.  
   - Encourages high collaboration.  
   - Reduces merge conflicts.  

   #### **Cons:**
   - Requires strong CI/CD automation.  
   - Risk of pushing unfinished features (mitigated using feature flags). 
