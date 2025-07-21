## Question 1 
What is the difference between `git fork` and `git clone`, and when would you use each?

## ✅ Answer  
- **`git fork`** creates a **copy of a repository on your GitHub (or GitLab, etc.) account**, letting you propose changes without write access to the original repo.
- **`git clone`** creates a **local copy of any Git repository** (your own or someone else’s) on your machine for development.
  
When you **fork** a repository on GitHub, you're telling the platform:  
> "I want a separate version of this repository in my own GitHub account."

This is especially useful for contributing to open-source and team projects where you don't have direct write access to the main repository. You fork the repo, make changes in your fork, and then create a **pull request** to propose those changes to the original project.

On the other hand, **`git clone`** is used to **download a repository (forked or original) to your local development machine**. This is what actually gives you the codebase to work with.

Here’s how you’d typically use both:
1. **Fork** the repo on GitHub (creates a copy under your GitHub username).
2. **Clone** your fork locally using:  
   ```bash
   git clone https://github.com/your-username/the-repo.git
   ```

> So: **Fork = GitHub-level action**, **Clone = Local machine-level action**.
---

## Question 2
What is the difference between `git fetch` and `git pull`, and when would you use each?

## ✅ Answer  
- `git fetch` retrieves the latest changes from the remote repository **without merging** them into your current branch.
- `git pull` does the same as `fetch` but **also automatically merges** the changes into your current branch.

When you work with Git, especially in a team setting, you often need to keep your local repository up-to-date with the remote repository. This is where `git fetch` and `git pull` come into play.
 
When you run `git fetch`, you’re asking Git to contact the remote (like GitHub) and download any changes (new commits, branches, tags) — **but not apply them** to your working directory.

```bash
git fetch origin
```

This is useful when:
- You want to see what others have pushed
- You're preparing for a manual merge or rebase
- You want to avoid surprise changes to your working branch

With `git pull`, you're doing this **plus** merging the changes into your current branch in one step:

```bash
git pull origin main

I mostly use `git pull` because it streamlines my workflow by fetching and merging remote changes in one step. It’s convenient for staying up to date quickly, especially when collaborating in fast-moving branches.
However, I also use `git fetch` when I want to review changes before merging them into my branch. This gives me a chance to inspect the incoming changes, check for potential conflicts, or decide if I want to rebase instead of merging.
```
`git pull` is essentially a shortcut that performs both a `git fetch` and a `git merge`. Instead of running two separate commands, I prefer to use:

```bash
git pull origin main
```

This makes my routine faster and keeps my local branch synchronized with the remote without extra steps. It's particularly useful when:
- I’m working alone or in a small team where merge conflicts are rare
- I'm contributing to a feature branch that others aren’t modifying
- I want to frequently pull in the latest changes to test or deploy updates

Of course, I stay cautious by committing or stashing local changes before pulling to avoid conflicts or interrupted workflows. And if I suspect major upstream changes or want a closer look, I’ll temporarily switch to `git fetch`.

But for my day-to-day development, especially in active branches, `git pull` keeps things fast and simple — and that makes it my go-to.

---

## Question 3 
What is the difference between `git rebase` and `git merge`? When would you use each?

## ✅ Answer  
- `git merge` integrates changes by creating a new merge commit, preserving the history of both branches.
- `git rebase` moves your branch on top of another, rewriting commit history to create a linear sequence.
- `github pull request merge uses git commit merge` to combine changes from one branch into another, preserving the history of both branches.

### 📘 Detailed Explanation  
Let’s say you have two branches:
- `main`
- `feature` (branched off earlier from `main`)

#### 👉 Using `git merge`:
```bash
git checkout feature
git merge main
```

This pulls changes from `main` into `feature` and creates a **merge commit**, like this:
```
A---B---C (main)
     \
      D---E---F (feature)
             \
              G (merge commit)
```

**Pros:**
- Preserves full history and context
- Safer in teams: no history rewriting
- Good for long-lived shared branches

**Cons:**
- History becomes messy with many merge commits
- Harder to trace linear commit flow

---

#### 👉 Using `git rebase`:
```bash
git checkout feature
git rebase main
```

This **re-applies your commits on top of the latest `main`**, like this:
```
A---B---C (main)
             \
              D'---E'---F' (rebased feature)
```

**Pros:**
- Clean, linear history
- Easier to `git log` and `git bisect`
- Preferred before merging short-lived branches into main

**Cons:**
- Rewrites commit history
- Risky if already pushed and others have based work on it
- Not ideal for shared/public branches

---

### 🧠 When to Use What

| Use `merge` when...            | Use `rebase` when...                  |
|-------------------------------|---------------------------------------|
| You're collaborating on shared branches | You're working alone or before a PR merge |
| You want to preserve commit context     | You want a clean, linear history          |
| History safety is a concern             | You're cleaning up before pushing         |

> Summary:  
> Use `merge` to combine, use `rebase` to simplify.

---

## Question 4
Explain the Git branching strategy you used in your company. Align it with the open-source branching strategy followed by Kubernetes.

## ✅ Answer  
In my company, we followed a well-structured Git branching model similar to the Kubernetes project's workflow. Our strategy centered around four key branches:

- `main` – the default and stable development branch  
- `feature/*` – for all new features and enhancements  
- `release/*` – for preparing and testing production releases  
- `hotfix/*` – for urgent bug fixes or patches to production

This helped us maintain stability while enabling parallel development and quick recovery from issues.

### 📘 Detailed Explanation  

#### 🔹 `main` branch
- Equivalent to `master` or `main` in Kubernetes.
- Represents the **latest development state** — stable but evolving.
- All feature branches are branched off from here.
- Protected with branch rules and mandatory code reviews.

> Developers do not commit directly to `main`. All changes go through pull requests.

---

#### 🔹 `feature/*` branches
- Used for individual features or enhancements.
- Named like `feature/login-api` or `feature/cleanup-metrics`.
- Created from `main`.
- Developers work independently and raise PRs when done.

> We squash commits before merging to keep the history clean.

---

#### 🔹 `release/*` branches
- Cut from `main` when preparing for a release, e.g., `release/1.4`.
- Only allows **bug fixes, performance improvements, and docs**.
- CI pipelines run regression tests and validations here.
- Used for staging deployments and QA approvals.

> Kubernetes also creates release branches (e.g., `release-1.28`) to stabilize features after code freeze.

---

#### 🔹 `hotfix/*` branches
- Created from the latest release tag or `main`, based on urgency.
- Used when we need to fix critical bugs directly on production without waiting for the next release cycle.
- After fixing and testing, changes are merged back to both `main` and the relevant `release/*` branch.

> This ensures the fix is available in both the short term and future releases.

---

### ✅ Benefits of this Strategy:
- Supports **parallel development** and **safe releases**
- Keeps `main` clean and always deployable
- Makes it easy to trace features and bug fixes
- Aligns well with CI/CD automation and changelog generation

---

> By following this branching strategy, we maintained agility without compromising stability — which is critical in both enterprise and open-source scale environments like Kubernetes.

---

## Question 5
What is the difference between `git reset`, `git revert`, and `git checkout`? When would you use each?
## ✅ Answer
- `git reset` changes the current branch's commit history, moving the HEAD pointer to a previous commit.
- `git revert` creates a new commit that undoes the changes made by a previous commit, preserving history.
- `git checkout` switches branches or restores files to a specific state.
### 📘 Detailed Explanation
#### 🔹 `git reset`
- **Purpose**: Move the HEAD pointer to a previous commit, effectively changing the commit history.
- **Usage**:
  ```bash
  git reset --hard <commit>
  ```
- **When to Use**:
  - When you want to **discard commits** and reset your branch to a previous state.
  - When you want to **remove commits from history** (e.g., before pushing to
    a shared repository).
- **Types**:
    - `--soft`: Moves HEAD but keeps changes staged.
    - `--mixed`: Moves HEAD and unstages changes (default).
    - `--hard`: Moves HEAD and discards all changes (use with caution).
#### 🔹 `git revert`
- **Purpose**: Create a new commit that undoes the changes made by a previous commit, preserving the commit history.
- **Usage**:
    ```bash
    git revert <commit>
    ```
- **When to Use**:
    - When you want to **undo changes without altering history**.
    - When you need to **safely revert changes in a shared repository**.
- **Example**: If you accidentally pushed a commit that broke functionality, you can revert it
    to restore the previous state without losing the commit history.
#### 🔹 `git checkout`
- **Purpose**: Switch branches or restore files to a specific state.
- **Usage**:
    ```bash
    git checkout <branch>
    git checkout <file>
    ```
- **When to Use**:  
    - When you want to **switch to a different branch**.
    - When you want to **restore a file to a previous state**.
- **Example**: If you want to switch from the `main` branch to a `feature` branch, you would use:
    ```bash
    git checkout feature-branch
    ```
### ✅ Summary Table    
| Command       | Purpose                                         | Changes History? | Use Case                                      |
|---------------|-------------------------------------------------|------------------|-----------------------------------------------|
| `git reset`   | Move HEAD to a previous commit                  | Yes              | Discard commits, reset branch state           |
| `git revert`  | Create a new commit that undoes changes         | No               | Safely undo changes in shared repositories     |
| `git checkout`| Switch branches or restore files                | No               | Change branches, restore file states          |

## Question 6 
Explain challenges you faced while using Git in your work experience.

## ✅ Answer  
1. **Merge Conflicts During Pulls**  
   I used to rely heavily on `git pull` without checking what changes others had pushed. This led to merge conflicts, especially in fast-moving branches. Eventually, I switched to using `git fetch` followed by a manual `merge` or `rebase`, which gave me more control over how I integrated changes.

2. **Messy Commit History with Frequent Merges**  
   Early in my career, I used `git merge` frequently while working on long-lived feature branches. It cluttered the history with multiple merge commits, making it difficult to follow the actual changes. I learned to use `git rebase` (before pushing) to create a clean, linear history — especially before opening pull requests.

3. **Confusion Between Fork and Clone in Open-Source Work**  
   When I first started contributing to open-source, I cloned repositories directly and couldn’t push my changes. I realized I should’ve used `git fork` to create my own copy of the repo on GitHub. After forking, I was able to push changes to my own version and submit pull requests to the original repository.

These challenges reflect how Git is powerful but not always beginner-friendly:
- **Merge conflicts** are a common problem in collaborative teams. Using `git fetch` and reviewing changes before merging helped me avoid surprise conflicts.
- **Messy commit history** can make debugging or code reviews painful. Switching to `rebase` in local branches before pushing made the history easier for teammates to follow.
- **Forking confusion** taught me about GitHub’s collaboration model. Understanding when to fork vs when to clone was key to contributing effectively to open-source.

- A recent challenge I faced was implementing a consistent Git branching strategy across 100+ repositories used by multiple teams in my organization. Each team followed their own style — some had `main/dev`, others used `master/feature`, and a few used no long-lived branches at all. This inconsistency made CI/CD pipelines error-prone, releases chaotic, and collaboration difficult.

To solve this, we standardized on a lightweight **trunk-based branching strategy** with well-defined rules around `main`, `release/*`, and `feature/*` branches — and rolled it out in phases across teams.

At a company-wide level, multiple teams were independently managing Git repos, and the lack of a unified branching approach caused several issues:
- CI pipelines were failing due to missing expected branches like `main` or `release`.
- Some teams rebased public branches, which broke collaborators' work.
- Merge conflicts were common in integration environments.
- Releases were often delayed due to confusion about which branch was production-ready.

Here’s how I addressed it:

#### ✅ Step 1: Analyze the current state
- Audited all repositories using automation (GitHub/GitLab APIs).
- Documented the branching models and naming conventions each team used.

#### ✅ Step 2: Design a unified strategy
- Proposed a **trunk-based development** model:
  - `main` → always production-ready
  - `release/x.y` → for stabilization and hotfixes
  - `feature/*` → short-lived, rebased before merge
- Outlined rules for using `merge`, `rebase`, and protection policies.

#### ✅ Step 3: Rollout with tooling & education
- Created templates (starter repos) with the correct branch structure.
- Set up default branch protections and PR requirements using GitHub Actions and branch policies.
- Ran onboarding sessions and created a lightweight Git handbook tailored to our strategy.

#### ✅ Step 4: Iterate with feedback
- Incorporated feedback from platform, dev, and QA teams.
- Adjusted the policy to allow for temporary exceptions during migration.

---

The result was:
- 95%+ repos aligned within 2 months.
- CI/CD pipeline reliability improved significantly.
- Teams were clearer on how and when to branch or merge.
- It became easier to onboard new developers and automate release workflows.

---
This experience taught me the importance of a consistent Git strategy in large organizations. It’s not just about using Git commands correctly; it’s about creating a shared understanding and workflow that everyone can follow, which ultimately leads to better collaboration and productivity.

## Question 7
How do you handle merge conflicts in Git?

## ✅ Answer  
When I encounter a merge conflict, I pause to understand which files are affected, examine the conflicting changes, and manually resolve them using a visual diff tool or editor. Once resolved, I mark the conflicts as resolved, stage the changes, and complete the merge or rebase.
  
Merge conflicts usually happen when:
- Two branches modify the same lines in a file
- One branch deletes a file the other modifies
- A rebase applies commits that overlap with existing changes

Here’s how I handle them:

#### 🔍 Step 1: Identify the conflict
Git clearly indicates the files with conflicts during a `git merge` or `git rebase`:
```bash
Auto-merging app.py  
CONFLICT (content): Merge conflict in app.py  
```

#### 🛠️ Step 2: Open and resolve the conflict
Conflicted files contain markers like:

```diff
<<<<<<< HEAD
print("Hello from main")
=======
print("Hello from feature-branch")
>>>>>>> feature-branch
```

I manually edit the file to reflect the correct final code, based on the intended logic. Sometimes I use tools like:
- `git diff` to understand the changes
- Visual Studio Code or GitKraken for visual resolution

#### ✅ Step 3: Mark as resolved
Once edited:
```bash
git add app.py
```

Then complete the merge:
```bash
git commit         # if using merge
# or
git rebase --continue  # if using rebase
```

## Question 8
What are `ours` and `theirs` strategies in Git merges? How and when are they used?

## ✅ Answer  
- **`ours`** strategy favors **your current branch’s changes**, even if the other branch has different content.
- **`theirs`** isn't directly available as a merge strategy but can be used during conflict resolution in a rebase or a manual merge to **accept incoming changes over yours**.

### 📘 Detailed Explanation  

#### 🧩 `ours` Strategy (as a Git merge strategy)
When running a merge, you can tell Git:
> “Even though we’re merging, if there’s a conflict — pick my current branch’s version.”

Used like this:
```bash
git merge -s ours feature-branch
```

🧠 **Important:** This does *not* mean it merges and keeps both sets of changes. It **pretends to merge** but **keeps only your current branch's content**, marking the merge as done.

📌 **Use cases:**
- When rolling back a hotfix and keeping current stable state
- When merging a long-dead branch just to close it but keep your branch's state intact

---

#### 🧩 `theirs` Strategy (used manually during conflicts)
There is **no `-s theirs` strategy** at the command line merge level.

However, when resolving a conflict manually, you can choose the **incoming branch's changes** using:
```bash
git checkout --theirs conflicted_file.txt
git add conflicted_file.txt
```

This tells Git:
> “For this conflicted file, discard my local version and use the version from the branch I’m merging in.”

📌 **Use cases:**
- When the incoming changes are definitely correct
- During `git rebase` when resolving repetitive or well-understood conflicts

---

### 🧠 Summary Table

| Strategy  | Behavior                                      | Use When...                                             |
|-----------|-----------------------------------------------|----------------------------------------------------------|
| `ours`    | Keeps **current branch’s** content             | You want to keep your version, discard the incoming one |
| `theirs`  | Keeps **incoming branch’s** content            | You want to override with the other branch’s changes    |

---

## Question 9
Have you ever used Git tags? If yes, why?

## ✅ Answer  
Yes, I’ve used Git tags primarily to mark release versions of our applications. It helps track which commit corresponds to a production deployment, and makes it easier to roll back or audit changes when needed.

In one of my recent projects, we followed a simple release process where every stable build that passed all stages in our CI/CD pipeline was tagged with a version number — like `v1.0.3`.

I used annotated tags to add context:

```bash
git tag -a v1.0.3 -m "Release version 1.0.3 with critical bug fixes"
git push origin v1.0.3
```

These tags were then picked up by Jenkins and used as part of the deployment name — so we always knew what version was running in production.

#### 🔍 Why Git Tags Are Useful:
- 🎯 **Marking release points:** Helps indicate stable or milestone commits
- 🔄 **Rollback support:** Easily check out a tag to return to a known good state
- 🧪 **Versioned builds:** Many CI systems trigger builds based on tags
- 🔖 **Consistent releases:** Tags act like bookmarks for deployments or patch notes

> In summary: I use Git tags to improve visibility, traceability, and control in software releases — they’re lightweight, powerful, and essential in production workflows.

---

## Question 10 
How do you combine multiple commits into a single commit in Git?

## ✅ Answer  
I use **interactive rebase** to squash multiple commits into a single, meaningful commit before pushing my changes. This helps in keeping the Git history clean and easy to read.

```bash
git log --oneline
abc123 Fix typo  
def456 Add input validation  
ghi789 Update error message  
jkl012 Initial work on login form  
```

Before pushing or raising a PR, I might want to **combine them into a single commit** that says something like:  
`Add login form with validation and error handling`

#### ✅ Here’s how I do it:

```bash
git rebase -i HEAD~4
```

This opens an editor with the last 4 commits:
```text
pick jkl012 Initial work on login form
pick ghi789 Update error message
pick def456 Add input validation
pick abc123 Fix typo
```

I change all but the first `pick` to `squash` or `s`:
```text
pick jkl012 Initial work on login form
squash ghi789 Update error message
squash def456 Add input validation
squash abc123 Fix typo
```

Then I write a new commit message when prompted, save, and exit.

Finally:
```bash
git push origin branch-name --force
```

> Note: You only force-push if the commits were already pushed to remote. Otherwise, a normal push is fine.

---

### 🧠 Why This Is Useful  
- Keeps Git history clean and easier to understand
- Combines all related changes into one atomic commit
- Helpful for reviewers and future debugging
- Commonly used before merging a feature branch into `main`

> This is often referred to as **squashing commits**, and it's a best practice when preparing PRs or fixing review feedback across multiple small commits.

---

## Question 11
Explain 10 Git commands that you use on a day-to-day basis. What are they used for?

## ✅ Answer  
Here are 10 Git commands I use regularly and what I use them for:

---

### 1. `git clone`
```bash
git clone https://github.com/org/repo.git
```
📦 Used to create a local copy of a remote repository when starting a new task or joining a project.

---

### 2. `git status`
```bash
git status
```
🧭 Shows the current state of the working directory — what’s staged, unstaged, and untracked.

---

### 3. `git add`
```bash
git add file.py
git add .
```
📝 Stages changes before committing. I use this before `git commit` to mark files I want to include.

---

### 4. `git commit`
```bash
git commit -m "Fix login bug"
```
💾 Saves a snapshot of the staged changes with a message describing the purpose.

---

### 5. `git push`
```bash
git push origin feature-branch
```
🚀 Uploads local commits to the remote repository, usually after a commit or merge.

---

### 6. `git pull`
```bash
git pull origin main
```
🔄 Fetches changes from the remote and merges them into the current branch — helps keep in sync with team updates.

---

### 7. `git fetch`
```bash
git fetch origin
```
📥 Downloads changes from the remote without merging — I use this when I want to review changes before integrating.

---

### 8. `git branch`
```bash
git branch
git branch feature/login
```
🌿 Lists all branches or creates a new one. Branching is the foundation of my feature workflows.

---

### 9. `git checkout`
```bash
git checkout main
git checkout -b hotfix/typo
```
🧳 Switches between branches or creates and switches in one go. I use this constantly during development.

---

### 10. `git rebase`
```bash
git rebase main
```
📚 Re-applies commits from one branch on top of another. Useful for maintaining a clean commit history before merging.

---

## Question 12
I want to ignore pushing changes to a specific file in Git. How can I do it?

## ✅ Answer  
To ignore future changes to a tracked file, I use the `assume-unchanged` flag. This tells Git to stop checking the file for changes, even though it's still in the repo.

```bash
git update-index --assume-unchanged path/to/your/file
```
  
There are two main scenarios when you don’t want a file to be pushed:

---

### ✅ 1. If the file is **already tracked**, and you want Git to **stop tracking changes**:

Use:
```bash
git update-index --assume-unchanged file.txt
```

This keeps the file in the repo, but Git will act like it hasn’t changed — useful for config files that differ by environment.

🔁 To undo this and start tracking again:
```bash
git update-index --no-assume-unchanged file.txt
```

📌 Common use cases:
- `.env` files with local credentials
- `settings.json` or editor-specific config
- Scripts that are tweaked temporarily

---

### 🚫 2. If the file should never be tracked:

Add it to `.gitignore` **before** committing it:
```
# .gitignore
.env
*.log
```

This works **only for untracked files**. If it’s already committed once, `.gitignore` won’t help unless you untrack it first:

```bash
git rm --cached file.txt
echo file.txt >> .gitignore
```

Then:
```bash
git commit -m "Stop tracking file.txt"
```

---

### ⚠️ Note:
`assume-unchanged` is a **local-only** flag. It won’t prevent others from seeing changes or pushing the file. It’s a lightweight trick, but not a security mechanism.

> Summary:  
> Use `.gitignore` for new/untracked files.  
> Use `assume-unchanged` for tracked files you don’t want to accidentally push.

---

## Question 13
What is the purpose of the `.git` folder in a Git repository?

## ✅ Answer  
The `.git` folder contains all the metadata, configuration, and object database Git needs to manage version control. It is what transforms an ordinary directory into a Git repository. 

When you run:
```bash
git init
```
Git creates a hidden directory called `.git/` in the root of your project. This folder stores everything Git needs to track versions, branches, commits, and configurations.

Here’s what it typically contains:

#### 🗂️ Key Contents of `.git/`:

- **`HEAD`**  
  A reference to the current checked-out branch. It tells Git “where you are” in the repo.

- **`config`**  
  Local repository settings like remote URLs, user info, aliases, etc.

- **`objects/`**  
  The actual content of your codebase — all commits, trees, and blobs are stored here in a compressed format.

- **`refs/`**  
  Contains references to all branches and tags (like `refs/heads/main` or `refs/tags/v1.0.0`).

- **`logs/`**  
  Records of updates to refs (used for debugging and recovery, e.g., with `git reflog`).

- **`index`**  
  The staging area — where files go after `git add` and before `git commit`.

---

### 🔐 Why It’s Important:
- Without the `.git` folder, your project is no longer a Git repository.
- If you delete or corrupt it, Git can no longer track changes.
- If you copy the `.git` folder into another directory, you essentially clone the repo without using a remote.

---

### ⚠️ Common Pitfall:
Developers sometimes accidentally delete the `.git` folder when cleaning up, which **removes all Git history** — not just local changes.

> Summary:  
> The `.git` directory is the internal database and control center of your repository. It contains everything Git needs to version, compare, and manage your project effectively.

---

## Question 14
Can you restore a deleted `.git` folder?

## ✅ Answer  
No, you cannot restore a deleted `.git` folder **on your own** unless you have:
- A backup of the folder (manual or automated), or
- A remote copy of the repository (e.g., on GitHub or GitLab)

### 📘 Detailed Explanation  

The `.git/` folder is the **heart of the Git repo** — it contains:
- All your commits
- Branch info
- Tags
- Staging data
- Remote config

If you delete it:
```bash
rm -rf .git
```
Your project becomes a regular directory with no version history.

---

### ✅ Recovery Options:

#### Option 1: You have a remote (like GitHub)

If the repo was pushed earlier:
```bash
# Move existing code out
mkdir backup && mv * backup/

# Clone fresh repo
git clone https://github.com/your/repo.git

# Move your untracked files back in
mv backup/* repo/
```

Then you can `git add`, `commit`, and `push` any uncommitted changes.

---

#### Option 2: You have a backup of the `.git` folder

If you made a backup (e.g., `.git.bak`), you can restore it:

```bash
mv .git.bak .git
git status
```

You’re back in business with full history and branches intact.

---

#### Option 3: Try file recovery tools *(low chance)*

If the `.git` folder was recently deleted and not overwritten, tools like `extundelete` or `recuva` **might** recover parts of it — but success is rare and not reliable.

---

### 🧠 Best Practices to Prevent This:

- Push frequently to a remote.
- Enable automatic backups or snapshot tools.
- Avoid using `rm -rf` without double-checking.
- Use Git GUIs or IDEs to reduce chances of accidental deletion.

> Summary:  
> Once `.git` is gone and you have no remote or backup, your project loses its entire Git history. The safest way to recover is to clone from the remote and reapply any local, uncommitted changes manually.

---

## Question 15

## Question  
A teammate accidentally committed a Kubernetes Secret (base64 encoded) to Git. What should you do?

## ✅ Answer  
Immediately remove the secret from the Git history using tools like `git filter-repo` or `BFG`, rotate the compromised secret, and enforce better secret management policies (e.g., use sealed secrets or external secret stores).

When a Kubernetes Secret (even base64-encoded) is committed to Git, it becomes publicly visible to:
- Everyone with repo access
- Anyone who forked or cloned the repo before removal
- CI/CD systems that fetch the repo

### 🛠️ Step-by-Step Response:

---

#### ✅ Step 1: Rotate the compromised secret  
Whether it’s an API key, database password, or token — assume it’s compromised.

- Create a new secret value (e.g., generate a new DB password or token).
- Update the Kubernetes Secret:
  ```bash
  kubectl create secret generic my-secret --from-literal=password=newpassword --dry-run=client -o yaml | kubectl apply -f -
  ```
- Update any workloads consuming the old secret.

---

#### ✅ Step 2: Remove the secret from Git history  
Base64 encoding is **not encryption**. Anyone can decode it.

If it was recently committed:
```bash
git reset HEAD~1
git restore --staged secret.yaml
rm secret.yaml
git commit -m "Remove secret from repo"
```

But this only removes it from the latest commit. To fully remove it from **entire history**:

Use [`git filter-repo`](https://github.com/newren/git-filter-repo) (preferred over `filter-branch`):
```bash
git filter-repo --path secret.yaml --invert-paths
```

Or use **BFG Repo-Cleaner**:
```bash
bfg --delete-files secret.yaml
```

Then force-push:
```bash
git push --force
```

> Everyone with clones will need to re-clone or follow special instructions to realign their history.

---

#### ✅ Step 3: Prevent it from happening again  
- Add sensitive files to `.gitignore`
  ```
  secrets.yaml
  *.key
  ```
- Use tools like [git-secrets](https://github.com/awslabs/git-secrets) or [pre-commit](https://pre-commit.com/) to scan commits for secrets.
- Educate team members that **Kubernetes Secrets are not encrypted by default in Git**, even though they look scrambled (base64).

---

### 🚫 Why This Matters  
Hardcoded secrets in Git are one of the most common security missteps. Even private repos can be compromised. This situation reflects your ability to respond quickly, contain damage, and improve team practices.

> Summary:  
> Rotate → Remove → Recommit safely → Enforce policies.

---

## Question 16
What are git hooks, how many types of git hooks you have used, and what are their use cases?
## ✅ Answer
Git hooks are scripts that run automatically at certain points in the Git workflow, allowing you to customize and automate tasks. They can be used for various purposes, such as enforcing coding standards, running tests, or sending notifications.
### 📘 Detailed Explanation

Git hooks are scripts that Git executes before or after certain events, like commits, merges, or pushes. They allow you to automate tasks and enforce policies in your Git workflow.
### 🔍 Types of Git Hooks
There are two main types of Git hooks:
1. **Client-side hooks**: Run on your local machine when you perform actions like committing or merging.
2. **Server-side hooks**: Run on the server when actions like receiving pushes or processing pull requests occur.
### ✅ Common Client-side Hooks
1. **`pre-commit`**: Runs before a commit is created. Used to check code quality, run linters, or prevent commits with errors.
   - Example: Running a linter to ensure code style before committing.
   ```bash
   # .git/hooks/pre-commit
   #!/bin/sh
   npm run lint
   ```
2. **`commit-msg`**: Runs after the commit message is entered but before the commit is finalized. Used to validate commit messages against a standard format.
   - Example: Ensuring commit messages follow a specific pattern.
   ```bash
   # .git/hooks/commit-msg
   #!/bin/sh
   if ! grep -qE "^(feat|fix|docs|style|refactor|test|chore): .+" "$1"; then
       echo "Commit message must start with a type (feat, fix, docs, etc.)"
       exit 1
   fi
   ```
3. **`pre-push`**: Runs before pushing changes to a remote repository. Used to run tests or checks to ensure the code is ready for deployment.
   - Example: Running tests before allowing a push.
   ```bash
   # .git/hooks/pre-push
   #!/bin/sh
   npm test
   if [ $? -ne 0 ]; then
       echo "Tests failed. Push aborted."
       exit 1
   fi
   ```
4. **`post-commit`**: Runs after a commit is created. Used for notifications or logging.
   - Example: Sending a notification to a chat channel after a commit.
   ```bash
   # .git/hooks/post-commit
   #!/bin/sh    
    curl -X POST -H 'Content-type: application/json' \
         --data '{"text":"New commit pushed!"}' \
         https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXX
    ```
### ✅ Common Server-side Hooks
1. **`pre-receive`**: Runs before any refs are updated on the server. Used to enforce policies or checks before accepting pushes.
   - Example: Rejecting pushes that
    do not meet certain criteria, like branch naming conventions.
    ```bash
    # .git/hooks/pre-receive
    #!/bin/sh
    while read oldrev newrev refname; do
        if [[ "$refname" != "refs/heads/main" && "$refname" != "refs/heads/develop" ]]; then
            echo "Only 'main' and 'develop' branches are allowed."
            exit 1
        fi
    done
    ```
2. **`post-receive`**: Runs after refs are updated. Used for notifications or triggering CI/CD pipelines.
   - Example: Triggering a CI build after a successful push.
   ```bash
    # .git/hooks/post-receive 
    #!/bin/sh
    curl -X POST http://ci-server/build?repo=your-repo
    ```
3. **`update`**: Runs before updating a ref. Used to enforce rules on individual branches.
    - Example: Preventing force pushes to protected branches.
    ```bash
    # .git/hooks/update
    #!/bin/sh
    while read oldrev newrev refname; do
        if [[ "$refname" == "refs/heads/main" && "$oldrev" != "
$newrev" ]]; then
            echo "Force pushes to 'main' are not allowed."
            exit 1
        fi
    done
    ```
### ✅ Use Cases for Git Hooks
- **Enforcing coding standards**: Use `pre-commit` to run linters or formatters.
- **Validating commit messages**: Use `commit-msg` to ensure consistent commit message formats.
- **Running tests**: Use `pre-push` to ensure code passes tests before pushing.
- **Automating notifications**: Use `post-commit` or `post-receive` to send notifications to chat systems or trigger CI/CD pipelines.
- **Enforcing branch policies**: Use `pre-receive` or `update` to restrict which branches can be pushed to or enforce naming conventions.
### ✅ Summary
Git hooks are powerful tools for automating tasks and enforcing policies in your Git workflow. They can be used to ensure code quality, validate commit messages, run tests, and trigger notifications. By implementing hooks, you can streamline your development process and maintain a consistent codebase across your team.

## Question 17
What is the difference between `git stash` and `git commit`? When would you use each?
## ✅ Answer  
- `git stash` temporarily saves your uncommitted changes, allowing you to switch branches or pull updates without committing. 
- `git commit` permanently saves changes to the repository history.
### 📘 Detailed Explanation 
#### 🔹 `git stash`
- **Purpose**: Temporarily save uncommitted changes in a stack, allowing you to switch branches or pull updates without committing.
- **Usage**:
  ```bash
  git stash save "WIP: work in progress"
  ```
- **When to Use**:  
    - When you need to switch branches but have uncommitted changes that you don't want to commit yet.
    - When you want to pull updates from the remote branch without committing your current work.
- **Example**: If you're working on a feature but need to quickly switch to `main` to fix a bug, you can stash your changes:

```bash
git stash save "WIP: feature work"
git checkout main
git pull origin main
git checkout feature-branch
git stash pop
```
#### 🔹 `git commit`
- **Purpose**: Permanently save changes to the repository history, creating a new commit with a message describing the changes.
- **Usage**:
  ```bash
  git commit -m "Add new feature"
  ```
- **When to Use**:
    - When you have completed a logical unit of work and want to save it to the repository history.
    - When you want to share your changes with others by pushing them to a remote repository.
- **Example**: After completing a feature, you would commit your changes:
```bash
git add .
git commit -m "Implement login feature with validation"
```
### ✅ Summary Table    
| Command       | Purpose                                         | Changes History? | Use Case                                      |
|---------------|-------------------------------------------------|------------------|-----------------------------------------------|
| `git stash`   | Temporarily save uncommitted changes            | No               | Switch branches without committing             |
| `git commit`  | Permanently save changes to the repository      | Yes              | Save completed work to the repository history |

---

## Question 18
What is git cherry-pick? When would you use it?
## ✅ Answer
`git cherry-pick` is a command that allows you to apply the changes introduced by a specific commit from one branch onto another branch. It’s useful for selectively applying commits without merging entire branches.
### 📘 Detailed Explanation
#### 🔹 `git cherry-pick`
- **Purpose**: Apply the changes from a specific commit onto the current branch, creating a new commit with those changes.
- **Usage**:
    ```bash
    git cherry-pick <commit-hash>
    ```
- **When to Use**:
    - When you want to apply a specific commit from another branch without merging the entire branch.
    - When you need to backport a bug fix or feature from one branch to another.
- **Example**: If you have a commit `abc123` on the `feature-branch` that fixes a critical bug, and you want to apply that fix to the `main` branch, you would do the following:
```bash
git checkout main
git cherry-pick abc123
```
This will create a new commit on the `main` branch with the changes from `abc123`.
### ✅ Benefits of Using `git cherry-pick`
- **Selective Changes**: Allows you to apply only the changes you need without merging unrelated commits
- **Backporting**: Useful for applying bug fixes or features to older branches without merging all changes
- **Granular Control**: Gives you control over which specific changes to apply, rather than merging entire branches.
---