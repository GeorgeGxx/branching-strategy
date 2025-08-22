
# GitFlow Rollback Guide

This document explains how to perform rollbacks in **GitFlow**, with step-by-step commands and a diagram of common rollback scenarios.

---

## 🔹 Step-by-step Example: Rollback in `master` (Production)

Imagine you released version **1.2.0** but it introduced a critical bug.  
You want to rollback to **1.1.0**, the last stable version.

### 1. Checkout `master` and create a hotfix from the stable tag
```bash
git checkout master
git checkout -b hotfix/rollback-v1.2 v1.1.0
```

### 2. Make changes if needed
If you just want to restore to v1.1.0 as-is, you can skip edits.  
Otherwise, apply fixes or revert specific commits.

### 3. Commit the rollback
```bash
git commit -m "Rollback: return to v1.1.0 state after failed v1.2.0 release"
```

### 4. Merge hotfix into `master`
```bash
git checkout master
git merge --no-ff hotfix/rollback-v1.2
```

### 5. Tag the rollback version
```bash
git tag -a v1.2.1 -m "Rollback release: reverted v1.2.0 to v1.1.0"
```

### 6. Merge hotfix into `develop` (to keep branches in sync)
```bash
git checkout develop
git merge --no-ff hotfix/rollback-v1.2
```

### 7. Push everything
```bash
git push origin master develop --tags
```

✅ Now production is rolled back, and both `develop` and `master` are consistent.

---

## 🔹 Diagram: Rollback Scenarios in GitFlow

```
           (Feature rollback)         (Release rollback)
feature/*  ------------> revert/reset  -------------> fix or recreate
                |
                v
develop  --------> revert merge ----------------------+
                                                      |
                                                      v
release/*  -------------> fix bugs / delete+recreate  |
                                                      |
                                                      v
master  -----> if bug released ----> hotfix branch --> merge to master + develop
```

- **Feature rollback**: reset/revert commits.  
- **Develop rollback**: revert a merge commit.  
- **Release rollback**: fix directly or recreate branch.  
- **Master rollback**: create hotfix from last stable commit → merge into master & develop.

---

👉 This way, GitFlow keeps its rules:  
- No history rewriting in shared branches.  
- Rollbacks are explicit and traceable.  
