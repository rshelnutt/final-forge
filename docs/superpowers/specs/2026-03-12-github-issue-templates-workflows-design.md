# GitHub Issue Templates & Workflows Design

**Date:** 2026-03-12
**Status:** Draft
**Scope:** `final-forge` repo (public issue tracking + user feedback for both web and mobile apps)

## Overview

Replace the existing Markdown issue templates with structured YAML form templates and add GitHub Actions workflows for automated triage, duplicate detection, lifecycle management, and cleanup. Modeled after Anthropic's `claude-code` `.github` setup, adapted for Final Forge.

## Audience

- **Issue filers:** Public users of Final Forge (web and mobile)
- **Triagers:** Small internal team

## Issue Templates

All templates use GitHub's YAML form syntax (dropdowns, checkboxes, textareas with validation). Blank issues are disabled via `config.yml` to force structured input.

Each template file includes top-level `name` and `description` fields (required by GitHub for the template chooser UI).

### 1. Bug Report (`bug_report.yml`)

**Auto-labels:** `bug`

**Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Preflight checklist | checkboxes | yes | Searched existing issues, single bug report |
| What's Wrong? | textarea | yes | Description of the bug |
| What Should Happen? | textarea | yes | Expected behavior |
| Steps to Reproduce | textarea | yes | Numbered steps with context |
| Error Messages/Logs | textarea (code) | no | Stack traces, console output |
| Platform | dropdown | yes | Web, Mobile (iOS), Mobile (Android) |
| Browser | dropdown | no | Chrome, Firefox, Safari, Edge, Other (shown for web bugs) |
| Feature Area | dropdown | yes | Binder Builder, Deck Builder, Card Search, Collection Management, UI/UX, Performance, Login/Account, Other |
| Screenshots | textarea | no | Drag-and-drop images |
| Additional Context | textarea | no | |

**Platform label mapping:**
- "Web" → `website`
- "Mobile (iOS)" → `mobile` + `ios`
- "Mobile (Android)" → `mobile` + `android`

The Claude triage workflow will apply these platform labels and feature area labels (`binder-builder`, `deck-builder`, etc.) based on dropdown selections and issue content.

### 2. Feature Request (`feature_request.yml`)

**Auto-labels:** `feat` (for new features) or `enhancement` (for improvements to existing features — applied by Claude triage based on content)

Note: Both `feat` and `enhancement` already exist in the repo. `feat` = "New feature or request", `enhancement` = "Addition or expansion of existing feature". The template auto-applies `feat`; the triage workflow may reclassify to `enhancement` if the request is about improving an existing feature.

**Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Preflight checklist | checkboxes | yes | Searched existing requests, single feature |
| Problem Statement | textarea | yes | What problem does this solve? |
| Proposed Solution | textarea | yes | How should it work? |
| Alternative Solutions | textarea | no | Workarounds or other approaches considered |
| Platform | dropdown | yes | Web, Mobile, Both. Mapping: "Web" → `website`, "Mobile" → `mobile`, "Both" → `website` + `mobile` |
| Feature Area | dropdown | yes | Binder Builder, Deck Builder, Card Search, Collection Management, UI/UX, Performance, Login/Account, Other |
| Use Case Example | textarea | no | Concrete scenario |
| Additional Context | textarea | no | Mockups, examples, links |

### 3. Documentation Issue (`documentation.yml`)

**Auto-labels:** `documentation`

**Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Documentation Type | dropdown | yes | Missing, Unclear, Incorrect/Outdated, Typo/Formatting, Missing Examples, Broken Links, Other |
| Location | input | no | URL or path |
| Section/Topic | input | yes | Specific area |
| Current Documentation | textarea | no | Quote what it says now |
| What's Wrong or Missing? | textarea | yes | |
| Suggested Improvement | textarea | yes | Proposed fix or new content |
| Impact | dropdown | yes | High/Medium/Low |
| Additional Context | textarea | no | |

### 4. Card Data Issue (`card-data.yml`)

**Auto-labels:** `card-data`

**Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Issue Type | dropdown | yes | Incorrect Pricing, Missing Card, Wrong Image, Wrong Card Info/Metadata, Outdated Data, Other |
| Card Name | input | yes | Full card name |
| Set Name | input | no | Set/expansion name |
| Platform | dropdown | yes | Web, Mobile, Both |
| What's Wrong? | textarea | yes | Description of the data issue |
| Expected Data | textarea | yes | What the correct data should be |
| Source/Reference | input | no | Scryfall link or other reference for correct data |
| Screenshots | textarea | no | |
| Additional Context | textarea | no | |

The Claude triage workflow will apply sub-labels (`card-pricing`, `missing-card`) based on the Issue Type dropdown selection.

### 5. Config (`config.yml`)

```yaml
blank_issues_enabled: false
```

No contact links for now.

## Labels to Create

These labels need to be added to the repository (in addition to existing labels):

| Label | Color | Description |
|-------|-------|-------------|
| `card-data` | `#fbca04` | Card data issue (pricing, metadata, images) |
| `card-pricing` | `#f9d0c4` | Incorrect or missing price data |
| `missing-card` | `#c5def5` | Card not found or missing from database |
| `binder-builder` | `#bfd4f2` | Binder builder feature area |
| `deck-builder` | `#d4c5f9` | Deck builder feature area |
| `autoclose` | `#ededed` | Scheduled for automatic closure |
| `triage` | `#fbca04` | New issue awaiting review |
| `collection` | `#0e8a16` | Collection management feature area |
| `search` | `#1d76db` | Card search feature area |
| `login-account` | `#e99695` | Login and account feature area |
| `performance` | `#f44611` | Performance-related issue |
| `ui-ux` | `#7057ff` | UI/UX design and interaction |

## Workflows

### 6. Claude Issue Triage (`claude-issue-triage.yml`)

- **Trigger:** `issues: [opened]`, `issue_comment: [created]`
- **Condition:** For `issue_comment`, only fires if `github.event.comment.user.type != 'Bot'` (prevents feedback loops from bot comments)
- **Permissions:** `contents: read`, `issues: write`
- **Action:** Uses `anthropics/claude-code-action@v1` to analyze issue content and apply labels from the existing taxonomy
- **Concurrency:** `issue-triage-${{ github.event.issue.number }}` — one triage job per issue at a time, cancels in-progress runs
- **Behavior:**
  - Reads the issue title, body, and template dropdown selections
  - Applies platform labels: `website`, `mobile`, `ios`, `android`
  - Applies feature area labels: `binder-builder`, `deck-builder`, `collection`, `search`, `card-data`, `card-pricing`, `missing-card`, `login-account`, `performance`, `ui-ux`
  - Applies `triage` label on new issues
  - On follow-up comments from non-bot users, re-evaluates if new info changes the categorization
- **Requires:** `ANTHROPIC_API_KEY` secret

### 7. Claude Duplicate Detection (`claude-dedupe-issues.yml`)

- **Trigger:** `issues: [opened]`, `workflow_dispatch` (manual, with issue number input)
- **Permissions:** `contents: read`, `issues: write`
- **Concurrency:** `issue-dedupe-${{ github.event.issue.number || inputs.issue_number }}` — one dedup job per issue at a time
- **Action:** Uses `anthropics/claude-code-action@v1` to compare new issue against open issues
- **Behavior:**
  - Searches for semantically similar open issues
  - If potential duplicates found, posts a comment listing them with links (e.g., "Possible duplicates: #12, #45")
  - Does NOT auto-close — leaves that decision to maintainers or the auto-close workflow
- **Note:** Runs independently and in parallel with the triage workflow; no conflicts since they modify different aspects (triage adds labels, dedup posts comments)
- **Requires:** `ANTHROPIC_API_KEY` secret

### 8. Auto-Close Duplicates (`auto-close-duplicates.yml`)

- **Trigger:** Daily cron (`0 9 * * *`), `workflow_dispatch`
- **Permissions:** `contents: read`, `issues: write`
- **Action:** Closes issues labeled `duplicate` that have been open for more than 3 days
- **Behavior:** Posts a comment explaining closure. Searches issue comments for references to other issue numbers (e.g., `#123`) posted by bots (from the dedup workflow) and includes a link to the original in the closing comment.

### 9. Claude `@claude` Mention Support (`claude.yml`)

- **Trigger:** `issue_comment: [created]`, `pull_request_review_comment: [created]`, `issues: [opened, assigned]`, `pull_request_review: [submitted]`
- **Condition:** All trigger types require `@claude` in the comment/body/title. Explicit `if` condition on every trigger path prevents overlap with the triage workflow.
- **Permissions:** `contents: read`, `pull-requests: read`, `issues: read`, `id-token: write`
- **Action:** Runs `anthropics/claude-code-action@v1` to respond
- **Requires:** `ANTHROPIC_API_KEY` secret

### 10. Lock Stale Issues (`lock-closed-issues.yml`)

- **Trigger:** Daily cron (`0 14 * * *`), `workflow_dispatch`
- **Permissions:** `issues: write`
- **Concurrency:** `lock-threads`
- **Action:** Uses `actions/github-script` to find closed issues with no activity for 7 days
- **Behavior:**
  - Posts a comment: "This issue has been automatically locked since it was closed and has not had any activity for 7 days. If you're experiencing a similar issue, please file a new issue and reference this one if it's relevant."
  - Locks the issue with reason `resolved`
  - Skips already-locked issues and pull requests

### 11. Issue Lifecycle Comments (`issue-lifecycle-comment.yml`)

- **Trigger:** `issues: [labeled]`
- **Permissions:** `issues: write`
- **Action:** Posts informative comments when key labels are applied
- **Label-to-comment mapping:**
  - `duplicate` → "This issue has been identified as a duplicate. It will be closed in favor of the original. If you believe this is incorrect, please comment."
  - `wontfix` → "This issue has been reviewed and will not be addressed at this time. If you have additional context that might change this decision, please share it."
  - `needs review` → "This issue needs additional information. Please provide the requested details so we can investigate further. Issues without a response may be closed after 14 days."
  - `autoclose` → "This issue is scheduled for automatic closure due to inactivity. Comment on this issue to prevent closure."

### 12. Remove Autoclose Label on Activity (`remove-autoclose-label.yml`)

- **Trigger:** `issue_comment: [created]`
- **Condition:** Issue is open, has `autoclose` label, commenter is not a bot (`github.event.comment.user.login != 'github-actions[bot]'`)
- **Permissions:** `issues: write`
- **Action:** Removes the `autoclose` label

### 13. Non-Write Users Check (`non-write-users-check.yml`)

- **Trigger:** `pull_request` with `paths: ['.github/**']`
- **Permissions:** `contents: read`, `pull-requests: write`
- **Action:** If PR modifies `allowed_non_write_users` in any workflow file, posts a warning comment about security implications. This is relevant because the triage and dedup workflows use `allowed_non_write_users: "*"` to allow non-write users to trigger Claude.
- **Idempotent:** Checks for existing comment marker before posting; won't post duplicate comments

### 14. Issue Sweep (`sweep.yml`)

- **Trigger:** Twice-daily cron (`0 10,22 * * *`), `workflow_dispatch`
- **Permissions:** `issues: write`
- **Concurrency:** `daily-issue-sweep`
- **Action:** Enforces lifecycle timeouts using `actions/github-script`
- **Behavior:**
  - Issues labeled `needs review` with no response after 14 days → add `autoclose` label
  - Issues labeled `autoclose` with no response after 7 days → close with comment
  - Issues labeled `triage` with no activity after 30 days → add `needs review` label and **remove `triage` label** (prevents re-triggering on subsequent sweeps)

## Secrets Required

| Secret | Purpose |
|--------|---------|
| `ANTHROPIC_API_KEY` | Claude-powered triage, dedup, and @claude responses |

`GITHUB_TOKEN` is automatically provided by GitHub Actions.

## What We're Not Including

- **Statsig event logging** — no analytics service configured
- **Issue opened dispatch** — no external target repo
- **Backfill duplicate comments** — can add later if needed for retroactive dedup

## File Structure

```
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.yml
│   ├── feature_request.yml
│   ├── documentation.yml
│   ├── card-data.yml
│   └── config.yml
└── workflows/
    ├── claude-issue-triage.yml
    ├── claude-dedupe-issues.yml
    ├── auto-close-duplicates.yml
    ├── claude.yml
    ├── lock-closed-issues.yml
    ├── issue-lifecycle-comment.yml
    ├── remove-autoclose-label.yml
    ├── non-write-users-check.yml
    └── sweep.yml
```

## Implementation Notes

- The existing Markdown templates (`bug_report.md`, `feature_request.md`) will be deleted and replaced with the YAML versions
- Workflows that reference external scripts (auto-close-duplicates, sweep, lifecycle-comment) will use inline `actions/github-script` instead of Bun scripts, keeping everything self-contained
- The Claude triage workflow prompt will include the full label taxonomy so it can accurately categorize issues
