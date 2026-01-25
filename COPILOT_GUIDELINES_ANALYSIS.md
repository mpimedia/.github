# Copilot Guidelines Task - Complete Analysis

## Phase 1: Analysis

### Issue Analysis Summary

**Purpose and Expected Outcome:**
This task aims to establish repository rules, standards, and processes for GitHub Copilot across the MPI Media organization, similar to what was previously done for @claude and @github. The goal is to ensure consistent Copilot behavior, enforce best practices, and provide context across all 8 repositories (5 applications + 3 configuration repos).

**Background Context:**
- Similar work was completed for Claude Code and GitHub (Issue #75 in Optimus)
- Solution used Git submodules to share standards via `mpi-application-standards`
- Claude uses `.claude/settings.json` with branch protection hooks
- Organization uses `.github` repo for templates and standards

**Affected Components, Files, and Systems:**

**Current State:**
1. **`.github` Repository (this repo):**
   - Contains organization-wide issue templates
   - Has `.claude/` directory with settings and hooks
   - Provides workflow templates for CI/CD
   - No Copilot-specific configuration currently exists

2. **Eight Target Repositories:**
   - **5 Applications:** Optimus, Avails, SFA, Garden, Harvest
   - **3 Configuration:** mpi-applications-workflows, mpi-application-standards, .github

3. **Existing Claude Setup:**
   - `.claude/settings.json` - Configures pre-tool-use hooks
   - `.claude/hooks/enforce-branch-creation.sh` - Prevents changes on main/master/develop
   - Branch protection enforced at tool level

**Dependencies and Integration Points:**

1. **GitHub Copilot Documentation:**
   - Supports multiple instruction file formats:
     - `/.github/copilot-instructions.md` (repository-wide)
     - `/.github/instructions/**/*.instructions.md` (path-specific)
     - `**/AGENTS.md` (agent-specific)
     - `/CLAUDE.md` (legacy, also read by Copilot)
     - `/GEMINI.md` (legacy, also read by Copilot)

2. **Shared Standards Repository:**
   - `mpi-application-standards` already exists for Claude
   - Currently contains `CLAUDE.base.md`, `CLAUDE.rails.md`
   - Could be extended to include Copilot instructions

3. **Organization Structure:**
   - `.github` repository provides defaults to all org repos
   - Individual repos can override with local configurations
   - Submodule pattern already proven with Claude setup

**Acceptance Criteria Requirements:**

1. **Branch Restrictions:** Copilot must only make changes in non-main branches or PRs
2. **Guidelines Availability:** Copilot guidelines, rules, and context must be available across all repos

**Potential Risks or Complications:**

1. **Format Differences:** Copilot instruction format differs from Claude's hooks-based approach
2. **No Native Hook System:** Copilot doesn't have pre-execution hooks like Claude
3. **Organization Limitations:** GitHub doesn't yet support org-wide `copilot-instructions.md` in `.github` repo
4. **Duplication Challenge:** May need to replicate instructions across multiple repos
5. **Submodule Complexity:** Adding another submodule layer could complicate maintenance
6. **Documentation Overlap:** Need to coordinate with existing `CLAUDE.md` files
7. **Testing Limitations:** Cannot easily test Copilot's behavior without manual validation

**Related Issues or Tasks Discovered:**

1. Optimus Issue #75 - Original implementation of shared standards
2. GitHub Feature Request for org-wide copilot-instructions.md support
3. Need to understand how mpi-application-standards is currently structured

---

## Phase 2: Solution Options

### Option 1: Extend Shared Standards Repository with Copilot Instructions

**Description:**
Leverage the existing `mpi-application-standards` Git submodule pattern to add Copilot-specific instructions. Each application repository would:
- Continue using `.mpi-standards/` submodule (already exists in Optimus)
- Add `.github/copilot-instructions.md` that imports/references shared standards
- Maintain consistency with the Claude setup pattern

Structure:
```
mpi-application-standards/
├── CLAUDE.base.md
├── CLAUDE.rails.md
├── COPILOT.base.md          # New: Universal Copilot standards
├── COPILOT.rails.md         # New: Rails-specific for Copilot
└── copilot-instructions/    # New: Path-specific instructions
    ├── tests.instructions.md
    └── components.instructions.md
```

Each repo's `.github/copilot-instructions.md`:
```markdown
<!-- Import shared base standards -->
@.mpi-standards/COPILOT.base.md
@.mpi-standards/COPILOT.rails.md

## Repository-Specific Guidelines
[Local customizations here]
```

**Benefits:**
- ✅ Consistent with existing Claude pattern (developer familiarity)
- ✅ Single source of truth for shared standards
- ✅ Easy updates: `git submodule update --remote` propagates changes
- ✅ Minimal disruption to existing workflows
- ✅ Supports both shared and repo-specific customizations
- ✅ Leverages proven infrastructure
- ✅ Version-controlled, trackable changes
- ✅ Works with existing Optimus setup

**Costs/Risks:**
- ❌ Requires adding submodule to 7 additional repos (if not already present)
- ❌ Copilot may not natively support `@import` syntax (would need symlinks or script)
- ❌ Developers must remember to update submodules
- ❌ Adds Git complexity for contributors unfamiliar with submodules
- ❌ Each repo still needs local `.github/copilot-instructions.md` file
- ❌ Cannot leverage org-wide `.github` repository defaults (GitHub limitation)

**Estimated Scope:**
- **Complexity:** Medium
- **Files to create/modify:** 
  - In `mpi-application-standards`: 2-3 new files (COPILOT.base.md, COPILOT.rails.md)
  - In each of 8 repos: 1 new file (`.github/copilot-instructions.md`)
  - In each repo without submodule: add submodule reference
- **Time estimate:** 2-4 hours
- **Maintenance:** Low (centralized updates)

---

### Option 2: Centralized .github Repository with Template Distribution

**Description:**
Create comprehensive Copilot instructions in this `.github` organization repository and provide templates/scripts for copying them to individual repos. Use GitHub Actions or documentation to help teams keep instructions synchronized.

Structure:
```
.github/ (this repo)
├── .github/
│   └── copilot-instructions.md      # Template for all repos
├── copilot-templates/
│   ├── base.copilot-instructions.md
│   ├── rails.copilot-instructions.md
│   └── instructions/
│       ├── tests.instructions.md
│       └── api.instructions.md
├── scripts/
│   └── sync-copilot-instructions.sh # Helper script
└── docs/
    └── COPILOT_SETUP.md            # Setup guide
```

**Benefits:**
- ✅ Clear separation of concerns (templates live in .github)
- ✅ No submodule complexity for contributors
- ✅ Can provide setup scripts for automation
- ✅ Documentation and templates in one place
- ✅ Easier for new repos to adopt (copy template)
- ✅ Branch protection can be documented in instructions
- ✅ Simpler mental model for developers

**Costs/Risks:**
- ❌ No automatic synchronization (manual copy required)
- ❌ Instructions can drift between repos over time
- ❌ Harder to propagate updates to all repos simultaneously
- ❌ Risk of inconsistency across organization
- ❌ Requires discipline to keep templates updated
- ❌ GitHub doesn't auto-apply .github repo Copilot instructions to other repos
- ❌ More maintenance burden (must update 8+ repos manually)

**Estimated Scope:**
- **Complexity:** Low-Medium
- **Files to create:**
  - In `.github` repo: 3-5 template files + setup script + documentation
  - In each of 8 repos: Copy template files (1-2 files each)
- **Time estimate:** 3-5 hours initial, ongoing manual updates
- **Maintenance:** High (must sync manually or with automation)

---

### Option 3: Hybrid Approach with Shared Core + Repo-Specific Overrides

**Description:**
Combine the best of both approaches: use `mpi-application-standards` for core, universal guidelines, and allow each repository to supplement with local customizations. Provide clear documentation and tooling to make this seamless.

Structure:
```
mpi-application-standards/ (submodule)
├── copilot/
│   ├── base-instructions.md      # Universal rules
│   ├── rails-instructions.md     # Rails-specific
│   ├── security-rules.md         # Security guidelines
│   └── branch-protection.md      # Workflow requirements

Each repo:
.github/
├── copilot-instructions.md       # Imports from submodule + local rules
└── instructions/
    └── [local-specific].instructions.md

With setup script to create symlinks:
.mpi-standards/copilot/ -> .github/copilot-base/
```

**Benefits:**
- ✅ Best of both worlds: shared core + local flexibility
- ✅ Clear separation: what's shared vs. what's custom
- ✅ Version-controlled shared standards
- ✅ Each team can customize without breaking shared rules
- ✅ Symbolic links or concatenation scripts can merge files
- ✅ Progressive adoption: start with shared, customize as needed
- ✅ Can implement branch protection in documentation

**Costs/Risks:**
- ❌ Most complex option to understand and maintain
- ❌ Requires documentation and tooling to manage
- ❌ Potential for confusion about which file to edit
- ❌ Symlinks may not work across all platforms (Windows)
- ❌ Need build step to combine files
- ❌ More moving parts = more things to break

**Estimated Scope:**
- **Complexity:** High
- **Files to create:**
  - In `mpi-application-standards`: 4-5 modular files
  - In `.github` repo: Setup script, documentation, guidelines
  - In each of 8 repos: Local override file + setup execution
- **Time estimate:** 4-6 hours initial setup, tooling development
- **Maintenance:** Medium (automated merging but requires tooling)

---

## Recommendation

**Recommended Option: Option 1 - Extend Shared Standards Repository**

**Justification:**

1. **Consistency with Existing Patterns:**
   - Optimus already uses `mpi-application-standards` submodule successfully
   - Developers are familiar with this pattern from Claude setup
   - Maintains architectural consistency across the organization

2. **Proven Infrastructure:**
   - Git submodules are a known solution
   - The pattern worked for Claude, GitHub workflows, and templates
   - Reduces learning curve for contributors

3. **Maintainability:**
   - Single source of truth for shared Copilot standards
   - Changes propagate via `git submodule update --remote`
   - Version-controlled, auditable changes

4. **Balanced Complexity:**
   - Medium complexity vs. High (Option 3) or High maintenance (Option 2)
   - Addresses both acceptance criteria:
     - Branch protection documented in instructions
     - Guidelines available across all repos through submodule

5. **Practical Implementation:**
   - Can be implemented incrementally
   - Optimus already has submodule, needs only new Copilot files
   - Other 7 repos can adopt progressively
   - Local overrides still possible in each repo's `.github/copilot-instructions.md`

**Why Not Other Options:**

- **Option 2:** Risk of drift and inconsistency; high manual maintenance burden
- **Option 3:** Over-engineered for current needs; unnecessary complexity; tooling overhead

**Implementation Notes:**

Since Copilot doesn't support `@import` syntax natively, we'll:
1. Create comprehensive `COPILOT.base.md` and `COPILOT.rails.md` in submodule
2. Each repo's `.github/copilot-instructions.md` will either:
   - Use relative links to reference submodule content
   - Or include a build step to concatenate files (if needed)
   - Or document that developers should read both sources

3. Document branch protection requirements prominently in instructions
4. Provide clear setup guide in `mpi-application-standards` README

---

## Next Steps (Pending Manager Approval)

1. Review this analysis with stakeholders
2. Get approval on Option 1 (Recommended)
3. Move to Phase 3: Create detailed implementation plan
4. Execute the plan across all 8 repositories
