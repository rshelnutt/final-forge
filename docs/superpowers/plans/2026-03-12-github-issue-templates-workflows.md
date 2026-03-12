# GitHub Issue Templates & Workflows Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace existing Markdown issue templates with structured YAML form templates and add GitHub Actions workflows for automated triage, duplicate detection, lifecycle management, and cleanup in the `final-forge` repo.

**Architecture:** YAML issue form templates enforce structured input from users. GitHub Actions workflows handle automation: Claude Code Action for AI-powered triage/dedup, `actions/github-script` for lifecycle management. All workflow logic is inline (no external scripts).

**Tech Stack:** GitHub YAML issue forms, GitHub Actions, `anthropics/claude-code-action@v1`, `actions/github-script@v7`, `gh` CLI for label creation.

**Spec:** `docs/superpowers/specs/2026-03-12-github-issue-templates-workflows-design.md`

---

## Chunk 1: Labels and Issue Templates

### Task 1: Create New Labels

Create 12 new labels on the `rshelnutt/final-forge` GitHub repo.

**Files:** None (GitHub API operations only)

- [ ] **Step 1: Create all labels via `gh` CLI**

Run each command:

```bash
gh label create "card-data" --color "fbca04" --description "Card data issue (pricing, metadata, images)" --repo rshelnutt/final-forge
gh label create "card-pricing" --color "f9d0c4" --description "Incorrect or missing price data" --repo rshelnutt/final-forge
gh label create "missing-card" --color "c5def5" --description "Card not found or missing from database" --repo rshelnutt/final-forge
gh label create "binder-builder" --color "bfd4f2" --description "Binder builder feature area" --repo rshelnutt/final-forge
gh label create "deck-builder" --color "d4c5f9" --description "Deck builder feature area" --repo rshelnutt/final-forge
gh label create "autoclose" --color "ededed" --description "Scheduled for automatic closure" --repo rshelnutt/final-forge
gh label create "triage" --color "fbca04" --description "New issue awaiting review" --repo rshelnutt/final-forge
gh label create "collection" --color "0e8a16" --description "Collection management feature area" --repo rshelnutt/final-forge
gh label create "search" --color "1d76db" --description "Card search feature area" --repo rshelnutt/final-forge
gh label create "login-account" --color "e99695" --description "Login and account feature area" --repo rshelnutt/final-forge
gh label create "performance" --color "f44611" --description "Performance-related issue" --repo rshelnutt/final-forge
gh label create "ui-ux" --color "7057ff" --description "UI/UX design and interaction" --repo rshelnutt/final-forge
```

- [ ] **Step 2: Verify labels were created**

```bash
gh label list --repo rshelnutt/final-forge --limit 50
```

Expected: All 12 new labels appear alongside the existing labels.

### Task 2: Delete Old Markdown Templates

Remove the existing Markdown-based issue templates that will be replaced by YAML forms.

**Files:**
- Delete: `.github/ISSUE_TEMPLATE/bug_report.md`
- Delete: `.github/ISSUE_TEMPLATE/feature_request.md`

- [ ] **Step 1: Delete old templates**

```bash
rm .github/ISSUE_TEMPLATE/bug_report.md
rm .github/ISSUE_TEMPLATE/feature_request.md
```

- [ ] **Step 2: Verify deletion**

```bash
ls .github/ISSUE_TEMPLATE/
```

Expected: Directory is empty (or contains only `.DS_Store`).

### Task 3: Create Bug Report Template

**Files:**
- Create: `.github/ISSUE_TEMPLATE/bug_report.yml`

- [ ] **Step 1: Write bug report template**

Create `.github/ISSUE_TEMPLATE/bug_report.yml` with this content:

```yaml
name: "\U0001F41B Bug Report"
description: Report a bug or unexpected behavior in Final Forge
title: "[BUG] "
labels:
  - bug
body:
  - type: markdown
    attributes:
      value: |
        Thanks for reporting this bug! Please fill out the sections below so we can investigate.

        Before submitting, please check:
        - You've searched [existing issues](https://github.com/rshelnutt/final-forge/issues?q=is%3Aissue+state%3Aopen+label%3Abug) to make sure this hasn't been reported yet.

  - type: checkboxes
    id: preflight
    attributes:
      label: Preflight Checklist
      options:
        - label: I have searched [existing issues](https://github.com/rshelnutt/final-forge/issues?q=is%3Aissue+state%3Aopen+label%3Abug) and this hasn't been reported yet
          required: true
        - label: This is a single bug report (please file separate reports for different bugs)
          required: true

  - type: textarea
    id: actual
    attributes:
      label: What's Wrong?
      description: Describe what's happening that shouldn't be
      placeholder: |
        When I try to add a card to my binder, the page shows a blank screen...
    validations:
      required: true

  - type: textarea
    id: expected
    attributes:
      label: What Should Happen?
      description: Describe the expected behavior
      placeholder: The card should be added to my binder and the page should update to show it
    validations:
      required: true

  - type: textarea
    id: reproduction
    attributes:
      label: Steps to Reproduce
      description: Please provide clear, numbered steps that anyone can follow to reproduce the issue.
      placeholder: |
        1. Go to the Binder Builder page
        2. Search for "Lightning Bolt"
        3. Click "Add to Binder"
        4. See blank screen
    validations:
      required: true

  - type: textarea
    id: error_output
    attributes:
      label: Error Messages/Logs
      description: If you see any error messages, paste them here
      placeholder: Paste any error output or console messages here.
      render: shell
    validations:
      required: false

  - type: dropdown
    id: platform
    attributes:
      label: Platform
      description: Which platform are you using?
      options:
        - Web
        - Mobile (iOS)
        - Mobile (Android)
    validations:
      required: true

  - type: dropdown
    id: browser
    attributes:
      label: Browser
      description: If using the web app, which browser?
      options:
        - Chrome
        - Firefox
        - Safari
        - Edge
        - Other
    validations:
      required: false

  - type: dropdown
    id: feature_area
    attributes:
      label: Feature Area
      description: Which part of the app is affected?
      options:
        - Binder Builder
        - Deck Builder
        - Card Search
        - Collection Management
        - UI/UX
        - Performance
        - Login/Account
        - Other
    validations:
      required: true

  - type: textarea
    id: screenshots
    attributes:
      label: Screenshots
      description: If applicable, drag and drop screenshots here
    validations:
      required: false

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Anything else that might help us understand the issue?
    validations:
      required: false
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/ISSUE_TEMPLATE/bug_report.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 4: Create Feature Request Template

**Files:**
- Create: `.github/ISSUE_TEMPLATE/feature_request.yml`

- [ ] **Step 1: Write feature request template**

Create `.github/ISSUE_TEMPLATE/feature_request.yml` with this content:

```yaml
name: "\u2728 Feature Request"
description: Suggest a new feature or enhancement for Final Forge
title: "[FEATURE] "
labels:
  - feat
body:
  - type: markdown
    attributes:
      value: |
        Thanks for suggesting a feature! Please help us understand your use case.

        Before submitting, please check if this feature has already been requested by searching [existing requests](https://github.com/rshelnutt/final-forge/issues?q=is%3Aissue+label%3Afeat).

  - type: checkboxes
    id: preflight
    attributes:
      label: Preflight Checklist
      options:
        - label: I have searched [existing requests](https://github.com/rshelnutt/final-forge/issues?q=is%3Aissue+label%3Afeat+label%3Aenhancement) and this feature hasn't been requested yet
          required: true
        - label: This is a single feature request (not multiple features)
          required: true

  - type: textarea
    id: problem
    attributes:
      label: Problem Statement
      description: What problem are you trying to solve? Focus on the problem, not the solution.
      placeholder: |
        I often need to compare prices across multiple printings of a card, but currently I can only see one printing at a time...
    validations:
      required: true

  - type: textarea
    id: solution
    attributes:
      label: Proposed Solution
      description: How would you like this to work?
      placeholder: |
        I'd like a comparison view that shows all printings side-by-side with their current prices...
    validations:
      required: true

  - type: textarea
    id: alternatives
    attributes:
      label: Alternative Solutions
      description: What alternatives have you considered or tried?
      placeholder: |
        Currently I open multiple browser tabs to compare...
    validations:
      required: false

  - type: dropdown
    id: platform
    attributes:
      label: Platform
      description: Which platform should this feature be on?
      options:
        - Web
        - Mobile
        - Both
    validations:
      required: true

  - type: dropdown
    id: feature_area
    attributes:
      label: Feature Area
      description: What area does this feature relate to?
      options:
        - Binder Builder
        - Deck Builder
        - Card Search
        - Collection Management
        - UI/UX
        - Performance
        - Login/Account
        - Other
    validations:
      required: true

  - type: textarea
    id: use_case
    attributes:
      label: Use Case Example
      description: Provide a concrete, real-world scenario of when you'd use this feature.
      placeholder: |
        1. I'm building a Commander deck
        2. I want to track which cards I already own
        3. With this feature I could...
    validations:
      required: false

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Mockups, examples, links to similar features in other tools, etc.
    validations:
      required: false
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/ISSUE_TEMPLATE/feature_request.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 5: Create Documentation Issue Template

**Files:**
- Create: `.github/ISSUE_TEMPLATE/documentation.yml`

- [ ] **Step 1: Write documentation template**

Create `.github/ISSUE_TEMPLATE/documentation.yml` with this content:

```yaml
name: "\U0001F4DA Documentation Issue"
description: Report missing, unclear, or incorrect documentation
title: "[DOCS] "
labels:
  - documentation
body:
  - type: markdown
    attributes:
      value: |
        Help us improve our documentation! Please let us know what's missing or confusing.

  - type: dropdown
    id: doc_type
    attributes:
      label: Documentation Type
      description: What kind of documentation issue is this?
      options:
        - Missing documentation (feature not documented)
        - Unclear/confusing documentation
        - Incorrect/outdated documentation
        - Typo or formatting issue
        - Missing code examples
        - Broken links
        - Other
    validations:
      required: true

  - type: input
    id: location
    attributes:
      label: Documentation Location
      description: Where did you encounter this issue? Provide a URL if possible.
      placeholder: "e.g., https://finalforge.app/docs/getting-started"
    validations:
      required: false

  - type: input
    id: section
    attributes:
      label: Section/Topic
      description: Which specific section or topic needs improvement?
      placeholder: "e.g., Binder Builder usage guide"
    validations:
      required: true

  - type: textarea
    id: current
    attributes:
      label: Current Documentation
      description: What does the documentation currently say? Quote the specific text if applicable.
      placeholder: |
        The docs currently say:
        "To add a card to your binder, click..."
    validations:
      required: false

  - type: textarea
    id: issue
    attributes:
      label: What's Wrong or Missing?
      description: Explain what's incorrect, unclear, or missing
      placeholder: |
        The documentation doesn't explain how to...
    validations:
      required: true

  - type: textarea
    id: suggested
    attributes:
      label: Suggested Improvement
      description: How should the documentation be improved?
      placeholder: |
        The documentation should include a step-by-step guide for...
    validations:
      required: true

  - type: dropdown
    id: impact
    attributes:
      label: Impact
      description: How much does this documentation issue affect users?
      options:
        - High - Prevents users from using a feature
        - Medium - Makes feature difficult to understand
        - Low - Minor confusion or inconvenience
    validations:
      required: true

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Screenshots, links to related documentation, etc.
    validations:
      required: false
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/ISSUE_TEMPLATE/documentation.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 6: Create Card Data Issue Template

**Files:**
- Create: `.github/ISSUE_TEMPLATE/card-data.yml`

- [ ] **Step 1: Write card data template**

Create `.github/ISSUE_TEMPLATE/card-data.yml` with this content:

```yaml
name: "\U0001F0CF Card Data Issue"
description: Report incorrect pricing, missing cards, or wrong card information
title: "[CARD DATA] "
labels:
  - card-data
body:
  - type: markdown
    attributes:
      value: |
        Report an issue with card data (pricing, images, metadata, or missing cards).

        Card data is sourced from Scryfall. If possible, include a link to the correct data on Scryfall so we can compare.

  - type: dropdown
    id: issue_type
    attributes:
      label: Issue Type
      description: What kind of card data issue is this?
      options:
        - Incorrect Pricing
        - Missing Card
        - Wrong Image
        - Wrong Card Info/Metadata
        - Outdated Data
        - Other
    validations:
      required: true

  - type: input
    id: card_name
    attributes:
      label: Card Name
      description: The full name of the affected card
      placeholder: "e.g., Lightning Bolt"
    validations:
      required: true

  - type: input
    id: set_name
    attributes:
      label: Set Name
      description: The set or expansion the card belongs to
      placeholder: "e.g., Alpha, Modern Horizons 3"
    validations:
      required: false

  - type: dropdown
    id: platform
    attributes:
      label: Platform
      description: Where did you see this issue?
      options:
        - Web
        - Mobile
        - Both
    validations:
      required: true

  - type: textarea
    id: whats_wrong
    attributes:
      label: What's Wrong?
      description: Describe the data issue you're seeing
      placeholder: |
        The price shown for Lightning Bolt (Alpha) is $0.25, but it should be much higher...
    validations:
      required: true

  - type: textarea
    id: expected_data
    attributes:
      label: Expected Data
      description: What the correct data should be
      placeholder: |
        According to Scryfall/TCGPlayer, the correct price is approximately $X...
    validations:
      required: true

  - type: input
    id: source_reference
    attributes:
      label: Source/Reference
      description: Link to Scryfall or another source with the correct data
      placeholder: "e.g., https://scryfall.com/card/lea/232/lightning-bolt"
    validations:
      required: false

  - type: textarea
    id: screenshots
    attributes:
      label: Screenshots
      description: If applicable, drag and drop screenshots showing the incorrect data
    validations:
      required: false

  - type: textarea
    id: additional
    attributes:
      label: Additional Context
      description: Any other information that might help
    validations:
      required: false
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/ISSUE_TEMPLATE/card-data.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 7: Create Template Config

**Files:**
- Create: `.github/ISSUE_TEMPLATE/config.yml`

- [ ] **Step 1: Write config**

Create `.github/ISSUE_TEMPLATE/config.yml` with this content:

```yaml
blank_issues_enabled: false
```

- [ ] **Step 2: Commit all templates**

```bash
git add .github/ISSUE_TEMPLATE/
git commit -m "Replace Markdown issue templates with YAML forms

Delete old bug_report.md and feature_request.md templates. Add structured
YAML form templates: bug report, feature request, documentation issue,
and card data issue. Disable blank issues via config.yml."
```

---

## Chunk 2: Claude-Powered Workflows

### Task 8: Create Claude Issue Triage Workflow

**Files:**
- Create: `.github/workflows/claude-issue-triage.yml`

- [ ] **Step 1: Write triage workflow**

Create `.github/workflows/claude-issue-triage.yml` with this content:

```yaml
name: Claude Issue Triage
on:
  issues:
    types: [opened]
  issue_comment:
    types: [created]

jobs:
  triage-issue:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    if: >-
      github.event_name == 'issues' ||
      (github.event_name == 'issue_comment' && !github.event.issue.pull_request && github.event.comment.user.type != 'Bot')
    concurrency:
      group: issue-triage-${{ github.event.issue.number }}
      cancel-in-progress: true
    permissions:
      contents: read
      issues: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run Claude Code for Issue Triage
        timeout-minutes: 5
        uses: anthropics/claude-code-action@v1
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GH_REPO: ${{ github.repository }}
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          allowed_non_write_users: "*"
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            You are an issue triage bot for Final Forge, a Magic: The Gathering card collection and deck building app (web + mobile).

            Analyze this issue and apply the appropriate labels. The issue is #${{ github.event.issue.number }} in ${{ github.repository }}.

            ## Label Taxonomy

            **Platform labels** (based on Platform dropdown or content):
            - `website` — Web app issues
            - `mobile` — Mobile app issues (apply alongside ios/android)
            - `ios` — iOS-specific issues
            - `android` — Android-specific issues

            Platform mapping rules:
            - "Web" → add `website`
            - "Mobile (iOS)" → add `mobile` + `ios`
            - "Mobile (Android)" → add `mobile` + `android`
            - "Mobile" → add `mobile`
            - "Both" → add `website` + `mobile`

            **Feature area labels** (based on Feature Area dropdown or content):
            - `binder-builder` — Binder builder
            - `deck-builder` — Deck builder
            - `search` — Card search
            - `collection` — Collection management
            - `ui-ux` — UI/UX design and interaction
            - `performance` — Performance issues
            - `login-account` — Login and account

            **Card data sub-labels** (for card-data issues, based on Issue Type dropdown):
            - `card-pricing` — Incorrect Pricing
            - `missing-card` — Missing Card

            **Issue type labels** (may reclassify based on content):
            - `feat` — New feature request (keep if truly new)
            - `enhancement` — Improvement to existing feature (reclassify from `feat` if appropriate)

            **Always apply on new issues:**
            - `triage` — Mark for team review

            ## Instructions

            1. Read the issue title, body, and any template dropdown selections.
            2. Apply the appropriate platform label(s) per the mapping rules above.
            3. Apply the appropriate feature area label(s).
            4. For card-data issues, apply the appropriate sub-label.
            5. If this is a new issue (not a comment update), apply the `triage` label.
            6. For feature requests, assess whether it's truly new (`feat`) or an improvement to something that exists (`enhancement`). Reclassify if needed.
            7. Do NOT remove labels that were applied by the template itself (like `bug`, `feat`, `card-data`, `documentation`).
            8. Do NOT apply priority labels (P0-blocking, P1-high, critical) — those are for the team only.

            Apply labels silently. Do not post a comment.
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/claude-issue-triage.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 9: Create Claude Duplicate Detection Workflow

**Files:**
- Create: `.github/workflows/claude-dedupe-issues.yml`

- [ ] **Step 1: Write dedup workflow**

Create `.github/workflows/claude-dedupe-issues.yml` with this content:

```yaml
name: Claude Issue Dedupe
on:
  issues:
    types: [opened]
  workflow_dispatch:
    inputs:
      issue_number:
        description: "Issue number to check for duplicates"
        required: true
        type: string

jobs:
  claude-dedupe-issues:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    concurrency:
      group: issue-dedupe-${{ github.event.issue.number || inputs.issue_number }}
      cancel-in-progress: true
    permissions:
      contents: read
      issues: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run Claude Code for Duplicate Detection
        timeout-minutes: 5
        uses: anthropics/claude-code-action@v1
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          allowed_non_write_users: "*"
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            You are a duplicate issue detector for Final Forge, a Magic: The Gathering card app.

            Check if issue #${{ github.event.issue.number || inputs.issue_number }} in ${{ github.repository }} is a duplicate of any existing open issue.

            ## Instructions

            1. Read the new issue's title and body.
            2. Use the GitHub CLI to search open issues: `gh issue list --repo ${{ github.repository }} --state open --limit 100 --json number,title,body,labels`
            3. Compare the new issue against existing open issues. Look for:
               - Same bug being reported (even if described differently)
               - Same feature being requested
               - Same documentation gap
               - Same card data problem
            4. If you find potential duplicates (>70% confidence), post a SINGLE comment on the issue:

            Format:
            ```
            **Possible duplicates found:**
            - #<number> - <title> (reason for match)
            - #<number> - <title> (reason for match)

            If this is indeed a duplicate, a maintainer will mark it accordingly.
            ```

            5. If no duplicates found, do nothing. Do NOT post a comment saying "no duplicates found."
            6. Be conservative — only flag issues that are genuinely similar. Different bugs in the same feature area are NOT duplicates.
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/claude-dedupe-issues.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 10: Create Claude @mention Workflow

**Files:**
- Create: `.github/workflows/claude.yml`

- [ ] **Step 1: Write Claude mention workflow**

Create `.github/workflows/claude.yml` with this content:

```yaml
name: Claude Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]
  pull_request_review:
    types: [submitted]

jobs:
  claude:
    if: |
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'pull_request_review' && contains(github.event.review.body, '@claude')) ||
      (github.event_name == 'issues' && (contains(github.event.issue.body, '@claude') || contains(github.event.issue.title, '@claude')))
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      issues: write
      id-token: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 1

      - name: Run Claude Code
        id: claude
        uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/claude.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

- [ ] **Step 3: Commit Claude-powered workflows**

```bash
git add .github/workflows/claude-issue-triage.yml .github/workflows/claude-dedupe-issues.yml .github/workflows/claude.yml
git commit -m "Add Claude-powered workflows: triage, dedup, @mention

- claude-issue-triage: auto-labels new issues using Claude Code Action
- claude-dedupe-issues: detects duplicate issues on open
- claude.yml: responds to @claude mentions in issues/PRs"
```

---

## Chunk 3: Lifecycle Management Workflows

### Task 11: Create Auto-Close Duplicates Workflow

**Files:**
- Create: `.github/workflows/auto-close-duplicates.yml`

- [ ] **Step 1: Write auto-close workflow**

Create `.github/workflows/auto-close-duplicates.yml` with this content:

```yaml
name: Auto-close duplicate issues

on:
  schedule:
    - cron: "0 9 * * *"
  workflow_dispatch:

jobs:
  auto-close-duplicates:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    permissions:
      contents: read
      issues: write

    steps:
      - name: Auto-close duplicate issues
        uses: actions/github-script@v7
        with:
          script: |
            const threeDaysAgo = new Date();
            threeDaysAgo.setDate(threeDaysAgo.getDate() - 3);

            let page = 1;
            let totalClosed = 0;

            while (true) {
              const { data: issues } = await github.rest.issues.listForRepo({
                owner: context.repo.owner,
                repo: context.repo.repo,
                state: 'open',
                labels: 'duplicate',
                per_page: 100,
                page: page
              });

              if (issues.length === 0) break;

              for (const issue of issues) {
                if (issue.pull_request) continue;

                // Use updated_at as a proxy for last activity — if any activity
                // happened within 3 days, skip to give maintainers time to review
                const lastActivity = new Date(issue.updated_at);
                if (lastActivity > threeDaysAgo) continue;

                // Search bot comments for duplicate references (from dedup workflow)
                let originalRef = '';
                try {
                  const { data: comments } = await github.rest.issues.listComments({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    per_page: 50
                  });

                  for (const comment of comments) {
                    // Only check bot comments for duplicate references
                    if (comment.user.type !== 'Bot' && comment.user.login !== 'github-actions[bot]') continue;
                    const match = comment.body.match(/#(\d+)/);
                    if (match) {
                      originalRef = ` See #${match[1]} for the original issue.`;
                      break;
                    }
                  }
                } catch (e) {
                  console.log(`Failed to fetch comments for #${issue.number}: ${e.message}`);
                }

                try {
                  await github.rest.issues.createComment({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    body: `This issue has been automatically closed as a duplicate.${originalRef} If you believe this is incorrect, please comment and a maintainer will review.`
                  });

                  await github.rest.issues.update({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    state: 'closed',
                    state_reason: 'not_planned'
                  });

                  totalClosed++;
                  console.log(`Closed duplicate issue #${issue.number}`);
                } catch (e) {
                  console.error(`Failed to close #${issue.number}: ${e.message}`);
                }
              }

              page++;
            }

            console.log(`Total duplicate issues closed: ${totalClosed}`);
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/auto-close-duplicates.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 12: Create Lock Stale Issues Workflow

**Files:**
- Create: `.github/workflows/lock-closed-issues.yml`

- [ ] **Step 1: Write lock workflow**

Create `.github/workflows/lock-closed-issues.yml` with this content:

```yaml
name: Lock Stale Issues

on:
  schedule:
    - cron: "0 14 * * *"
  workflow_dispatch:

permissions:
  issues: write

concurrency:
  group: lock-threads

jobs:
  lock-closed-issues:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Lock closed issues after 7 days of inactivity
        uses: actions/github-script@v7
        with:
          script: |
            const sevenDaysAgo = new Date();
            sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);

            const lockComment = `This issue has been automatically locked since it was closed and has not had any activity for 7 days. If you're experiencing a similar issue, please file a new issue and reference this one if it's relevant.`;

            let page = 1;
            let hasMore = true;
            let totalLocked = 0;

            while (hasMore) {
              const { data: issues } = await github.rest.issues.listForRepo({
                owner: context.repo.owner,
                repo: context.repo.repo,
                state: 'closed',
                sort: 'updated',
                direction: 'asc',
                per_page: 100,
                page: page
              });

              if (issues.length === 0) {
                hasMore = false;
                break;
              }

              for (const issue of issues) {
                if (issue.locked) continue;
                if (issue.pull_request) continue;

                const updatedAt = new Date(issue.updated_at);
                if (updatedAt > sevenDaysAgo) {
                  hasMore = false;
                  break;
                }

                try {
                  await github.rest.issues.createComment({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    body: lockComment
                  });

                  await github.rest.issues.lock({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    lock_reason: 'resolved'
                  });

                  totalLocked++;
                  console.log(`Locked issue #${issue.number}: ${issue.title}`);
                } catch (error) {
                  console.error(`Failed to lock issue #${issue.number}: ${error.message}`);
                }
              }

              page++;
            }

            console.log(`Total issues locked: ${totalLocked}`);
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/lock-closed-issues.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 13: Create Issue Lifecycle Comments Workflow

**Files:**
- Create: `.github/workflows/issue-lifecycle-comment.yml`

- [ ] **Step 1: Write lifecycle comment workflow**

Create `.github/workflows/issue-lifecycle-comment.yml` with this content:

```yaml
name: Issue Lifecycle Comment

on:
  issues:
    types: [labeled]

permissions:
  issues: write

jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - name: Post lifecycle comment
        uses: actions/github-script@v7
        with:
          script: |
            const label = context.payload.label.name;

            const comments = {
              'duplicate': 'This issue has been identified as a duplicate. It will be closed in favor of the original. If you believe this is incorrect, please comment.',
              'wontfix': 'This issue has been reviewed and will not be addressed at this time. If you have additional context that might change this decision, please share it.',
              'needs review': 'This issue needs additional information. Please provide the requested details so we can investigate further. Issues without a response may be closed after 14 days.',
              'autoclose': 'This issue is scheduled for automatic closure due to inactivity. Comment on this issue to prevent closure.'
            };

            const message = comments[label];
            if (!message) return;

            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: message
            });

            console.log(`Posted lifecycle comment for label: ${label}`);
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/issue-lifecycle-comment.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 14: Create Remove Autoclose Label Workflow

**Files:**
- Create: `.github/workflows/remove-autoclose-label.yml`

- [ ] **Step 1: Write remove autoclose workflow**

Create `.github/workflows/remove-autoclose-label.yml` with this content:

```yaml
name: Remove Autoclose Label on Activity

on:
  issue_comment:
    types: [created]

permissions:
  issues: write

jobs:
  remove-autoclose:
    if: |
      github.event.issue.state == 'open' &&
      contains(github.event.issue.labels.*.name, 'autoclose') &&
      github.event.comment.user.login != 'github-actions[bot]'
    runs-on: ubuntu-latest
    steps:
      - name: Remove autoclose label
        uses: actions/github-script@v7
        with:
          script: |
            console.log(`Removing autoclose label from issue #${context.issue.number} due to new comment from ${context.payload.comment.user.login}`);

            try {
              await github.rest.issues.removeLabel({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: context.issue.number,
                name: 'autoclose'
              });
              console.log(`Successfully removed autoclose label from issue #${context.issue.number}`);
            } catch (error) {
              if (error.status === 404) {
                console.log(`Autoclose label was already removed from issue #${context.issue.number}`);
              } else {
                throw error;
              }
            }
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/remove-autoclose-label.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

### Task 15: Create Issue Sweep Workflow

**Files:**
- Create: `.github/workflows/sweep.yml`

- [ ] **Step 1: Write sweep workflow**

Create `.github/workflows/sweep.yml` with this content:

```yaml
name: Issue Sweep

on:
  schedule:
    - cron: "0 10,22 * * *"
  workflow_dispatch:

permissions:
  issues: write

concurrency:
  group: daily-issue-sweep

jobs:
  sweep:
    runs-on: ubuntu-latest
    steps:
      - name: Enforce lifecycle timeouts
        uses: actions/github-script@v7
        with:
          script: |
            const now = new Date();

            async function getIssuesWithLabel(label) {
              const issues = [];
              let page = 1;
              while (true) {
                const { data } = await github.rest.issues.listForRepo({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  state: 'open',
                  labels: label,
                  per_page: 100,
                  page: page
                });
                if (data.length === 0) break;
                issues.push(...data.filter(i => !i.pull_request));
                page++;
              }
              return issues;
            }

            function daysSince(dateStr) {
              return (now - new Date(dateStr)) / (1000 * 60 * 60 * 24);
            }

            // 1. needs review + no response > 14 days → add autoclose
            const needsReview = await getIssuesWithLabel('needs review');
            for (const issue of needsReview) {
              if (daysSince(issue.updated_at) > 14) {
                const hasAutoclose = issue.labels.some(l => l.name === 'autoclose');
                if (!hasAutoclose) {
                  await github.rest.issues.addLabels({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    labels: ['autoclose']
                  });
                  console.log(`Added autoclose to issue #${issue.number} (needs review > 14 days)`);
                }
              }
            }

            // 2. autoclose + no response > 7 days → close
            const autoclose = await getIssuesWithLabel('autoclose');
            for (const issue of autoclose) {
              if (daysSince(issue.updated_at) > 7) {
                await github.rest.issues.createComment({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  issue_number: issue.number,
                  body: 'This issue has been automatically closed due to inactivity. If this is still relevant, please open a new issue and reference this one.'
                });
                await github.rest.issues.update({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  issue_number: issue.number,
                  state: 'closed',
                  state_reason: 'not_planned'
                });
                console.log(`Closed issue #${issue.number} (autoclose > 7 days)`);
              }
            }

            // 3. triage + no activity > 30 days → add needs review, remove triage
            const triage = await getIssuesWithLabel('triage');
            for (const issue of triage) {
              if (daysSince(issue.updated_at) > 30) {
                await github.rest.issues.addLabels({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  issue_number: issue.number,
                  labels: ['needs review']
                });
                try {
                  await github.rest.issues.removeLabel({
                    owner: context.repo.owner,
                    repo: context.repo.repo,
                    issue_number: issue.number,
                    name: 'triage'
                  });
                } catch (e) {
                  if (e.status !== 404) throw e;
                }
                console.log(`Escalated issue #${issue.number} from triage to needs review (> 30 days)`);
              }
            }

            console.log('Sweep complete.');
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/sweep.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

- [ ] **Step 3: Commit lifecycle workflows**

```bash
git add .github/workflows/auto-close-duplicates.yml .github/workflows/lock-closed-issues.yml .github/workflows/issue-lifecycle-comment.yml .github/workflows/remove-autoclose-label.yml .github/workflows/sweep.yml
git commit -m "Add lifecycle management workflows

- auto-close-duplicates: daily close of issues labeled duplicate
- lock-closed-issues: lock closed issues after 7 days inactivity
- issue-lifecycle-comment: post comments on label changes
- remove-autoclose-label: remove autoclose on user activity
- sweep: enforce triage/needs-review/autoclose timeouts"
```

---

## Chunk 4: Security Workflow and Final Verification

### Task 16: Create Non-Write Users Check Workflow

**Files:**
- Create: `.github/workflows/non-write-users-check.yml`

- [ ] **Step 1: Write security check workflow**

Create `.github/workflows/non-write-users-check.yml` with this content:

```yaml
name: Non-write Users Check
on:
  pull_request:
    paths:
      - ".github/**"

permissions:
  contents: read
  pull-requests: write

jobs:
  allowed-non-write-check:
    runs-on: ubuntu-latest
    env:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    steps:
      - name: Check for allowed_non_write_users changes
        run: |
          DIFF=$(gh pr diff "$PR_NUMBER" -R "$REPO" || true)

          if ! echo "$DIFF" | grep -qE '^diff --git a/\.github/.*\.ya?ml'; then
            exit 0
          fi

          MATCHES=$(echo "$DIFF" | grep "^+.*allowed_non_write_users" || true)

          if [ -z "$MATCHES" ]; then
            exit 0
          fi

          EXISTING=$(gh pr view "$PR_NUMBER" -R "$REPO" --json comments --jq '.comments[].body' \
            | grep -c "<!-- non-write-users-check -->" || true)

          if [ "$EXISTING" -gt 0 ]; then
            exit 0
          fi

          BODY=$(cat <<'COMMENT'
          <!-- non-write-users-check -->
          **`allowed_non_write_users` detected**

          This PR adds or modifies `allowed_non_write_users`, which allows users without write access to trigger Claude Code Action workflows. This can introduce security risks.

          Please verify this change is intentional and review the security implications before merging.
          COMMENT
          )
          gh pr comment "$PR_NUMBER" -R "$REPO" --body "$BODY"
        env:
          PR_NUMBER: ${{ github.event.pull_request.number }}
          REPO: ${{ github.repository }}
```

- [ ] **Step 2: Validate YAML syntax**

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/non-write-users-check.yml'))" && echo "Valid YAML"
```

Expected: `Valid YAML`

- [ ] **Step 3: Commit security workflow**

```bash
git add .github/workflows/non-write-users-check.yml
git commit -m "Add non-write users security check for workflow PRs

Warns when PRs modify allowed_non_write_users in .github workflow files."
```

### Task 17: Clean Up and Final Verification

- [ ] **Step 1: Clean up .DS_Store files and update gitignore**

```bash
git rm --cached .github/.DS_Store 2>/dev/null || true
rm -f .github/.DS_Store
grep -qxF '.DS_Store' .gitignore || echo '.DS_Store' >> .gitignore
```

- [ ] **Step 2: Verify full file structure**

```bash
find .github -type f | sort
```

Expected output:
```
.github/ISSUE_TEMPLATE/bug_report.yml
.github/ISSUE_TEMPLATE/card-data.yml
.github/ISSUE_TEMPLATE/config.yml
.github/ISSUE_TEMPLATE/documentation.yml
.github/ISSUE_TEMPLATE/feature_request.yml
.github/workflows/auto-close-duplicates.yml
.github/workflows/claude-dedupe-issues.yml
.github/workflows/claude-issue-triage.yml
.github/workflows/claude.yml
.github/workflows/issue-lifecycle-comment.yml
.github/workflows/lock-closed-issues.yml
.github/workflows/non-write-users-check.yml
.github/workflows/remove-autoclose-label.yml
.github/workflows/sweep.yml
```

- [ ] **Step 3: Validate all YAML files**

```bash
for f in .github/ISSUE_TEMPLATE/*.yml .github/workflows/*.yml; do
  python3 -c "import yaml; yaml.safe_load(open('$f'))" && echo "OK: $f" || echo "FAIL: $f"
done
```

Expected: All files show `OK`.

- [ ] **Step 4: Commit cleanup**

```bash
git add .gitignore
git commit -m "Add .DS_Store to gitignore"
```

- [ ] **Step 5: Remind user to add ANTHROPIC_API_KEY secret**

The Claude-powered workflows require an `ANTHROPIC_API_KEY` secret. Add it at:
`https://github.com/rshelnutt/final-forge/settings/secrets/actions`

Without this secret, the triage, dedup, and @claude workflows will fail silently.
