<<<<<<< HEAD
# Reproducibility Review Queuing System

**University of Florida Laboratory of Systems Medicine**

A transparent, version-controlled system for managing two-stage reproducibility reviews of computational research manuscripts.

---

## System Documentation

This document describes the technical methodology behind the review queue system, including how GitHub features are used to create transparency, enforce review integrity, and maintain a complete audit trail.

---

Click any section below to expand it.

---

<details>
<summary><strong>What is This System?</strong></summary>

This repository manages a queue of manuscripts awaiting reproducibility verification. Each manuscript goes through two independent reviews where reviewers attempt to reproduce computational results from published or submitted research papers.

The system uses GitHub's built-in features to:
- Track which manuscripts are in the queue
- Assign reviewers automatically
- Enforce two-stage independent review (different reviewers)
- Validate submissions automatically
- Maintain a complete history of all activities
- Provide public transparency (or private within collaborator group)

**Key Design Goal:** Everything is visible, traceable, and verifiable. No hidden decisions, no black-box automation.

</details>

---

<details>

**The Solution:** The workflow enforces reviewer separation.

### Mechanism:

1. **When first review completes**, the reviewer's GitHub username is recorded in `metadata.yml`:
   ```yaml
   reviewer_1: jane-smith
   reviewer_1_completed: 2026-04-01
   reviewer_2: null
   state: awaiting-review-2
   ```

2. **When someone comments `/checkout` on an `awaiting-review-2` issue**, the workflow:
   - Reads the `metadata.yml` file
   - Extracts `reviewer_1` value
   - Compares it to the GitHub username of the person commenting
   - **If they match:** Reject the command, post error comment
   - **If they differ:** Proceed with assignment

**Example workflow logic (pseudocode):**
```python
def handle_checkout(issue, commenter):
    if issue.label == "awaiting-review-2":
        metadata = read_yaml(f"reviews/awaiting-review-2/{issue.name}/metadata.yml")
        if metadata['reviewer_1'] == commenter:
            post_comment("❌ You cannot review your own first submission.")
            return
    # Proceed with assignment...
```

**Why this is transparent:**
- The rejection is posted as a public comment on the issue
- The metadata file is visible in the repository
- The workflow code is visible in `.github/workflows/manage-queue.yml`
- Anyone can verify the logic

**Edge case handling:**
- If `metadata.yml` is missing → validation check fails during PR submission
- If `metadata.yml` has wrong format → validation check fails
- If someone manually edits Git history to change metadata → Git log shows the manual edit (not normal workflow)

</details>

---

<details>
<summary><strong>Version Control and Audit Trail</strong></summary>

### Every Action Leaves a Trace

**Issue Comments:**
- **What:** Every slash command, every discussion
- **Who:** GitHub username (authenticated)
- **When:** Timestamp (UTC)
- **Preserved:** Forever (unless issue is deleted, which is rare and logged)

**Label Changes:**
- **Visible in:** Issue timeline, issue events API
- **Shows:** Old label → new label, who made the change, when
- **Preserved:** Forever

**Pull Requests:**
- **Shows:** Exactly what files changed
- **Diff view:** Line-by-line comparison of changes
- **Preserved:** Forever (even after merge)

**Git Commits:**
- **Contains:** Author, timestamp, commit message, full file snapshot
- **Verified by:** Cryptographic hash (SHA-1 or SHA-256)
- **Immutable:** Cannot be secretly changed (would change hash, visible to all)

**Workflow Runs:**
- **Logs:** Complete console output of every automation execution
- **Preserved:** 90 days (GitHub default), can be extended
- **Shows:** What triggered the workflow, what it did, success/failure

### How Reviewers Can Verify History

**To see what happened to a manuscript:**
1. Open the issue
2. Scroll through comments and timeline
3. See all state transitions, assignments, commands

**To see what a reviewer submitted:**
1. Find their pull request (linked from issue or in PR list)
2. Click "Files changed" tab
3. See every file they added and the contents

**To see if automation worked correctly:**
1. Go to "Actions" tab
2. Filter by workflow name
3. Click a workflow run to see full execution log

**To see when files were modified:**
1. Browse to the file in GitHub
2. Click "History" button
3. See all commits that modified the file

**To see who did what in the repository:**
1. Go to "Insights" → "Contributors"
2. See commit counts, lines added/removed per person
3. Click a person to see their commit history

</details>

---

<details>
<summary><strong>Automated Workflows</strong></summary>

All automation is defined in `.github/workflows/` and runs on GitHub's servers.

### 1. Queue Management Workflow (`manage-queue.yml`)

**Triggers:** When a comment is posted on an issue

**Execution Steps:**
1. Parse comment for slash commands: `/checkout`, `/release`, `/approve`
2. Check if commenter is a repository collaborator (permission verification)
3. Read current issue state (labels)
4. Execute command:

   **For `/checkout`:**
   - Verify issue is in `queued` or `awaiting-review-2` state
   - If `awaiting-review-2`: check commenter ≠ first reviewer (read `metadata.yml`)
   - Assign issue to commenter
   - Update label to `review-1-active` or `review-2-active`
   - Post confirmation comment

   **For `/release`:**
   - Verify issue is in `review-1-active` or `review-2-active` state
   - Verify commenter is currently assigned
   - Unassign issue
   - Update label back to `queued` or `awaiting-review-2`
   - Post confirmation comment

   **For `/approve`:**
   - Verify issue is in `review-2-active` state
   - Verify commenter is the second reviewer
   - Trigger completion workflow (zip, email, close)

**Why this is transparent:**
- Code is visible in repository
- Execution log shows each step
- Comments posted by workflow are visible to all

### 2. Submission Validation Workflow (`validate-submission.yml`)

**Triggers:** When a pull request is opened or updated

**Execution Steps:**
1. Find folders added/modified in the PR
2. For each folder in `reviews/in-progress/` or `reviews/awaiting-review-2/`:
   - Check for exactly 1 `.ipynb` file
   - Check for exactly 1 `.pdf` file
   - Check for `metadata.yml` file
   - Validate YAML structure (required fields, correct types)
   - Check that files referenced in YAML exist
   - Check for at least 1 image file (`.png`, `.jpg`, `.svg`) [warning, not error]
3. If all checks pass: mark check as ✅
4. If any check fails: mark check as ❌ with error message

**Why this is transparent:**
- Check status visible on PR page (green checkmark or red X)
- Click "Details" to see full validation log
- Prevents incomplete submissions from being merged
- Forces reviewers to follow structure

### 3. Completion Notification Workflow (`notify-on-complete.yml`)

**Triggers:** When `/approve` command is executed on `review-2-active` issue

**Execution Steps:**
1. Find the review folder in `reviews/awaiting-review-2/`
2. Move folder to `reviews/completed/`
3. Create `.zip` archive of the folder
4. Send email via SMTP with attachment:
   - **To:** Configured recipient (from `NOTIFY_TO` secret)
   - **From:** Configured sender (from `SMTP_USER` secret)
   - **Subject:** "Review complete: [manuscript name]"
   - **Attachment:** Zipped review package
5. Update issue label to `complete`
6. Close issue with automated comment

**Why this is transparent:**
- Workflow execution logged in Actions tab
- Folder move visible in Git commit history
- Issue closure visible in issue timeline
- (Email recipient is configured in repository secrets, visible to maintainers)

</details>

---

<details>
<summary><strong>For Reviewers</strong></summary>

**New reviewers:** Follow this learning path:
1. [LOCAL_SETUP_GUIDE.md](docs/LOCAL_SETUP_GUIDE.md) - Install software and set up environment
2. [WORKFLOW_GUIDE.md](docs/WORKFLOW_GUIDE.md) - Learn Git workflow (clone, commit, pull request)
3. [CONTRIBUTING.md](CONTRIBUTING.md) - Learn what to do in a review

**To claim a manuscript:** Comment `/checkout` on a `queued` or `awaiting-review-2` issue

**To release a manuscript:** Comment `/release` if you need to return it to the queue

**To finalize a review:** (Second reviewer only) Comment `/approve` when verification is complete

</details>

---

<details>
<summary><strong>For Maintainers</strong></summary>

**Setup instructions:** See [docs/SETUP.md](docs/SETUP.md) for:
- Creating required labels
- Configuring repository secrets (SMTP credentials)
- Setting up branch protection rules
- Managing collaborator access

**Merging pull requests:** Review submitted files, verify automated checks pass, then merge

**Handling edge cases:** If workflows fail or issues get stuck, manually update labels and post explanatory comments (all actions still logged)

</details>

---

<details>
<summary><strong>Why This Design?</strong></summary>

**No Custom Servers:**
- GitHub provides all infrastructure (hosting, compute, authentication)
- No server maintenance, no uptime monitoring
- No security patching of custom code

**Complete Transparency:**
- Everything is visible in issues, PRs, commits, and workflow logs
- No "black box" decisions
- Audit trail for accountability

**Decentralized Collaboration:**
- Reviewers work independently
- No coordinator bottleneck
- Self-service assignment

**Automated Integrity:**
- Reviewer separation enforced by code, not policy
- Validation automated, not manual
- State transitions enforced, not trusted

**Familiar Tools:**
- Researchers already use Git and GitHub
- Jupyter notebooks already standard in computational research
- No specialized software to learn

</details>

---

<details open>
<summary><strong>Summary</strong></summary>

This repository manages a queue of manuscripts awaiting reproducibility verification. Each manuscript goes through two independent reviews where reviewers attempt to reproduce computational results from published or submitted research papers.

This system uses standard GitHub features (issues, pull requests, labels, actions, version control) to create a transparent, auditable, and automated review queue. Every action is logged, every decision is visible, and integrity rules (like reviewer separation) are enforced by code rather than policy.

**Transparency mechanisms:**
- Issues show queue status and assignments
- Pull requests show exactly what reviewers submit
- Git commits preserve complete change history
- Workflows automate repetitive tasks with visible logs
- Labels enforce state machine transitions

**Result:** A review queue where nothing happens in secret, everything is traceable, and anyone with access can verify that the process followed the rules.

</details>
=======
# SDE Review Queue

This repository is a lightweight review-tracking system for manuscripts, notebooks, and reproducibility checks.

It was designed for academic teams that need a simple way to coordinate first-pass and second-pass review work without adding a separate database or web application. Undergraduates and graduate reviewers use GitHub issues to claim work, record progress, and hand submissions from one review stage to the next.

## What the system does

- Tracks each submission as a GitHub issue
- Records review packages in the repository so work is visible and auditable
- Enforces a two-stage review flow with different reviewers at each stage
- Automates state changes, packaging, and completion notifications with GitHub Actions

The review lifecycle is:

```text
queued -> review-1-active -> awaiting-review-2 -> review-2-active -> complete
```

## How teams use it

In practice, reviewers pick up work from the issue queue, complete or verify the associated notebook and manuscript materials, and move the package forward. The repository acts as the shared record of who reviewed what, when it was reviewed, and whether it is ready for the next stage.

This keeps the process quick to explain:

- New work enters the queue as an issue
- A first reviewer claims it and prepares the review package
- A second reviewer independently checks it
- Approval closes the issue and triggers the final delivery step

## Documentation

- Reviewer workflow: [docs/USAGE.md](docs/USAGE.md)
- Reviewer step-by-step guide: [CONTRIBUTING.md](CONTRIBUTING.md)
- Maintainer setup: [docs/SETUP.md](docs/SETUP.md)

## Why this approach

The system stays intentionally simple. GitHub provides authentication, history, storage, automation, and issue tracking, so the queue can remain transparent and easy to maintain while still supporting a formal review process.
