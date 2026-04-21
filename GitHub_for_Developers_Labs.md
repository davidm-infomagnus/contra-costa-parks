# 🚀 GitHub for Developers – Intermediate: Hands-On Labs

> **Duration:** 3 hours (theory + practice)  
> **Format:** Instructor-led with hands-on activities  
> **Prerequisites:** Git installed, terminal/CLI access. GitHub account recommended but not required.

---

## 🛤️ Choose Your Path

This training supports **two paths** so everyone can follow along regardless of setup:

| | 📗 Path A — GitHub (Recommended) | 📙 Path B — Local Only |
|---|---|---|
| **Setup** | Create repo on GitHub.com, clone locally | Initialize repo locally with `git init` |
| **Requires** | GitHub account + authentication | Only Git installed |
| **Covers** | All labs including PRs, branch protection | Sections 1–4 fully; Section 5 as instructor demo |
| **Best for** | Full experience | Troubleshooting auth/network issues |

> **💡 Recommendation:** Try Path A first. If you run into authentication or network issues, switch to Path B — you can always connect to GitHub later with `git remote add`.

Look for the **📗 Path A** and **📙 Path B** markers throughout this guide.

---

## 🗺️ Training Roadmap

| Section | Topic | Time |
|---------|-------|------|
| 1 | Git Fundamentals & Lab 1 | 60 min |
| 2 | Git Tags & Lab 2 | 15 min |
| 3 | Git Debugging & Lab 3 | 25 min |
| 4 | Git History | 30 min |
| 5 | Branching Strategies & GitHub Flow | 40 min |
| — | Wrap-up & Q&A | 10 min |

---

## 🧰 Initial Setup

Before we begin, let's make sure Git is installed and configured.

### Installing Git

**macOS:**
```bash
# Option 1: Xcode Command Line Tools (most common)
xcode-select --install

# Option 2: Homebrew
brew install git
```

**Windows:**
1. Download from [git-scm.com/download/win](https://git-scm.com/download/win)
2. Run the installer — **use the recommended defaults**
3. Open **Git Bash** (installed with Git) or **PowerShell**

> **⚠️ Windows Troubleshooting:**
> - If `git --version` is not recognized in PowerShell, close and reopen your terminal — the PATH update requires a restart.
> - If it still doesn't work, add Git manually to PATH:
>   1. Search "Environment Variables" in the Start menu
>   2. Under System Variables, edit `Path`
>   3. Add `C:\Program Files\Git\cmd`
> - **Recommended terminal:** Use **Git Bash** (comes with the Git installer) — it provides a Unix-like shell that matches all commands in this guide.
> - If using **PowerShell**, note that `cat > file << 'EOF'` (heredoc) syntax won't work. Use your text editor to create files instead, or use Git Bash.

**Linux (Debian/Ubuntu):**
```bash
sudo apt update && sudo apt install git
```

### Git Configuration

```bash
# Verify Git is installed
git --version

# Set your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Enable colored output
git config --global color.ui auto

# Verify your configuration
git config --list --show-origin
```

### 📗 GitHub Authentication (Path A only)

> **Skip this section if you're following Path B (local only).**

You need to be authenticated to push and pull from GitHub. Choose one method:

**Option 1 — GitHub CLI (recommended):**
```bash
# Install GitHub CLI
# macOS:   brew install gh
# Windows: winget install --id GitHub.cli
# Linux:   https://github.com/cli/cli/blob/trunk/docs/install_linux.md

# Authenticate
gh auth login
# Follow the prompts: GitHub.com → HTTPS → Login with a web browser
```

> **⚠️ Windows GH CLI Troubleshooting:**
> - If `winget` is not available, download the installer from [cli.github.com](https://cli.github.com)
> - If `gh auth login` hangs or fails to open a browser, try: `gh auth login --web`
> - If you're behind a corporate proxy/firewall, Option 2 (PAT) may be easier

**Option 2 — HTTPS with Personal Access Token (PAT):**
1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **"Generate new token (classic)"**
3. Select the `repo` scope, then generate
4. **Copy the token** — you won't see it again!
5. When Git prompts for a password, paste the token instead

> **💡 Windows tip:** Enable the Git Credential Manager to store your token so you don't have to paste it every time:
> ```bash
> git config --global credential.helper manager
> ```

**Option 3 — SSH Key:**
```bash
# Check for existing SSH keys
ls -la ~/.ssh

# Generate a new key (if needed)
ssh-keygen -t ed25519 -C "your.email@example.com"

# Copy the public key
# macOS:   cat ~/.ssh/id_ed25519.pub | pbcopy
# Windows: cat ~/.ssh/id_ed25519.pub | clip
# Linux:   cat ~/.ssh/id_ed25519.pub

# Add the key at: github.com/settings/ssh/new

# Test the connection
ssh -T git@github.com
```

**Verify authentication works:**
```bash
gh auth status          # If using GitHub CLI
# OR
ssh -T git@github.com   # If using SSH
```

> **Still stuck?** Don't worry — switch to **📙 Path B (Local Only)** and follow along. You'll learn the same Git concepts. You can connect to GitHub later.

---

# Section 1: Git Fundamentals (60 min)

## 📖 Concept: Atomic Changes

Every commit should represent **one logical change**. Think of commits like sentences in a story — each one should make sense on its own.

**The Golden Rule:** If you can't describe your commit in a single sentence without using "and", it's probably not atomic.

---

## 🔬 Lab 1: "The Contra Costa County Parks Website"

**Scenario:** You've just joined the Contra Costa County Parks Department as a developer. Your team maintains a small website that lists parks and trails. You'll add a new feature: a page for the **Iron Horse Regional Trail**.

### Step 1 — Create Your Repository

#### 📗 Path A — Create on GitHub.com, then clone

1. Go to [github.com/new](https://github.com/new)
2. Fill in the repository details:
   - **Repository name:** `contra-costa-parks`
   - **Description:** `Contra Costa County Parks website — GitHub for Developers training`
   - **Visibility:** Public (or Private if preferred)
   - ✅ **Add a README file** — check this box
3. Click **"Create repository"**

> **💡 Tip:** Adding a README during creation gives you an initial commit, which makes it easier to start working right away.

### Step 2 — Clone the Repository Locally

```bash
# Clone YOUR repository (replace <your-username> with your GitHub username)
git clone https://github.com/<your-username>/contra-costa-parks.git
cd contra-costa-parks

# Inspect the repository — you should see the initial README commit
git status
git log --oneline --graph --all
git remote -v
```

> **💡 HTTPS vs SSH:** If you have SSH keys configured, you can use:
> ```bash
> git clone git@github.com:<your-username>/contra-costa-parks.git
> ```

#### 📙 Path B — Initialize locally

```bash
# Create and enter the project folder
mkdir contra-costa-parks
cd contra-costa-parks

# Initialize a new Git repository
git init

# Create an initial README
echo "# Contra Costa County Parks" > README.md
git add README.md
git commit -m "Initial commit: add README"

# Verify
git status
git log --oneline
```

> **💡 Connect later:** You can link this local repo to GitHub at any time:
> ```bash
> git remote add origin https://github.com/<your-username>/contra-costa-parks.git
> git push -u origin main
> ```

### Step 3 — Create the Project Base

```bash
# Create the project structure
mkdir -p src/pages src/styles

# Create the homepage
cat > src/pages/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Contra Costa County Parks</title>
    <link rel="stylesheet" href="../styles/main.css">
</head>
<body>
    <header>
        <h1>🌲 Contra Costa County Parks</h1>
        <nav>
            <a href="index.html">Home</a>
            <a href="trails.html">Trails</a>
        </nav>
    </header>
    <main>
        <h2>Welcome to Our Parks!</h2>
        <p>Explore the beautiful parks and trails of Contra Costa County.</p>
        <ul>
            <li>Mount Diablo State Park</li>
            <li>Briones Regional Park</li>
            <li>Tilden Regional Park</li>
        </ul>
    </main>
    <footer>
        <p>&copy; 2026 Contra Costa County Parks Dept.</p>
    </footer>
</body>
</html>
EOF

# Create the stylesheet
cat > src/styles/main.css << 'EOF'
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f0f4e8;
    color: #2d3436;
}
header {
    background-color: #2d6a4f;
    color: white;
    padding: 1rem 2rem;
}
nav a {
    color: #b7e4c7;
    margin-right: 1rem;
    text-decoration: none;
}
main {
    padding: 2rem;
    max-width: 800px;
    margin: auto;
}
footer {
    text-align: center;
    padding: 1rem;
    background-color: #2d6a4f;
    color: white;
}
EOF

# Stage and commit the initial project
git add .
git commit -m "Initial commit: project structure and homepage"
```

### Step 4 — Push the Initial Project to GitHub

#### 📗 Path A:
```bash
# Push the project base to your remote repository
git push origin main
```

> Refresh your repo page on GitHub.com — you should see `src/pages/index.html` and `src/styles/main.css` in the file browser.

#### 📙 Path B:
> No push needed — your commits are saved locally. Verify with:
> ```bash
> git log --oneline
> ```

### Step 5 — Create a Feature Branch

```bash
# Create and switch to a new feature branch
git checkout -b feature/iron-horse-trail

# Verify you are on the new branch
git branch
```

### Step 6 — Add the New Feature (Trails Page)

```bash
cat > src/pages/trails.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Trails - Contra Costa County Parks</title>
    <link rel="stylesheet" href="../styles/main.css">
</head>
<body>
    <header>
        <h1>🌲 Contra Costa County Parks</h1>
        <nav>
            <a href="index.html">Home</a>
            <a href="trails.html">Trails</a>
        </nav>
    </header>
    <main>
        <h2>🥾 Iron Horse Regional Trail</h2>
        <p><strong>Distance:</strong> 32 miles</p>
        <p><strong>Difficulty:</strong> Easy to Moderate</p>
        <p><strong>Description:</strong> The Iron Horse Regional Trail follows the route
        of the former Southern Pacific Railroad line through the San Ramon Valley.
        Perfect for hiking, biking, and horseback riding.</p>

        <h3>Trail Highlights</h3>
        <ul>
            <li>Walnut Creek to Pleasanton corridor</li>
            <li>Connects to BART stations</li>
            <li>Paved multi-use path</li>
            <li>Open sunrise to sunset</li>
        </ul>
    </main>
    <footer>
        <p>&copy; 2026 Contra Costa County Parks Dept.</p>
    </footer>
</body>
</html>
EOF
```

### Step 7 — Stage, Verify, and Commit

```bash
# Check what changed
git status

# See the diff (working tree vs staging)
git diff

# Stage the new file
git add src/pages/trails.html

# Verify staged changes
git status
git diff --staged

# Commit with an atomic, descriptive message
git commit -m "feat: add Iron Horse Regional Trail page"
```

### Step 8 — Add Trail Styling (Second Atomic Commit)

```bash
# Append trail-specific styles
cat >> src/styles/main.css << 'EOF'

/* Trail page styles */
.trail-card {
    border: 2px solid #2d6a4f;
    border-radius: 8px;
    padding: 1.5rem;
    margin: 1rem 0;
    background-color: #d8f3dc;
}
.trail-card h3 {
    color: #1b4332;
}
EOF

git add src/styles/main.css
git commit -m "style: add trail card styles for trail pages"
```

### Step 9 — Push the Feature Branch to GitHub

#### 📗 Path A:
```bash
# Push the feature branch to your remote repository
git push origin feature/iron-horse-trail
```

> Check GitHub.com — you'll see a banner saying **"feature/iron-horse-trail had recent pushes"**. We'll use Pull Requests for merging later in Section 5, but for now let's practice a local merge.

#### 📙 Path B:
> Skip this step — proceed directly to the merge.

### Step 10 — Switch Branches and Merge Locally

```bash
# Switch back to main
git checkout main

# Verify the trails page does NOT exist on main
ls src/pages/

# Merge the feature branch into main
git merge feature/iron-horse-trail

# Verify the merge
git log --oneline --graph --all

# Verify the file now exists on main
ls src/pages/
```

### Step 11 — Push the Merged Changes to GitHub

#### 📗 Path A:
```bash
# Push the updated main branch
git push origin main

# Verify on GitHub.com — refresh your repo and check that trails.html is there
```

#### 📙 Path B:
> No push needed. Verify the merge locally:
> ```bash
> git log --oneline --graph --all
> ```

---

### 📝 Section 1 Recap

| Concept | What You Learned |
|---------|-----------------|
| Atomic Commits | One logical change per commit |
| Branching | Isolate work without affecting `main` |
| Staging Area | Control exactly what goes into each commit |
| Merge | Bring feature work back into the main line |

### 📋 Command Reference — Git Fundamentals

```
git config --global <key> <value>   Set global configuration
git config --list                   View all configuration
git init                            Initialize a new repository
git clone <url>                     Clone a remote repository
git status                          Show working tree status
git add <file>                      Stage changes
git commit -m "message"             Commit staged changes
git log --oneline --graph --all     Visual commit history
git branch <name>                   Create a new branch
git checkout <branch>               Switch branches
git checkout -b <branch>            Create and switch in one step
git merge <branch>                  Merge a branch into current
git diff                            Show unstaged changes
git diff --staged                   Show staged changes
git push origin <branch>            Push branch to remote
git fetch                           Download remote changes (refs only)
git pull                            Fetch + merge remote changes
```

---

# Section 2: Git Tags (15 min)

## 🔬 Lab 2: "Tagging the Park Website Releases"

**Scenario:** The Parks Department wants to track website releases. Let's tag our commits to mark version milestones.

### Step 1 — Locate Commits to Tag

```bash
# View commit history
git log --oneline
```

You should see something like:

```
abc1234 style: add trail card styles for trail pages
def5678 feat: add Iron Horse Regional Trail page
ghi9012 Initial commit: project structure and homepage
```

### Step 2 — Create a Lightweight Tag

```bash
# Tag the initial commit as v0.1.0 (lightweight — just a pointer)
git tag v0.1.0 <initial-commit-sha>

# Verify
git tag
```

### Step 3 — Create an Annotated Tag

```bash
# Tag the current HEAD as v1.0.0 (annotated — includes metadata)
git tag -a v1.0.0 -m "Release 1.0.0: Iron Horse Trail feature"

# Inspect the annotated tag
git show v1.0.0
```

### Step 4 — Compare Tag Types

```bash
# Lightweight tag — just shows the commit
git show v0.1.0

# Annotated tag — shows tagger, date, message + commit
git show v1.0.0
```

> **💡 Best Practice:** Use **annotated tags** for releases (they contain author, date, and message). Use **lightweight tags** for temporary or personal markers.

### Step 5 — Push Tags to Remote

#### 📗 Path A:
```bash
# Push a specific tag
git push origin v1.0.0

# Push ALL tags
git push origin --tags

# List remote tags
git ls-remote --tags origin
```

#### 📙 Path B:
> Tags are stored locally and will work for all remaining labs. You can push them later when you connect to GitHub.
> ```bash
> # Verify your local tags
> git tag
> git log --oneline --decorate
> ```

---

### 📝 Section 2 Recap

| Concept | What You Learned |
|---------|-----------------|
| Lightweight Tags | Simple pointers to a commit (no metadata) |
| Annotated Tags | Full objects with tagger, date, and message |
| Pushing Tags | Tags are NOT pushed by default — use `--tags` |

### 📋 Command Reference — Git Tags

```
git tag                              List all tags
git tag <name>                       Create a lightweight tag at HEAD
git tag <name> <sha>                 Create a lightweight tag at a specific commit
git tag -a <name> -m "msg"           Create an annotated tag
git show <tag>                       Show tag details
git push origin <tag>                Push a specific tag
git push origin --tags               Push all tags
git tag -d <name>                    Delete a local tag
git push origin --delete <tag>       Delete a remote tag
```

---

# Section 3: Git Debugging (25 min)

## 📖 Concept: Finding Bugs in History

When a bug appears and you don't know which commit introduced it, Git gives you powerful tools to **hunt it down** without reading every commit manually.

---

## 🔬 Lab 3: "The Broken Trail Map"

**Scenario:** Someone on the team pushed several commits to the parks website, and now the trail distances are showing **negative values** — clearly a bug! You need to find exactly which commit broke it.

### Step 1 — Set Up the Bug Scenario

```bash
# Make sure you're on main
git checkout main

# Create the data directory and add trail data
mkdir -p src/data

# We'll simulate a series of commits, one of which introduces a bug
cat > src/data/trails.json << 'EOF'
{
  "trails": [
    { "name": "Iron Horse Trail", "distance_miles": 32 },
    { "name": "Briones Loop", "distance_miles": 6.2 }
  ]
}
EOF
git add src/data/trails.json
git commit -m "data: add trail distance data"

# Commit 2: Add a new trail (still good)
cat > src/data/trails.json << 'EOF'
{
  "trails": [
    { "name": "Iron Horse Trail", "distance_miles": 32 },
    { "name": "Briones Loop", "distance_miles": 6.2 },
    { "name": "Lafayette-Moraga Trail", "distance_miles": 7.7 }
  ]
}
EOF
git add src/data/trails.json
git commit -m "data: add Lafayette-Moraga Trail"

# Commit 3: THE BUG — negative distance introduced!
cat > src/data/trails.json << 'EOF'
{
  "trails": [
    { "name": "Iron Horse Trail", "distance_miles": -32 },
    { "name": "Briones Loop", "distance_miles": 6.2 },
    { "name": "Lafayette-Moraga Trail", "distance_miles": 7.7 }
  ]
}
EOF
git add src/data/trails.json
git commit -m "data: update trail info formatting"

# Commit 4: Add another trail (bug persists)
cat > src/data/trails.json << 'EOF'
{
  "trails": [
    { "name": "Iron Horse Trail", "distance_miles": -32 },
    { "name": "Briones Loop", "distance_miles": 6.2 },
    { "name": "Lafayette-Moraga Trail", "distance_miles": 7.7 },
    { "name": "Contra Costa Canal Trail", "distance_miles": 12.5 }
  ]
}
EOF
git add src/data/trails.json
git commit -m "data: add Contra Costa Canal Trail"

# Commit 5: Update footer (bug still there)
cat >> src/styles/main.css << 'EOF'

footer a { color: #b7e4c7; }
EOF
git add src/styles/main.css
git commit -m "style: update footer link colors"

# 📗 Path A only — push all the new commits to GitHub:
git push origin main
# 📙 Path B — skip this push, continue to Step 2
```

### Step 2 — Use `git diff` to View Changes

```bash
# Compare the current file to two commits ago
git diff HEAD~3 -- src/data/trails.json

# See what changed in a specific commit
git show HEAD~2 -- src/data/trails.json
```

### Step 3 — Use `git blame` to Investigate

```bash
# See who last modified each line of the file
git blame src/data/trails.json
```

You'll see a line-by-line view showing **which commit** last changed each line. Look for the line with `-32` — that's your suspect!

### Step 4 — Use `git bisect` (Manual Mode)

```bash
# Start bisect
git bisect start

# Mark current state as bad
git bisect bad

# Mark the initial data commit as good (the tag or first data commit)
# Find it first:
git log --oneline -- src/data/trails.json

# Mark a known good commit
git bisect good <sha-of-"data: add trail distance data">
```

Git will now **checkout a midpoint commit**. Test it:

```bash
# Check if the bug exists at this point
cat src/data/trails.json
# Look for negative values

# If distances are positive → it's good
git bisect good

# If a distance is negative → it's bad
git bisect bad
```

Repeat until Git identifies the **first bad commit**:

```
<sha> is the first bad commit
```

```bash
# End bisect and return to your branch
git bisect reset
```

### Step 5 — Use `git bisect` (Automated Mode) 🤖

Create a test script that Git can run automatically:

```bash
cat > /tmp/test-trails.sh << 'EOF'
#!/bin/bash
# Exit 0 (good) if no negative distances, exit 1 (bad) if found
if [ -f src/data/trails.json ]; then
    if grep -q '"-' src/data/trails.json; then
        exit 1  # BAD — negative value found
    fi
fi
exit 0  # GOOD
EOF
chmod +x /tmp/test-trails.sh

# Run automated bisect
git bisect start
git bisect bad HEAD
git bisect good <sha-of-first-data-commit>
git bisect run /tmp/test-trails.sh
```

Git will automatically test each midpoint and report the first bad commit!

```bash
git bisect reset
```

---

### 📝 Section 3 Recap

| Concept | What You Learned |
|---------|-----------------|
| `git diff` | Compare changes between commits, branches, or working tree |
| `git blame` | See who last modified each line and in which commit |
| `git bisect` (manual) | Binary search through history by marking good/bad |
| `git bisect` (auto) | Automate bisect with a test script |

### 📋 Command Reference — Git Debugging

```
git diff                             Unstaged changes
git diff --staged                    Staged changes
git diff <commit1> <commit2>         Compare two commits
git diff <commit> -- <file>          Compare file at commit vs HEAD
git blame <file>                     Line-by-line last-change annotation
git blame -L 10,20 <file>            Blame specific line range
git bisect start                     Begin binary search
git bisect bad [<commit>]            Mark a commit as bad
git bisect good [<commit>]           Mark a commit as good
git bisect run <script>              Automate bisect with a test
git bisect reset                     End bisect, return to branch
git show <commit>                    Show commit details and diff
```

---

# Section 4: Git History (30 min)

## 🔬 Lab 4: "Rewriting the Park's History"

**Scenario:** The Parks Department's Git history has some problems — a typo in a commit message, a commit that should be undone, and some messy commits that need cleaning up before merging.

### Part A — Reverting a Commit

The bug commit from Lab 3 needs to be **safely undone** without rewriting history.

```bash
# Find the buggy commit
git log --oneline -- src/data/trails.json

# Revert the commit that introduced negative distances
git revert <sha-of-buggy-commit>
# An editor opens — accept the default revert message or customize it
```

```bash
# Inspect what happened under the hood
git log --oneline -5

# See the generated patch
git show HEAD
```

> **💡 Key Insight:** `git revert` creates a **new commit** that undoes the changes. It doesn't delete history — safe for shared branches!

### Part B — Amending a Commit

```bash
# Let's add a README
echo "# Contra Costa County Parks Website" > README.md
git add README.md
git commit -m "docs: add RADME"

# Oops! Typo in the commit message. Fix it:
git commit --amend -m "docs: add README"

# Verify the fix
git log --oneline -3
```

Now let's amend content too:

```bash
# We forgot to add more info to the README
cat >> README.md << 'EOF'

> Maintained by the Parks Department DevOps team.
EOF

git add README.md
git commit --amend --no-edit
# --no-edit keeps the same commit message

git show HEAD
```

> ⚠️ **Warning:** Only amend commits that haven't been pushed! Amending rewrites history.

### Part C — Interactive Rebase (Squash & Reword)

```bash
# Create a feature branch with several messy commits
git checkout -b feature/park-events

echo "<h2>Upcoming Events</h2>" > src/pages/events.html
git add src/pages/events.html
git commit -m "wip"

echo "<p>Summer Solstice Hike - June 21</p>" >> src/pages/events.html
git add src/pages/events.html
git commit -m "stuff"

echo "<p>Star Gazing Night - July 15</p>" >> src/pages/events.html
git add src/pages/events.html
git commit -m "more stuff"

echo "<p>Fall Colors Walk - October 10</p>" >> src/pages/events.html
git add src/pages/events.html
git commit -m "fix"

# View the messy history
git log --oneline -4
```

Now clean it up with interactive rebase:

```bash
# Rebase the last 4 commits
git rebase -i HEAD~4
```

Your editor will show:

```
pick abc1234 wip
pick def5678 stuff
pick ghi9012 more stuff
pick jkl3456 fix
```

Change it to:

```
reword abc1234 wip
squash def5678 stuff
squash ghi9012 more stuff
squash jkl3456 fix
```

- **reword** — change the first commit's message to: `feat: add park events page`
- **squash** — fold the remaining commits into the first one

Save and close. Edit the combined commit message when prompted.

```bash
# Verify: 4 messy commits → 1 clean commit
git log --oneline -3
```

### Part D — Git Reflog (The Safety Net)

```bash
# "Accidentally" reset and lose the events commit
git reset --hard HEAD~1

# Oh no! The events page is gone!
ls src/pages/events.html  # File not found!

# But Git remembers EVERYTHING — check the reflog
git reflog

# Find the SHA of the lost commit and recover it
git reset --hard <sha-from-reflog>

# The events page is back!
ls src/pages/events.html
cat src/pages/events.html
```

### Part E — Git Cherry-Pick

```bash
# Switch back to main
git checkout main

# Suppose we only want the events page from the feature branch,
# not everything else. Cherry-pick just that commit:
git log --oneline feature/park-events

git cherry-pick <sha-of-events-commit>

# Verify it landed on main
git log --oneline -3
cat src/pages/events.html
```

---

### 📝 Section 4 Recap

| Concept | What You Learned |
|---------|-----------------|
| `git revert` | Safely undo a commit by creating a new inverse commit |
| `git commit --amend` | Fix the last commit (message or content) |
| Interactive Rebase | Squash, reword, reorder, or drop commits |
| `git reflog` | Recover "lost" commits — Git's safety net |
| `git cherry-pick` | Copy a specific commit to another branch |

### 📋 Command Reference — Git History

```
git revert <sha>                     Create a commit that undoes <sha>
git commit --amend                   Modify the last commit
git commit --amend -m "new msg"      Amend just the message
git commit --amend --no-edit         Amend content, keep message
git rebase -i HEAD~N                 Interactive rebase last N commits
  pick   → keep commit as-is
  reword → change commit message
  edit   → pause to modify commit
  squash → merge into previous commit (edit message)
  fixup  → merge into previous, discard message
  drop   → remove commit entirely
git reflog                           Show history of HEAD movements
git reset --hard <sha>               Move HEAD to a specific commit
git cherry-pick <sha>                Copy a commit onto current branch
git show <sha>                       Show commit content and patch
```

---

# Section 5: Branching Strategies & GitHub Flow (40 min)

> **📙 Path B note:** This section covers GitHub-specific features (branch protection, PRs, code owners). If you're working locally, **follow along with the instructor's screen** for the GitHub UI portions. The Git commands (branching, merging, CODEOWNERS file) still work locally.

## 🔬 Lab 5: "The Parks Team Workflow"

**Scenario:** The Parks Department is growing! You now have a team of 3 developers. Let's set up proper branch protection, code ownership, and practice the GitHub Flow.

### Part A — Set Up Branch Protection (📗 Path A — GitHub UI)

> **📙 Path B:** Follow along on the instructor's screen for this part.

> **Navigate to:** Your repo → Settings → Branches → Add branch protection rule

1. **Branch name pattern:** `main`
2. Enable these rules:
   - ✅ Require a pull request before merging
   - ✅ Require approvals (set to 1)
   - ✅ Require status checks to pass before merging
   - ✅ Do not allow bypassing the above settings

> **💡 Rulesets vs Branch Protection:** GitHub now offers **Repository Rulesets** (Settings → Rules → Rulesets) as a more flexible alternative. Rulesets can target multiple branches, apply to tags, and be managed at the org level.

### Part B — Set Up Code Owners (Both Paths)

Everyone can create the CODEOWNERS file — it's just a regular file in your repo:

```bash
# Create a CODEOWNERS file in the repo root
cat > CODEOWNERS << 'EOF'
# Default owners for everything
*                       @parks-devops-team

# Frontend pages — owned by the web team
src/pages/              @web-team

# Styles — owned by the design lead
src/styles/             @design-lead

# Data files — owned by the data team
src/data/               @data-team

# Infrastructure and CI/CD
.github/                @parks-devops-team
EOF

git add CODEOWNERS
git commit -m "chore: add CODEOWNERS file"

# 📗 Path A only — push to GitHub:
git push origin main
# 📙 Path B — skip the push
```

### Part C — Practice GitHub Flow (📗 Path A + Instructor Demo for 📙 Path B)

#### 1. Create a Feature Branch

```bash
git checkout -b feature/visitor-counter
```

#### 2. Make Changes and Commit

```bash
cat > src/pages/counter.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Visitor Counter - Contra Costa Parks</title>
    <link rel="stylesheet" href="../styles/main.css">
</head>
<body>
    <header>
        <h1>🌲 Contra Costa County Parks</h1>
    </header>
    <main>
        <h2>📊 Park Visitor Counter</h2>
        <div class="trail-card">
            <h3>Today's Visitors</h3>
            <p id="count">Loading...</p>
        </div>
    </main>
    <footer>
        <p>&copy; 2026 Contra Costa County Parks Dept.</p>
    </footer>
</body>
</html>
EOF

git add src/pages/counter.html
git commit -m "feat: add visitor counter page"
```

#### 3. Push and Create a Pull Request

**📗 Path A:**
```bash
# Push the feature branch to GitHub
git push origin feature/visitor-counter
```

**On GitHub:**
1. Click **"Compare & pull request"**
2. Fill in the PR details:
   - **Title:** `feat: Add visitor counter page`
   - **Description** (using Markdown):
     ```markdown
     ## Summary
     Adds a new visitor counter page for tracking park attendance.

     ## Changes
     - [ ] New `counter.html` page
     - [ ] Connected to park stats API

     ## Testing
     - Verified locally with `open src/pages/counter.html`

     Closes #42
     ```
   - **Reviewers:** Add a teammate
   - **Labels:** `enhancement`, `frontend`
   - **Assignees:** Yourself

#### 4. Create a Draft PR (Instructor Demo)

Show how to:
- Click the dropdown arrow on "Create pull request" → **Create draft pull request**
- Use task lists in the description for tracking progress
- Convert from draft to ready for review

#### 5. Review and Suggest Changes

As a reviewer:
1. Go to **Files changed** tab
2. Click the **+** next to a line number
3. Click the suggestion icon (±) and write:
   ```suggestion
   <p id="count">0 visitors today</p>
   ```
4. Click **Add single comment** or **Start a review**
5. The author can then **Commit suggestion** directly from the PR

#### 6. Merge the PR

Discuss the merge options available in the dropdown:
- **Create a merge commit** (recursive merge — preserves full history)
- **Squash and merge** (combines all PR commits into one)
- **Rebase and merge** (replays commits on top of base — linear history)

Choose **Squash and merge** for a clean history.

**📗 Path A:**
```bash
# After merging, clean up locally
git checkout main
git pull origin main
git branch -d feature/visitor-counter
```

**📙 Path B:**
```bash
# Merge locally instead of via PR
git checkout main
git merge --squash feature/visitor-counter
git commit -m "feat: add visitor counter page"
git branch -d feature/visitor-counter

# Verify
git log --oneline -3
```

### Part D — Understanding Merge Strategies

| Strategy | Command | When to Use |
|----------|---------|-------------|
| **Fast-forward** | `git merge --ff-only` | Linear history, no divergence |
| **Recursive** | `git merge` (default) | Standard merge with merge commit |
| **Octopus** | `git merge branch1 branch2 branch3` | Merging 3+ branches (rare) |
| **Rebase + Merge** | `git rebase main && git merge --ff-only` | Clean linear history |

```bash
# Demo: fast-forward vs merge commit
git checkout -b demo/ff-test
echo "test" > ff-test.txt
git add ff-test.txt && git commit -m "test ff"

git checkout main

# Fast-forward (no merge commit)
git merge --ff-only demo/ff-test
git log --oneline --graph -5

# Clean up
git branch -d demo/ff-test
```

---

### 📝 Section 5 Recap

| Concept | What You Learned |
|---------|-----------------|
| Branch Protection | Enforce quality gates (PRs, approvals, status checks) |
| Rulesets | Org-level, multi-branch alternative to branch protection |
| CODEOWNERS | Automatically assign reviewers based on file paths |
| Pull Requests | Structured code review with suggestions and discussions |
| Draft PRs | Signal work-in-progress, use task lists |
| Merge Strategies | Choose the right merge type for your workflow |
| GitHub Flow | Branch → Commit → PR → Review → Merge |

### 📋 Command Reference — Branching & PRs

```
git push origin <branch>             Push a branch to remote
git push origin --delete <branch>    Delete a remote branch
git branch -d <branch>               Delete a local branch (safe)
git branch -D <branch>               Force delete a local branch
git merge --ff-only <branch>         Merge only if fast-forward possible
git merge --no-ff <branch>           Force a merge commit
git merge --squash <branch>          Squash all commits then merge
git rebase <branch>                  Rebase current onto <branch>
```

---

# 🎯 Wrap-Up & Quick Reference Card

## The GitHub Flow in 6 Steps

```
1. 🌿 Create a feature branch         git checkout -b feature/my-work
2. 💾 Commit changes (atomically!)     git add . && git commit -m "feat: ..."
3. 📤 Push to GitHub                   git push origin feature/my-work      (📗 Path A)
4. 🔍 Open a Pull Request              (GitHub UI)                          (📗 Path A)
5. 💬 Discuss & Review                 (GitHub UI — suggestions, approvals) (📗 Path A)
6. 🚀 Merge & Deploy                   git merge / GitHub UI merge
```

> **📙 Path B alternative for steps 3–5:** Merge locally with `git merge` or `git merge --squash`.

## 🖥️ Windows Tips — Creating Files Without Heredocs

The `cat > file << 'EOF'` syntax used throughout this guide works in **Git Bash** and **macOS/Linux terminals**. If you're using **PowerShell** or **CMD**, create files using one of these alternatives:

```powershell
# PowerShell — use Set-Content or your text editor
Set-Content -Path "src\pages\index.html" -Value @"
<html>
  <body>Hello World</body>
</html>
"@

# Or simply open the file in VS Code / Notepad and paste the content:
code src/pages/index.html
notepad src\pages\index.html
```

> **💡 Recommendation:** Use **Git Bash** (installed with Git for Windows) for the best experience with this training.

## Essential Git Aliases (Bonus!)

Add these to your `~/.gitconfig` for faster workflows:

```bash
git config --global alias.st "status -sb"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.ci "commit"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.unstage "restore --staged"
```

Now you can use:
```bash
git st          # short status
git lg          # pretty log graph
git last        # last commit details
git unstage .   # unstage all files
```

---

## 📚 Further Reading

| Resource | Link |
|----------|------|
| Git Documentation | https://git-scm.com/doc |
| GitHub Flow Guide | https://docs.github.com/en/get-started/using-github/github-flow |
| Branch Protection Rules | https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository |
| CODEOWNERS Syntax | https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners |
| Git Bisect Deep Dive | https://git-scm.com/docs/git-bisect |
| Interactive Rebase | https://git-scm.com/docs/git-rebase |

---

> **Training by:** David Medina - GitHub for Developers Training  
> **Organization:** Contra Costa County — Developers Team  
> **Date:** 2026  
> **Version:** 2.0.0
