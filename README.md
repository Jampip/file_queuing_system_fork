# SDE Manuscript Review Queue

A queue for two-stage peer review of stochastic differential equation manuscripts and their matching Jupyter notebooks.

## How to use it

Each manuscript is tracked as a **GitHub Issue**. Reviewers self-select work by commenting slash commands on issues:

| Command | What it does |
|---|---|
| `/checkout` | Claim a queued item for review |
| `/release`  | Hand it back to the queue |
| `/approve`  | (Second reviewer) finalize the review |

A manuscript moves through these stages automatically:

```
queued  →  review-1-active  →  awaiting-review-2  →  review-2-active  →  complete
```

The second reviewer must be a different person than the first — this is enforced automatically. When a review is approved, the package is zipped and emailed to the designated recipient, and the issue closes.

**New to the repository?** Start here:
1. [`docs/LOCAL_SETUP_GUIDE.md`](docs/LOCAL_SETUP_GUIDE.md) - First-time setup (install Git, VS Code, Python, etc.)
2. [`docs/WORKFLOW_GUIDE.md`](docs/WORKFLOW_GUIDE.md) - Step-by-step: clone, add files, and create pull requests
3. [`CONTRIBUTING.md`](CONTRIBUTING.md) - What to do in each review (content-specific instructions)

**To add a manuscript to the queue:** open a new issue using the *"Add manuscript to review queue"* template.

## Maintainers

See [`docs/SETUP.md`](docs/SETUP.md) for first-time setup, secrets configuration, and ownership transfer.
