# Copilot Setup Guide for MPI Media Repositories

This guide explains how to add GitHub Copilot instructions to MPI Media repositories to ensure consistent AI-assisted development across all projects.

## Table of Contents

1. [Quick Start](#quick-start)
2. [Understanding Copilot Instructions](#understanding-copilot-instructions)
3. [Step-by-Step Setup](#step-by-step-setup)
4. [Customizing Instructions](#customizing-instructions)
5. [Testing Your Setup](#testing-your-setup)
6. [Troubleshooting](#troubleshooting)
7. [Best Practices](#best-practices)

---

## Quick Start

For a new MPI Media repository:

```bash
# 1. Create the instructions file
mkdir -p .github/instructions
touch .github/copilot-instructions.md

# 2. Copy the base template from .github repository
curl -o .github/copilot-instructions.md \
  https://raw.githubusercontent.com/mpimedia/.github/main/.github/copilot-instructions.md

# 3. Customize for your repository (see Customizing Instructions section)

# 4. Commit and push
git add .github/
git commit -m "Add Copilot instructions"
git push
```

---

## Understanding Copilot Instructions

### What Are Copilot Instructions?

GitHub Copilot custom instructions are markdown files that guide Copilot's behavior when working in your repository. They help Copilot:

- Follow your coding standards and conventions
- Understand your project structure and architecture
- Apply security best practices specific to your org
- Know how to build, test, and deploy your code
- Respect branch protection and workflow requirements

### Types of Instruction Files

GitHub Copilot supports several instruction file formats:

1. **Repository-wide instructions** (primary)
   - Location: `.github/copilot-instructions.md`
   - Applies to all files in the repository
   - Contains general guidelines, standards, and workflows

2. **Path-specific instructions**
   - Location: `.github/instructions/*.instructions.md`
   - Applies to specific file types or directories
   - Uses `applyTo` frontmatter to specify glob patterns
   - Example: Test files, API endpoints, React components

3. **Agent-specific instructions**
   - Location: `AGENTS.md` (any directory)
   - Used by Copilot coding agents
   - Copilot prioritizes the nearest `AGENTS.md` in folder hierarchy

---

## Step-by-Step Setup

### For Repositories WITHOUT Copilot Instructions

#### Step 1: Create the Base Instructions File

```bash
# Create the directory structure
mkdir -p .github/instructions

# Create the main instructions file
touch .github/copilot-instructions.md
```

#### Step 2: Copy the MPI Media Template

```bash
# Download the template
curl -o .github/copilot-instructions.md \
  https://raw.githubusercontent.com/mpimedia/.github/main/.github/copilot-instructions.md
```

#### Step 3: Customize for Your Repository

Edit `.github/copilot-instructions.md` to add repository-specific information.

#### Step 4: Commit and Push

```bash
git add .github/
git commit -m "Add GitHub Copilot instructions"
git push origin your-branch-name
```

---

## Resources

### Internal Resources
- [MPI Application Standards](https://github.com/mpimedia/mpi-application-standards)
- [MPI Application Workflows](https://github.com/mpimedia/mpi-application-workflows)
- [.github Repository](https://github.com/mpimedia/.github)

### GitHub Documentation
- [Copilot Custom Instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot)
- [Copilot Coding Agent](https://docs.github.com/en/copilot/using-github-copilot/using-github-copilot-code-review)

---

**Document Version:** 1.0.0  
**Last Updated:** 2026-01-25  
**Maintained by:** MPI Media DevOps Team
