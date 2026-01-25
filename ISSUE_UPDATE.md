# Issue Update - To be posted to GitHub Issue

## Phase 1: Analysis

### ✅ Tasks Completed
- [x] **Review requirements and explore codebase**
- [x] **Edit this issue to document your understanding in the section below**

---

#### Issue Analysis Summary

**Purpose and Expected Outcome:**

This task establishes repository rules, standards, and processes for GitHub Copilot across the MPI Media organization, mirroring the successful setup completed for @claude and @github in Issue #75. The goal is to ensure consistent Copilot behavior, enforce best practices (especially branch protection), and provide comprehensive context across all 8 repositories.

**Affected Components, Files, and Systems:**

**Current State Analysis:**

1. **`.github` Repository (this repo):**
   - ✅ Contains organization-wide issue templates (bug_report, feature_request, task)
   - ✅ Has `.claude/` directory with settings.json and branch protection hook
   - ✅ Provides workflow templates for Rails CI/CD
   - ❌ No Copilot-specific configuration currently exists

2. **Eight Target Repositories:**
   - **5 Applications:** Optimus, Avails, SFA, Garden, Harvest
   - **3 Configuration:** mpi-applications-workflows, mpi-application-standards, .github

3. **Existing Claude Setup Pattern:**
   - `.claude/settings.json` - Configures pre-tool-use hooks for branch protection
   - `.claude/hooks/enforce-branch-creation.sh` - Bash script prevents changes on main/master/develop
   - Successfully implemented via Git submodule pattern (`mpi-application-standards`)
   - Optimus already uses `.mpi-standards/` submodule with `CLAUDE.base.md` and `CLAUDE.rails.md`

**GitHub Copilot Capabilities Discovered:**

Copilot supports multiple instruction file formats (per documentation):
- `/.github/copilot-instructions.md` - Repository-wide instructions (PRIMARY)
- `/.github/instructions/**/*.instructions.md` - Path-specific rules (e.g., for tests, components)
- `**/AGENTS.md` - Agent-specific automations
- `/CLAUDE.md`, `/GEMINI.md` - Legacy formats also read by Copilot

**Dependencies and Integration Points:**

1. **mpi-application-standards** repository (already exists)
   - Currently contains Claude-specific standards
   - Can be extended to include Copilot instructions
   - Proven Git submodule distribution pattern

2. **Organization Structure:**
   - `.github` repository provides defaults to all org repos (templates only)
   - ⚠️ **Limitation:** GitHub doesn't yet support org-wide `copilot-instructions.md` in `.github` repo
   - Individual repos must have their own `.github/copilot-instructions.md`

**Acceptance Criteria Mapping:**

1. ✅ **Branch restrictions:** Copilot must only make changes in non-main branches
   - **Solution:** Document branch protection requirements prominently in instructions
   - **Note:** Unlike Claude, Copilot has no pre-execution hooks; relies on documentation and developer discipline

2. ✅ **Guidelines availability:** Guidelines must be available across all 8 repos
   - **Solution:** Use submodule pattern to share core instructions
   - Each repo gets `.github/copilot-instructions.md` that references shared standards

**Potential Risks or Complications:**

1. **No Native Hook System:** Copilot doesn't have Claude's pre-execution hooks for enforcing branch protection
2. **Manual Enforcement:** Branch protection must be documented and followed by developers (no technical barrier)
3. **Duplication Required:** Each of 8 repos needs local `.github/copilot-instructions.md` (GitHub limitation)
4. **Submodule Learning Curve:** Some developers may be unfamiliar with Git submodules
5. **Sync Responsibility:** Teams must remember to `git submodule update --remote` for latest standards
6. **Import Syntax:** Copilot doesn't natively support `@import`, may need concatenation or clear references

**Related Issues or Tasks Discovered:**

- Optimus Issue #75: Successfully implemented shared standards for Claude/GitHub using submodules
- GitHub Feature Request: Community request for org-wide copilot-instructions.md support (pending)

---

## Phase 2: Solution Options

### ✅ Tasks Completed
- [x] **Develop 1-3 approaches for completing the task**
- [x] **Edit this issue to document each option with cost/benefit analysis below**

---

#### Option 1: Extend Shared Standards Repository with Copilot Instructions

**Description:**

Leverage the existing `mpi-application-standards` Git submodule pattern (already proven with Claude). Add Copilot-specific instruction files to the shared standards repository, and have each application repository reference them via their local `.github/copilot-instructions.md`.

**Structure:**
```
mpi-application-standards/ (submodule in each repo)
├── CLAUDE.base.md               (existing)
├── CLAUDE.rails.md              (existing)
├── COPILOT.base.md              (NEW - universal Copilot standards)
├── COPILOT.rails.md             (NEW - Rails-specific for Copilot)
└── copilot/                     (NEW - modular instructions)
    ├── branch-protection.md
    ├── security-standards.md
    └── testing-guidelines.md

Each repo's .github/copilot-instructions.md:
# MPI Media Copilot Instructions

## Shared Standards
See `.mpi-standards/COPILOT.base.md` for universal guidelines
See `.mpi-standards/COPILOT.rails.md` for Rails-specific patterns

## Repository-Specific Guidelines
[Local customizations here]
```

**Benefits:**
- ✅ **Consistency:** Matches proven Claude setup pattern (developer familiarity)
- ✅ **Single source of truth:** Shared standards in one repository
- ✅ **Easy propagation:** `git submodule update --remote` distributes changes
- ✅ **Minimal disruption:** Leverages existing infrastructure (Optimus already has submodule)
- ✅ **Flexibility:** Supports both shared and repo-specific customizations
- ✅ **Version control:** All changes tracked and auditable
- ✅ **Progressive adoption:** Can implement in Optimus first, then roll out

**Costs/Risks:**
- ❌ **Submodule complexity:** Requires adding submodule to 7 repos (if not already present)
- ❌ **Manual updates:** Developers must remember to update submodules
- ❌ **Learning curve:** Contributors unfamiliar with submodules need guidance
- ❌ **Duplication:** Each repo still needs local `.github/copilot-instructions.md` (GitHub limitation)
- ❌ **No import syntax:** Copilot doesn't natively support `@import` (must reference or concatenate)

**Estimated Scope:**
- **Complexity:** Medium
- **Files to create:**
  - In `mpi-application-standards`: 2-4 new files (COPILOT.base.md, COPILOT.rails.md, modular files)
  - In each of 8 repos: 1 new file (`.github/copilot-instructions.md`)
  - Optimus: Minimal changes (already has submodule)
  - Other 7 repos: Add submodule if missing
- **Time estimate:** 3-4 hours (2 hrs for content creation, 1-2 hrs for repo setup)
- **Maintenance:** Low (centralized updates, one-time submodule setup per repo)

---

#### Option 2: Centralized .github Repository with Template Distribution

**Description:**

Create comprehensive Copilot instruction templates in this `.github` organization repository. Provide documentation and optional scripts to help teams copy templates to individual repos. Teams manually copy and customize templates.

**Structure:**
```
.github/ (this repository)
├── copilot-templates/
│   ├── copilot-instructions.md.template    # Base template for all repos
│   ├── rails-app.copilot-instructions.md   # Rails-specific template
│   └── instructions/
│       ├── tests.instructions.md
│       └── api.instructions.md
├── scripts/
│   └── setup-copilot-instructions.sh       # Helper script for copying
└── docs/
    └── COPILOT_SETUP_GUIDE.md             # Comprehensive setup documentation
```

**Benefits:**
- ✅ **Simple mental model:** No submodules, just copy templates
- ✅ **Clear organization:** All templates in one place
- ✅ **Easy discovery:** New repos can find templates in .github
- ✅ **No submodule complexity:** Easier for Git beginners
- ✅ **Setup scripts:** Can automate initial copying
- ✅ **Documentation co-located:** Templates and docs together

**Costs/Risks:**
- ❌ **No automatic sync:** Changes don't propagate automatically
- ❌ **Drift risk:** Instructions can become inconsistent across repos over time
- ❌ **Manual propagation:** Must update 8+ repos manually for each change
- ❌ **Discipline required:** Teams must check for template updates regularly
- ❌ **High maintenance:** Burden on maintainers to sync changes
- ❌ **Version skew:** Hard to know which repos have latest version
- ❌ **No GitHub auto-apply:** .github repo doesn't auto-apply Copilot instructions to other repos

**Estimated Scope:**
- **Complexity:** Low-Medium (simple structure, high coordination)
- **Files to create:**
  - In `.github` repo: 4-6 template files + setup script + documentation (2-3 hours)
  - In each of 8 repos: Copy and customize template (15-30 min per repo)
- **Time estimate:** 4-6 hours initial (includes setup and first rollout)
- **Maintenance:** High (must manually propagate updates or build automation)

---

#### Option 3: Hybrid Approach with Shared Core + Local Overrides

**Description:**

Combine both approaches: use `mpi-application-standards` submodule for core universal guidelines, and allow each repository to supplement with local customizations. Provide tooling (concatenation script or symlinks) to merge shared and local files seamlessly.

**Structure:**
```
mpi-application-standards/ (submodule)
├── copilot/
│   ├── 00-base.md              # Universal rules (numbered for order)
│   ├── 01-security.md          # Security guidelines
│   ├── 02-branch-protection.md # Workflow requirements
│   └── 10-rails.md             # Rails-specific (optional)

Each repo:
.github/
├── copilot-instructions.md            # Generated/concatenated file
├── copilot-local/
│   └── 90-repo-specific.md            # Local overrides
└── scripts/
    └── build-copilot-instructions.sh  # Concatenates shared + local
```

**Benefits:**
- ✅ **Best of both worlds:** Shared core + local flexibility
- ✅ **Clear separation:** Obvious what's shared vs. custom
- ✅ **Version-controlled core:** Shared standards tracked in submodule
- ✅ **Local flexibility:** Teams can add repo-specific rules without touching shared standards
- ✅ **Automated merging:** Script handles concatenation
- ✅ **Progressive adoption:** Can start with shared, add local as needed

**Costs/Risks:**
- ❌ **Highest complexity:** Most moving parts and concepts to understand
- ❌ **Tooling required:** Need build/concatenation script that works across platforms
- ❌ **Documentation burden:** Must explain system clearly
- ❌ **Potential confusion:** Which file to edit when? Where do changes go?
- ❌ **Platform issues:** Symlinks don't work reliably on Windows
- ❌ **Build step required:** Must run script to generate final file
- ❌ **Debugging difficulty:** Harder to troubleshoot when files are merged

**Estimated Scope:**
- **Complexity:** High (requires tooling, documentation, training)
- **Files to create:**
  - In `mpi-application-standards`: 4-6 modular files (2 hours)
  - In `.github` repo: Setup/build scripts, comprehensive documentation (2-3 hours)
  - In each of 8 repos: Local override structure + run setup (30-45 min per repo)
- **Time estimate:** 6-8 hours initial setup (includes tooling development and testing)
- **Maintenance:** Medium (automated merging, but tooling needs maintenance)

---

#### Recommendation

**✅ RECOMMENDED: Option 1 - Extend Shared Standards Repository**

**Justification:**

1. **Proven Pattern:**
   - Optimus already successfully uses `mpi-application-standards` submodule for Claude
   - Developers are familiar with this pattern
   - Infrastructure and processes already established

2. **Maintainability:**
   - Single source of truth for shared standards
   - Changes propagate via Git submodules (standard Git feature)
   - Version-controlled, auditable updates

3. **Balanced Complexity:**
   - Medium complexity vs. High (Option 3)
   - Sustainable maintenance vs. High burden (Option 2)
   - Familiar tools (Git) vs. custom scripts

4. **Meets Acceptance Criteria:**
   - ✅ Branch protection: Documented prominently in shared instructions
   - ✅ Guidelines availability: Shared via submodule across all 8 repos
   - ✅ Consistency: Single source prevents drift

5. **Practical Implementation:**
   - Incremental rollout possible (Optimus first)
   - Minimal disruption to existing workflows
   - Local customization still supported

6. **Long-term Sustainability:**
   - Standard Git feature (submodules), not custom tooling
   - Easy onboarding: "Clone with --recurse-submodules"
   - Clear ownership: Standards team maintains shared files

**Why Not Other Options:**

- **Option 2 (Templates):** High risk of inconsistency and drift; requires manual synchronization across 8 repos for every update; high maintenance burden
- **Option 3 (Hybrid):** Over-engineered for current needs; adds unnecessary complexity; requires custom tooling that must be maintained

---

## Phase 3: Planning (after manager approval)

⏸️ **Status:** Awaiting manager approval on Option 1

**Next Steps:**
1. Manager reviews Phase 1 & 2 analysis
2. Manager approves Option 1 (or requests changes)
3. I will then document the detailed implementation plan in Phase 3
4. Execute the plan after plan approval

---

## Phase 4: Execution Synopsis (after implementation)

⏸️ **Status:** Not started (pending Phase 3 approval)

---

**Note:** This analysis document is comprehensive and ready for manager review. Please provide approval to proceed with Option 1, or let me know if you'd like adjustments to the recommendation.
