# Session Handoff Report

**Session ID:** `session_017UKdvB9s6XhYBtsQLvkw6w`
**Date:** 2026-03-01
**Environment:** Claude Code Web (claude.ai/code)
**Repo:** `mpimedia/.github` (org-wide config repo)
**Branch:** `claude/design-system-setup-QpJSm`

---

## What We Were Working On

The user (wrburgess) wants to build out an **MPI Design System** (`mpimedia/mpi-design-system` repo). During this session we discussed project management tooling and started setting up infrastructure.

---

## Decisions Made

### 1. GitHub Project Board — APPROVED, NOT YET CREATED

The user agreed that a GitHub Project makes sense for tracking design system work. He wants **Claude to create the project** (not do it manually).

**Agreed-upon board structure:**

| Column | Purpose |
|---|---|
| **Backlog** | Design needs identified but not yet started |
| **Proposal** | Issue filed with requirements, waiting for design work |
| **In Design** | Badie + Claude actively working on it |
| **In Review** | Design shared, awaiting stakeholder feedback |
| **Approved** | Design accepted, ready for implementation |
| **Implementing** | Being built as ViewComponent / ERB in a Rails app |
| **Done** | Implemented, merged, and live |

**Scope:** Org-wide (not just mpi-design-system) — should be able to pull issues from Rails app repos too.

**Why it wasn't created yet:** The web environment doesn't have `gh` CLI auth to make GitHub API calls for Projects v2. The TUI session (with local `gh` auth) can do this.

### 2. Design System Context (from prior sessions)

The design system involves:
- **Badie** proposing designs
- **wrburgess** reviewing/approving
- **Claude** generating artifacts (components, specs)
- A pipeline: propose → design → review → accept → implement
- Implementation target: ViewComponent / ERB in Rails apps
- Apps that consume the system: **Optimus, Avails, SFA, Garden, Harvest**

---

## Immediate Next Actions (for TUI session)

### Action 1: Create the GitHub Project

```bash
# Create org-level project
gh project create --owner mpimedia --title "MPI Design System"

# Then configure the Status field with custom columns.
# Get project number from output above, then:
PROJECT_NUM=<number>

# Get project and field IDs
PROJECT_ID=$(gh project list --owner mpimedia --format json | jq -r '.projects[] | select(.title=="MPI Design System") | .id')
FIELD_ID=$(gh project field-list $PROJECT_NUM --owner mpimedia --format json | jq -r '.fields[] | select(.name=="Status") | .id')

# Set custom status options
gh api graphql -f query='
mutation {
  updateProjectV2Field(input: {
    projectId: "'"$PROJECT_ID"'"
    fieldId: "'"$FIELD_ID"'"
    singleSelectOptions: [
      {name: "Backlog", color: "GRAY"},
      {name: "Proposal", color: "BLUE"},
      {name: "In Design", color: "PURPLE"},
      {name: "In Review", color: "YELLOW"},
      {name: "Approved", color: "GREEN"},
      {name: "Implementing", color: "ORANGE"},
      {name: "Done", color: "GREEN"}
    ]
  }) {
    projectV2Field { ... on ProjectV2SingleSelectField { name options { name } } }
  }
}'
```

### Action 2: Set up the `mpi-design-system` repo

The repo exists at `https://github.com/mpimedia/mpi-design-system` but was not accessible from this session's proxy. The TUI session should clone it and begin scaffolding.

---

## Current Repo State (mpimedia/.github)

This is the **org-wide GitHub config repo**, not the design system repo. It contains:

- **Issue templates:** bug, feature, task, question
- **PR template:** standardized format
- **Copilot instructions:** org-wide + path-specific (tests, workflows)
- **CI workflow templates:** Rails CI with RSpec, RuboCop, Brakeman, Heroku deploy
- **Org profile:** README visible at github.com/mpimedia
- **Claude hooks:** Branch protection enforcement (prevents commits to main/master/develop)

### Key org standards (relevant to design system work):
- **Tech stack:** Ruby on Rails, PostgreSQL, Redis, Sidekiq, RSpec
- **Testing:** 80%+ coverage, RSpec, FactoryBot
- **Code style:** Rails conventions, 2-space indent, 120-char line limit
- **Branch protection:** Feature branches only, conventional commits
- **AI attribution:** Required `Co-Authored-By` on all AI-generated commits
- **Security:** No secrets in code, input validation, Brakeman scans

### Branch info:
- Currently on `claude/design-system-setup-QpJSm`
- No uncommitted changes from this session
- Default branch is `main`

---

## What Was NOT Done

- [ ] GitHub Project not created (no API auth in web env)
- [ ] `mpi-design-system` repo not accessed (proxy only allows `.github` repo)
- [ ] No design system scaffolding started
- [ ] No issues created yet

---

## People & Roles

| Person | Role |
|---|---|
| **wrburgess** | Manager, reviewer, approver |
| **Badie** | Designer, proposes designs |
| **Claude** | Generates artifacts, specs, components |

---

## How to Use This Report

Start a new Claude Code session locally:

```bash
cd path/to/mpi-design-system
claude
```

Then paste or reference this file to provide context. The key ask is:

> "Create an org-level GitHub Project called 'MPI Design System' with columns: Backlog, Proposal, In Design, In Review, Approved, Implementing, Done. Then help me scaffold the mpi-design-system repo."
