---
name: gabe-repo-triage
description: >
  Triage one repository's maintainer queue - open issues, PRs, CI status, and
  unreleased changelog - and sort every item into autonomous / needs-Gabe /
  defer-close, URL-first. Use when Gabe says "triage <repo>", "what's in my
  queue", or "review the PRs/issues on X". Read-only triage by default; only
  acts when told to work autonomously.
---

# repo-triage

Maintainer-facing queue review for one repo (unless told "broad", "all", or given several). Output is URL-first: every item leads with its GitHub link.

## Gates before any local work

- Checkout is on `main`, pull is clean, worktree has no uncommitted changes. If not, stop and report; do not mutate Gabe's working tree.
- Read `VISION.md` / `README.md` / `CLAUDE.md` if present for the product-fit baseline.
- Gabe's own comments on an issue or PR are authoritative routing.

## Gather

Use `gh` against the remote for open issues, open PRs (draft vs ready, mergeable), CI on the default branch and PR heads, and the changelog or latest tag vs `main` for unreleased changes. For a no-remote repo, triage the working tree and git log (dirty state, stale branches, TODO/FIXME density, failing local checks).

## Classify

Give each item a one-line read: why it matters, author trust (factual: account age, activity, prior contributions), product fit, risk low/medium/high with blast radius, proof state (bugs need repro or logs, features an end-to-end plan, security changes code-path validation), blockers, next action. Then sort:

1. **Autonomous**: fixable without Gabe (clear-repro bugs, docs, narrow tests, low-risk cleanup, dependency bumps passing CI).
2. **Needs Gabe**: his decision, missing credentials, security judgment, unclear direction, not yet decision-ready.
3. **Defer / close / supersede**: stale, duplicate, or overlapped.

## Autonomous mode

Only when told to "work autonomously": for each autonomous item, implement the smallest correct fix, verify locally and end-to-end, run `/code-review` before committing, get CI green, post test evidence, open the PR. Never push to `main`, never release. Return to clean `main` and continue until the queue is empty or a blocker appears, then report and stop.
