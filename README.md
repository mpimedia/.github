# MPI Media

Organization-wide GitHub defaults for MPI Media projects.

## What This Repository Provides

This special `.github` repository provides default configurations that apply to all repositories in the MPI Media organization:

### Issue Templates

All MPI Media repositories automatically inherit these issue templates:

- **Feature Request** - For new features or enhancements
- **Bug Report** - For reporting bugs or unexpected behavior
- **Task** - For general tasks, refactoring, or maintenance work

### Pull Request Template

A standardized PR template that ensures:
- Clear summary of changes
- Technical approach documentation
- Testing details
- Context for future contributors

### GitHub Copilot Instructions

Organization-wide Copilot custom instructions that guide AI-assisted development:
- `.github/copilot-instructions.md` - Repository-wide guidelines for Copilot
- `.github/instructions/*.instructions.md` - Path-specific instructions for tests, workflows, etc.
- Branch protection requirements and coding standards
- Build, test, and deployment procedures

See [docs/COPILOT_SETUP.md](docs/COPILOT_SETUP.md) for setup instructions.

## Related Repositories

- **[mpi-application-standards](https://github.com/mpimedia/mpi-application-standards)** - Shared Claude Code and development standards
- **[mpi-application-workflows](https://github.com/mpimedia/mpi-application-workflows)** - Reusable GitHub Actions workflows

## Projects

- **Optimus** - Rails application template and pattern source
- **Avails** - Central data repository
- **SFA** - Video clip hosting and search
- **Garden** - Static site generator
- **Harvest** - Ecommerce platform
