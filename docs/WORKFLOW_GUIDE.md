# Step-by-Step Workflow Guide

**This guide shows you exactly how to:**
1. Clone (download) this repository to your computer
2. Do your review work
3. Add your finished files
4. Create a pull request (not merge directly!)

**Follow this every time you work on a review.**

---

## Prerequisites

Before you start, make sure you've completed:
- ✅ [LOCAL_SETUP_GUIDE.md](LOCAL_SETUP_GUIDE.md) - First-time setup
- ✅ You've been added as a collaborator to the repository
- ✅ You have a Personal Access Token saved

---

## Part 1: Clone the Repository (First Time Only)

You only need to do this **once** when you first start working on reviews.

### Step 1: Open PowerShell

1. Press the **Windows key**
2. Type `PowerShell`
3. Press **Enter**

### Step 2: Choose Where to Save the Repository

Pick a location on your computer. We recommend:
```powershell
cd C:\Users\YourName\Documents
```
Replace `YourName` with your actual Windows username.

**⚠️ Important:** Don't use a folder that syncs with OneDrive, Dropbox, or Google Drive. Git handles syncing for you.

### Step 3: Clone the Repository

Copy and paste this command:

```powershell
git clone https://github.com/Lizo-RoadTown/file_queuing_system.git
```

Press **Enter**.

**You'll be asked for:**
- **Username:** Type your GitHub username and press Enter
- **Password:** **Paste your Personal Access Token** (NOT your GitHub password!) and press Enter

> **Note:** When you paste the token, you won't see any characters appear. This is normal! Just paste and press Enter.

**You'll see:**
```
Cloning into 'file_queuing_system'...
remote: Enumerating objects: ...
remote: Counting objects: 100% ...
Receiving objects: 100% ...
done
```

✅ **Success!** The repository is now on your computer.

### Step 4: Navigate Into the Repository

```powershell
cd file_queuing_system
```

### Step 5: Set Up Your Python Environment (First Time Only)

Follow the instructions in [LOCAL_SETUP_GUIDE.md - Step 4](LOCAL_SETUP_GUIDE.md#step-4-set-up-your-python-environment) to create your conda environment.

---

## Part 2: Daily Workflow - Starting Your Work

**Do this every time you sit down to work on a review.**

### VS Code Sync Check

In VS Code, you can confirm your Git state in two places:

1. Look at the **bottom-left status bar**
   - It should show your branch name, usually `main`
   - If you see a number on the sync icon, GitHub has changes to pull or local commits to push
2. Open the **Source Control** panel
   - If it is empty, you have no uncommitted local changes
   - If files appear there, you still have local work that is not committed yet

If you want one exact command that tells you whether you are synced, run:

```powershell
git status --short --branch
```

How to read it:

- `## main...origin/main` = synced
- `## main...origin/main [ahead 1]` = you have a commit that still needs to be pushed
- `## main...origin/main [behind 1]` = GitHub has changes you still need to pull
- `## main...origin/main [ahead 1, behind 1]` = both changed; stop and ask a maintainer before guessing

### Preferred Method: Use the VS Code Git Buttons

For most reviewers, the safest approach is to use the **Source Control** panel in VS Code instead of typing Git commands manually.

Use this exact order when you are ready to save work to GitHub:

1. **Review your changed files** in the Source Control panel
2. **Commit** in VS Code
3. **Sync Changes** in VS Code
4. **After syncing:** make sure the Source Control panel is clear and the branch shows `main` with no pending sync count

Use the terminal only if VS Code shows an error message or if a maintainer asks you to run a specific command.

### Step 1: Open PowerShell and Navigate to the Repository

```powershell
cd C:\Users\YourName\Documents\file_queuing_system
```

### Step 2: Get the Latest Changes from GitHub

If you have not started editing yet and want to make sure you have the latest copy first, get the latest changes from GitHub.

**Preferred in VS Code:**

1. Open the **Source Control** panel
2. Click **...** at the top of the Source Control panel
3. Click **Pull**, or click **Sync Changes** if VS Code offers it

**Terminal fallback:**

```powershell
git pull --ff-only origin main
```

**What this does:** Downloads any changes other people made since you last worked.

`--ff-only` means Git will only update cleanly. If Git cannot do that safely, it stops instead of making an unexpected merge.

If you already started editing files, do **not** stop and pull in the middle unless a maintainer tells you to. Finish your local work, commit it, and then use **Sync Changes**.

**You'll see:**
```
Already up to date.
```
or
```
Updating abc1234..def5678
Fast-forward
 reviews/... | 10 +++
 1 file changed, 10 insertions(+)
```

### Step 3: Activate Your Python Environment

```powershell
conda activate review-queue
```

Your prompt will change to show `(review-queue)` at the beginning.

### Step 4: Open VS Code

```powershell
code .
```

VS Code will open with the repository loaded.

✅ **You're ready to work!**

---

## Part 3: Doing Your Review Work

This is where you actually do your review. The specific steps depend on whether you're doing a first review or second review (see [CONTRIBUTING.md](../CONTRIBUTING.md) for details).

### Claim an Issue

1. **Go to GitHub** in your web browser:
   ```
   https://github.com/Lizo-RoadTown/file_queuing_system/issues
   ```

2. **Find an issue** labeled `queued` (for first review) or `awaiting-review-2` (for second review)

3. **Click on the issue** to open it

4. **Scroll to the bottom** and type a comment: `/checkout`

5. **Click "Comment"**

The issue will automatically update and assign you. ✅

### Create Your Review Folder

In VS Code:

1. **Right-click** on the `reviews/in-progress/` folder in the left sidebar
2. **Select "New Folder"**
3. **Name it** something descriptive (e.g., `ManuscriptName`)
4. Press **Enter**

### Add Your Files

Add all your review files to this folder:
- Your Jupyter notebook (`.ipynb` file)
- The manuscript PDF
- Output images (PNG files)
- A `metadata.yml` file (see template in [CONTRIBUTING.md](../CONTRIBUTING.md))

**Example folder structure:**
```
reviews/in-progress/MyReview/
├── manuscript.pdf
├── 10.1234.example.ipynb
├── output.png
└── metadata.yml
```

---

## Part 4: Saving Your Work to Git

Now you need to save your work and send it to GitHub. This is a **3-step process**: Stage → Commit → Push.

### Understanding Git Saves

Think of Git like taking a photo:
1. **Stage** = Arrange what you want in the photo
2. **Commit** = Take the photo and write a caption
3. **Push** = Upload the photo to the cloud (GitHub)

### Step 1: Check What Changed

**Preferred in VS Code:**

1. Click the **Source Control** icon in the left sidebar
2. Review the changed files list
3. Make sure you only see the files you intended to change

**Terminal fallback:**

```powershell
git status
```

**You'll see** something like:
```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        reviews/in-progress/MyReview/

nothing added to commit but untracked files present
```

This shows what files you've added or changed (in red).

### Step 2: Stage Your Files

This tells Git which files to save.

**Preferred in VS Code:**

1. In the **Source Control** panel, hover over each file you want to include
2. Click the **+** next to the file, or click **+** next to **Changes** to stage all listed files
3. Double-check that only the right files are under **Staged Changes**

**To add everything you've changed:**
```powershell
git add .
```

The `.` means "add everything."

**Or to add specific files only:**
```powershell
git add reviews/in-progress/MyReview/
```

**Check it worked:**
```powershell
git status
```

Now the files should be in green under "Changes to be committed."

### Step 3: Commit Your Changes (Take the Photo)

This saves your changes **locally** on your computer with a description.

**Preferred in VS Code:**

1. Type a short commit message in the Source Control message box
2. Click **Commit**
3. If VS Code asks whether to stage all changes first, only agree if the file list is correct

**Terminal fallback:**

```powershell
git commit -m "Add first review for MyReview"
```

**The text in quotes** is your commit message. Make it descriptive:
- ✅ `"Add first review for Hepatitis B manuscript"`
- ✅ `"Fix typo in metadata.yml"`
- ✅ `"Add output images for SIR model"`
- ❌ `"stuff"` (too vague)
- ❌ `"update"` (doesn't say what was updated)

**You'll see:**
```
[main abc1234] Add first review for MyReview
 4 files changed, 250 insertions(+)
 create mode 100644 reviews/in-progress/MyReview/manuscript.pdf
 ...
```

### Step 4: Push to GitHub (Upload to the Cloud)

In VS Code, the safer button here is usually **Sync Changes**, not **Push**.

**Preferred in VS Code:**

1. Click **Sync Changes**
2. If VS Code asks to pull first, allow it
3. If VS Code shows a conflict or rejection message, stop and ask a maintainer before choosing options you do not recognize

**Why this is safer:** `Sync Changes` handles both directions. `Push` only sends your work up and is more likely to fail if GitHub changed first.

For this team, the practical rule is simple: people usually do not need to think about syncing until they are ready to commit and send work.

**Terminal fallback:**

```powershell
git pull --rebase origin main
```

If that succeeds, push:

```powershell
git push origin main
```

**You'll see:**
```
Enumerating objects: ...
Counting objects: 100% ...
Writing objects: 100% ...
To https://github.com/Lizo-RoadTown/file_queuing_system.git
   abc1234..def5678  main -> main
```

✅ **Your changes are now on GitHub!**

### Step 5: Confirm You Are Synced Again

**Preferred in VS Code:**

1. Check that the **Source Control** panel is empty
2. Check that the bottom-left branch indicator still shows `main`
3. Check that VS Code is not showing a pending sync count

**Terminal fallback:**

```powershell
git status --short --branch
```

If it shows:

```text
## main...origin/main
```

then your computer and GitHub are synced.

---

## Part 5: Creating a Pull Request

**Important:** You create a **Pull Request**, not a direct merge. A pull request lets others review your work before it's added to the main repository.

### What's a Pull Request?

A pull request (PR) is like saying: *"Hey, I've finished some work. Can you review it and add it to the main repository?"*

**You do NOT merge directly.** Ever. Always create a pull request.

### Step 1: Go to GitHub

Open your web browser and go to:
```
https://github.com/Lizo-RoadTown/file_queuing_system
```

### Step 2: You'll See a Yellow Banner

After you push, GitHub usually shows a yellow banner at the top:

```
[branch icon] main had recent pushes less than a minute ago
                                     [Compare & pull request]
```

**Click the "Compare & pull request" button.**

If you don't see the banner:
1. Click the **"Pull requests"** tab at the top
2. Click the green **"New pull request"** button
3. Make sure it says `base: main` ← `compare: main`
4. Click **"Create pull request"**

### Step 3: Fill Out the Pull Request

You'll see a form:

**Title:** GitHub auto-fills this with your commit message. You can change it if needed.
- Example: `Add first review for MyReview`

**Description:** Add details about what you did:
```
- Added Jupyter notebook reproducing the model
- Included manuscript PDF
- Generated output plots
- Created metadata.yml

Closes #5
```

> **Tip:** If your PR completes an issue, add `Closes #X` where X is the issue number. This automatically closes the issue when the PR is merged.

**Reviewers (optional):** You can request someone review your PR by clicking the gear icon next to "Reviewers" and selecting them.

### Step 4: Create the Pull Request

Click the green **"Create pull request"** button.

✅ **Done!** Your pull request is now created.

### What Happens Next?

1. **Automated checks run** - GitHub Actions will validate your submission
   - Check if you have all required files
   - Verify the folder structure is correct
   - Usually takes 1-2 minutes

2. **You'll see check marks or X's** next to the checks:
   - ✅ Green check = passed
   - ❌ Red X = failed (click "Details" to see why)

3. **If checks fail:**
   - Read the error message
   - Fix the issue in your files
   - Save and push again (repeat Part 4, Steps 2-4)
   - The PR automatically updates with your new changes

4. **Reviewers or maintainers will review your PR**
   - They might request changes
   - They might approve it

5. **Once approved, someone will merge it**
   - NOT you! A maintainer merges it
   - The PR will close automatically
   - Your changes are now part of the main repository

---

## Part 6: If You Need to Make Changes to Your Pull Request

If the automated checks fail or someone requests changes:

### Step 1: Make the Changes

Edit your files in VS Code to fix the issues.

### Step 2: Save to Git Again

```powershell
git add .
git commit -m "Fix metadata.yml format"
git push
```

✅ **Your pull request automatically updates** with the new changes! You don't need to create a new PR.

---

## Part 7: After Your PR is Merged

Once a maintainer merges your pull request:

### Step 1: Pull the Latest Changes

```powershell
git pull
```

This downloads the merged changes back to your computer.

### Step 2: Update the Issue (If Doing First Review)

If you just completed a **first review**:

1. Go to the issue on GitHub
2. The folder should now be moved to `reviews/awaiting-review-2/` by a maintainer OR
3. You can update the issue label to `awaiting-review-2`

If you just completed a **second review**:
- Comment `/approve` on the issue
- Your reviewer work ends there
- If the maintainer has configured email delivery, automation will then move the tracked folder or standalone file into `reviews/completed/`, close the issue, and send the package
- Reviewer separation is enforced automatically when the first reviewer is recorded in `metadata.yml`

---

## Quick Reference: Complete Workflow

### Every Time You Start Working

```powershell
# Navigate to repository
cd C:\Users\YourName\Documents\file_queuing_system

# Get latest changes
git pull

# Activate environment
conda activate review-queue

# Open VS Code
code .
```

### When You're Done Working

```powershell
# Check what changed
git status

# Stage all changes
git add .

# Commit with a message
git commit -m "Describe what you did"

# Push to GitHub
git push
```

### Then on GitHub (web browser)

1. Click **"Compare & pull request"**
2. Fill out title and description
3. Click **"Create pull request"**
4. Wait for checks and approval
5. A maintainer will merge it (not you!)

---

## Common Questions

### Do I merge my own pull request?

**NO.** Always let a maintainer merge it. Creating a pull request is asking for permission to add your changes. Merging is granting that permission.

### What if I have more work to do after creating a PR?

Just edit your files, then:
```powershell
git add .
git commit -m "Add more changes"
git push
```

The PR updates automatically!

### What if someone else merged changes while I was working?

After you commit your work, but before you push:

```powershell
git pull --rebase origin main
```

If there are conflicts, Git will tell you. Stop there and ask a maintainer for help rather than guessing.

### Can I work on multiple reviews at once?

Yes, but it's easier to use **branches** for this. That's an advanced topic - ask a maintainer for guidance.

### I pushed to the wrong place / made a mistake!

Don't panic! Git can undo almost anything. Contact a maintainer and explain what happened.

---

## Troubleshooting

### "Git push rejected" or "Updates were rejected"

**Cause:** Someone else pushed changes before you.

**Fix:**
```powershell
git pull --rebase origin main
git push origin main
```

### "Your branch is behind origin/main"

**Cause:** GitHub has newer changes than your computer.

**Fix:**
```powershell
git pull --ff-only origin main
```

### The safest quick routine in VS Code

If people are nervous, use this exact pattern every time they are ready to save work:

1. Look at the Source Control panel and make sure you only see the files you meant to change
2. Confirm the correct files are staged
3. Commit from the Source Control panel with a clear message
4. Click **Sync Changes** in VS Code
5. If VS Code needs to pull first, allow it
6. Confirm the Source Control panel is empty and the sync indicator is clear

If anyone wants an exact terminal check, `git status --short --branch` should show `## main...origin/main`.

### Short version: what goes before what

1. **Do the work**
2. **Review the changed files**
3. **Commit**
4. **Sync Changes**
5. **Confirm you are synced**

If someone has not started editing yet, they can pull first. But the minimum sequence they need to remember is: **Commit, then Sync Changes, then confirm you are synced.**

Do **not** tell reviewers to push first in order to sync. That is the step that causes many of the avoidable errors.

### "There isn't anything to compare" when creating PR

**Cause:** You didn't push your changes yet.

**Fix:** Go back to Part 4 and push your changes.

### "Please tell me who you are" error

**Cause:** Git doesn't know your name/email.

**Fix:**
```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Then try committing again.

### Can't push - asks for password every time

**Fix:** Use your Personal Access Token, not your GitHub password. If you lost it, generate a new one (see [LOCAL_SETUP_GUIDE.md - Step 2.3](LOCAL_SETUP_GUIDE.md#step-2-get-access-to-the-repository)).

---

## Getting Help

If you're stuck:

1. **Check this guide's Troubleshooting section** (above)
2. **Check** [LOCAL_SETUP_GUIDE.md - Troubleshooting](LOCAL_SETUP_GUIDE.md#troubleshooting)
3. **Ask a maintainer or teammate** - share:
   - What you were trying to do
   - The exact error message
   - What you've already tried
4. **Open an issue** on GitHub labeled "help wanted"

**Remember:** Everyone learns this eventually. Don't be afraid to ask questions!

---

## Summary

1. **Clone once** (first time only)
2. **Pull** every time you start working
3. **Do your review work** in `reviews/in-progress/`
4. **Stage, commit, push** your changes
5. **Create a pull request** on GitHub (don't merge!)
6. **Wait for approval** and let a maintainer merge

That's it! Follow these steps and you'll be contributing successfully. 🎉
