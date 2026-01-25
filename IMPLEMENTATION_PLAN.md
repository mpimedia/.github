# Phase 3: Implementation Plan for Option 1

## Chosen Option

**Option 1: Extend Shared Standards Repository with Copilot Instructions**

Manager Approval: ✅ Approved by @wrburgess on 2026-01-25

---

## Implementation Plan

### Overview
Implement Copilot guidelines across 8 MPI Media repositories using the proven Git submodule pattern. This approach extends the existing `mpi-application-standards` repository with Copilot-specific instructions, maintaining consistency with the Claude setup.

### Step-by-Step Implementation

#### Step 1: Create Copilot Instructions in .github Repository (Prototype)
**Location:** This repository (`mpimedia/.github`)
**Purpose:** Create initial Copilot instructions as a reference/template
**Time:** 1 hour

**Files to create:**
1. `.github/copilot-instructions.md` - Organization-wide Copilot instructions prototype
   - Project overview and structure
   - Branch protection requirements (primary acceptance criteria)
   - Code standards and conventions
   - Build, test, and validation procedures
   - Security guidelines
   - Reference to shared standards (when implemented)

**Content Structure:**
```markdown
# MPI Media - GitHub Copilot Instructions

## Organization Overview
[Context about MPI Media projects]

## Branch Protection Requirements ⚠️
**CRITICAL:** All changes must be made in feature branches or pull requests.
- Never commit directly to `main`, `master`, or `develop` branches
- Always create a feature branch before making changes
- Use descriptive branch names (e.g., `feature/add-user-auth`, `fix/api-timeout`)

## Code Standards
[Shared coding conventions]

## Build & Test
[How to build and test projects]

## Security Guidelines
[Security best practices]

## Repository-Specific Instructions
Individual repositories may have additional instructions in their own .github/copilot-instructions.md
```

#### Step 2: Create Path-Specific Instructions (Optional)
**Location:** `.github/instructions/` directory
**Purpose:** Provide context for specific file types
**Time:** 30 minutes

**Files to create:**
1. `.github/instructions/tests.instructions.md` - Guidelines for test files
2. `.github/instructions/workflows.instructions.md` - GitHub Actions workflow guidelines

#### Step 3: Document Setup Process
**Location:** This repository
**Purpose:** Provide clear instructions for other repos to adopt
**Time:** 30 minutes

**Files to create/update:**
1. `docs/COPILOT_SETUP.md` - Comprehensive setup guide for other repositories
   - How to add Copilot instructions to a repository
   - How to reference shared standards (when available)
   - Examples and best practices
   - Troubleshooting common issues

2. Update `README.md` - Add section about Copilot instructions

#### Step 4: Future Work - Extend mpi-application-standards
**Location:** `mpi-application-standards` repository (separate PR)
**Purpose:** Create shared standards for all repos
**Time:** 1-2 hours (separate task)

**Note:** This step requires access to the `mpi-application-standards` repository and should be done as a separate PR. For now, we'll document the plan:

**Files to create in mpi-application-standards:**
1. `COPILOT.base.md` - Universal Copilot guidelines
2. `COPILOT.rails.md` - Rails-specific Copilot guidelines
3. `copilot/branch-protection.md` - Detailed branch protection rules
4. `copilot/security-standards.md` - Security best practices
5. `copilot/testing-guidelines.md` - Testing standards

#### Step 5: Rollout to Other Repositories
**Location:** 7 other repositories
**Purpose:** Apply Copilot instructions to all repos
**Time:** 2-3 hours (15-20 min per repo)

**Repositories:**
1. ✅ `.github` (this repo) - Template/prototype
2. ⏳ `optimus` - Has submodule, add Copilot instructions
3. ⏳ `avails` - Add submodule + Copilot instructions
4. ⏳ `sfa` - Add submodule + Copilot instructions
5. ⏳ `garden` - Add submodule + Copilot instructions
6. ⏳ `harvest` - Add submodule + Copilot instructions
7. ⏳ `mpi-application-workflows` - Add Copilot instructions
8. ⏳ `mpi-application-standards` - Add shared Copilot files

**Note:** Rollout to other repositories is outside the scope of this PR since we don't have access to those repositories in this sandbox. We'll create comprehensive documentation for the team to follow.

---

## Test Plan

### Validation Approach

Since Copilot instructions are consumed by GitHub Copilot (a closed-source AI service), traditional automated testing is not possible. Instead, we'll use the following validation approach:

#### 1. File Structure Validation
**Test:** Verify all files are created in correct locations
- ✅ `.github/copilot-instructions.md` exists
- ✅ `.github/instructions/*.instructions.md` exist
- ✅ Documentation files exist

**How:** Manual file system inspection + Git status check

#### 2. Markdown Syntax Validation
**Test:** Ensure all markdown files have valid syntax
- ✅ No broken links
- ✅ Valid markdown structure
- ✅ Code blocks properly formatted

**How:** Use markdown linter if available, or manual review

#### 3. Content Completeness Check
**Test:** Verify all required sections are present
- ✅ Branch protection requirements prominently documented
- ✅ Code standards included
- ✅ Build/test instructions provided
- ✅ Security guidelines included

**How:** Manual review against checklist

#### 4. GitHub Detection Test
**Test:** Verify GitHub recognizes the instructions
**How:** 
- Create a test issue in this repository
- Assign to @copilot
- Verify Copilot acknowledges the instructions
- Check that Copilot references branch protection requirements

**Note:** This requires GitHub Copilot access and is best done by the team.

#### 5. Documentation Review
**Test:** Ensure setup documentation is clear and actionable
- ✅ Setup guide is comprehensive
- ✅ Examples are provided
- ✅ Troubleshooting section included

**How:** Manual review + peer feedback

#### 6. Integration Test (Manual)
**Test:** Verify instructions work with Copilot in practice
**Steps:**
1. Create a test issue in a repository with instructions
2. Assign to @copilot
3. Observe Copilot's behavior
4. Verify it references branch protection
5. Verify it follows code standards

**Note:** Requires actual Copilot usage, best done post-merge.

---

## Acceptance Criteria Validation

### Criterion 1: Branch Protection
✅ **"Copilot restrictions for changes only made in non-main branch or PR should be maintained across all repos"**

**How this is addressed:**
- Branch protection requirements prominently documented at top of `copilot-instructions.md`
- Clear, bold warnings about never committing to main/master/develop
- Specific instructions on creating feature branches
- Examples of proper branch naming conventions

**Validation:**
- Manual review of documentation
- Test with Copilot to ensure it references these guidelines
- Document in setup guide for other repos

**Note:** Unlike Claude's technical enforcement via hooks, Copilot relies on documentation. The instructions are clear and prominent to maximize compliance.

### Criterion 2: Guidelines Availability
✅ **"Copilot guidelines, rules, and context should be available across all repos"**

**How this is addressed:**
- Prototype created in `.github` org repository
- Comprehensive setup documentation for other repos
- Plan documented for `mpi-application-standards` extension
- Clear rollout process for all 8 repositories

**Validation:**
- Files created in this repository (prototype)
- Documentation provides clear steps for other repos
- Setup guide tested for clarity and completeness

---

## Risk Mitigation

### Risk 1: Copilot Ignoring Instructions
**Mitigation:** 
- Use clear, prominent formatting for critical rules
- Place branch protection at the top of the file
- Use bold, warnings, and emojis to draw attention
- Test with actual Copilot behavior and iterate

### Risk 2: Instructions Becoming Outdated
**Mitigation:**
- Document update process in setup guide
- Link to this repository as source of truth
- Establish review cadence (quarterly?)
- Version the instructions for tracking

### Risk 3: Inconsistency Across Repos
**Mitigation:**
- Provide templates and examples
- Document shared standards plan
- Clear setup guide reduces variation
- Future: Implement submodule pattern when mpi-application-standards is extended

### Risk 4: Developer Confusion
**Mitigation:**
- Comprehensive documentation
- Clear examples
- Troubleshooting section
- Link to GitHub's official documentation

---

## Success Metrics

### Immediate Success (This PR)
- ✅ Copilot instructions file created in `.github` repository
- ✅ Path-specific instructions created
- ✅ Comprehensive documentation provided
- ✅ Setup guide is clear and actionable
- ✅ Both acceptance criteria addressed

### Short-term Success (Next 2-4 weeks)
- Instructions added to all 8 repositories
- Team feedback collected and incorporated
- Copilot demonstrably references branch protection
- No accidental commits to protected branches via Copilot

### Long-term Success (3-6 months)
- Shared standards in `mpi-application-standards` implemented
- Consistent Copilot behavior across all projects
- Regular updates to instructions based on learnings
- High developer satisfaction with Copilot guidance

---

## Timeline

| Step | Task | Duration | Status |
|------|------|----------|--------|
| 1 | Create copilot-instructions.md in .github | 1 hour | ⏳ Ready |
| 2 | Create path-specific instructions | 30 min | ⏳ Ready |
| 3 | Create setup documentation | 30 min | ⏳ Ready |
| 4 | Update README.md | 15 min | ⏳ Ready |
| 5 | Validation and review | 30 min | ⏳ Ready |
| **Total** | **Phase 3 Implementation** | **~3 hours** | ⏳ Ready |

**Post-PR Tasks (Separate PRs):**
- Extend `mpi-application-standards` with Copilot files (1-2 hours)
- Roll out to 7 other repositories (2-3 hours)
- Manual testing with Copilot (1 hour)

---

## Execution Checklist

Phase 3 execution tasks:

- [ ] Create `.github/copilot-instructions.md` with comprehensive guidelines
- [ ] Create `.github/instructions/tests.instructions.md`
- [ ] Create `.github/instructions/workflows.instructions.md`
- [ ] Create `docs/` directory if not exists
- [ ] Create `docs/COPILOT_SETUP.md` setup guide
- [ ] Update `README.md` with Copilot section
- [ ] Validate markdown syntax
- [ ] Review content completeness
- [ ] Verify acceptance criteria addressed
- [ ] Commit and push changes
- [ ] Update issue with Phase 3 completion

---

## Notes for Future Contributors

### Why This Approach?
- Maintains consistency with Claude setup pattern
- Provides immediate value (prototype in .github)
- Documents clear path for scaling to other repos
- Balances immediate needs with long-term maintainability

### What's Not Included?
- Actual rollout to 7 other repositories (requires separate PRs)
- Extension of `mpi-application-standards` (requires access to that repo)
- Automated testing (not possible with Copilot's architecture)
- Technical enforcement of branch protection (Copilot limitation)

### Future Enhancements?
- Add more path-specific instructions as patterns emerge
- Create organization-level custom instructions (when GitHub supports it)
- Develop best practices library based on actual usage
- Consider automation tools for syncing instructions

### Questions or Issues?
Contact @wrburgess or the DevOps team for guidance on:
- Adding instructions to other repositories
- Updating shared standards
- Troubleshooting Copilot behavior
- Requesting new guidelines or patterns
