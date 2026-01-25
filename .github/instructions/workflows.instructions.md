---
applyTo: ".github/workflows/*.yml,.github/workflows/*.yaml"
---

# GitHub Actions Workflow Guidelines

These instructions apply to all GitHub Actions workflow files in `.github/workflows/`.

## Workflow Structure

### Basic Structure
```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: .ruby-version
          bundler-cache: true
      
      - name: Run tests
        run: bundle exec rspec
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
```

## Reusable Workflows

### Use Organization Workflows
MPI Media provides reusable workflows in the `mpi-application-workflows` repository:

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  ci:
    uses: mpimedia/mpi-application-workflows/.github/workflows/ci-rails.yml@main
    with:
      ruby-version: '3.2'
    secrets: inherit
```

### Available Reusable Workflows
- `ci-rails.yml` - Full CI pipeline (security, lint, test)
- `update-gems.yml` - Automated gem updates
- `update-packages.yml` - Automated package updates
- `check-indexes.yml` - Migration index checking

## Security Best Practices

### Never Hardcode Secrets
```yaml
# ❌ Bad: Hardcoded secrets
env:
  API_KEY: abc123xyz

# ✅ Good: Use GitHub Secrets
env:
  API_KEY: ${{ secrets.API_KEY }}
```

### Use GITHUB_TOKEN for API Access
```yaml
steps:
  - name: Create comment
    uses: actions/github-script@v7
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      script: |
        github.rest.issues.createComment({
          issue_number: context.issue.number,
          owner: context.repo.owner,
          repo: context.repo.repo,
          body: 'Tests passed!'
        })
```

### Pin Action Versions
```yaml
# ✅ Good: Pin to specific version with SHA
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1

# ⚠️ Acceptable: Pin to major version
- uses: actions/checkout@v4

# ❌ Bad: Using latest (unpredictable)
- uses: actions/checkout@main
```

## Performance Optimization

### Cache Dependencies
```yaml
- name: Set up Ruby
  uses: ruby/setup-ruby@v1
  with:
    ruby-version: .ruby-version
    bundler-cache: true  # Caches gems automatically

- name: Cache node modules
  uses: actions/cache@v4
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### Run Jobs in Parallel
```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: bundle exec rubocop
  
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: bundle exec rspec
  
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: bundle exec brakeman
```

### Use Matrix Builds for Multiple Versions
```yaml
jobs:
  test:
    strategy:
      matrix:
        ruby-version: ['3.1', '3.2', '3.3']
        rails-version: ['7.0', '7.1']
    
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Ruby ${{ matrix.ruby-version }}
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: ${{ matrix.ruby-version }}
      
      - name: Run tests with Rails ${{ matrix.rails-version }}
        run: bundle exec rspec
        env:
          RAILS_VERSION: ${{ matrix.rails-version }}
```

## Common Workflows

### Rails CI Pipeline
```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: .ruby-version
          bundler-cache: true
      - name: Run RuboCop
        run: bundle exec rubocop

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: .ruby-version
          bundler-cache: true
      - name: Run Brakeman
        run: bundle exec brakeman -q -z

  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      
      redis:
        image: redis:7
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - uses: actions/checkout@v4
      
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: .ruby-version
          bundler-cache: true
      
      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version-file: .node-version
          cache: npm
      
      - name: Install dependencies
        run: npm ci
      
      - name: Set up database
        run: |
          bin/rails db:create
          bin/rails db:schema:load
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
          RAILS_ENV: test
      
      - name: Compile assets
        run: bin/rails assets:precompile
        env:
          RAILS_ENV: test
      
      - name: Run tests
        run: bundle exec rspec
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
          REDIS_URL: redis://localhost:6379/0
          RAILS_ENV: test
```

### Deployment Workflow
```yaml
name: Deploy

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.13.15
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: ${{ secrets.HEROKU_APP_NAME }}
          heroku_email: ${{ secrets.HEROKU_EMAIL }}
      
      - name: Notify deployment
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.repos.createDeploymentStatus({
              owner: context.repo.owner,
              repo: context.repo.repo,
              deployment_id: context.payload.deployment.id,
              state: 'success',
              environment_url: 'https://app.example.com'
            })
```

## Error Handling

### Fail Fast vs Continue on Error
```yaml
jobs:
  test:
    steps:
      # Fail immediately if critical step fails
      - name: Run tests
        run: bundle exec rspec
      
      # Continue even if this step fails
      - name: Upload coverage
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/
      
      # Run only on failure
      - name: Report failure
        if: failure()
        run: echo "Tests failed!"
```

### Set Timeout for Long-Running Jobs
```yaml
jobs:
  test:
    timeout-minutes: 30  # Fail after 30 minutes
    runs-on: ubuntu-latest
    steps:
      - run: bundle exec rspec
```

## Environment Variables

### Define at Appropriate Level
```yaml
# Job level - applies to all steps
jobs:
  test:
    env:
      RAILS_ENV: test
      DATABASE_URL: postgres://localhost/test
    
    steps:
      - run: bundle exec rspec

# Step level - applies to single step
jobs:
  test:
    steps:
      - name: Run tests
        run: bundle exec rspec
        env:
          RAILS_ENV: test
```

## Conditional Execution

### Run Jobs Based on Conditions
```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying to production"
  
  test:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - run: bundle exec rspec
```

### Skip Workflows for Certain Paths
```yaml
on:
  push:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  pull_request:
    paths:
      - 'app/**'
      - 'lib/**'
      - 'spec/**'
```

## Debugging Workflows

### Enable Debug Logging
Add these repository secrets for more verbose logging:
- `ACTIONS_RUNNER_DEBUG`: true
- `ACTIONS_STEP_DEBUG`: true

### Use tmate for Interactive Debugging
```yaml
- name: Setup tmate session
  if: failure()
  uses: mxschmitt/action-tmate@v3
  timeout-minutes: 15
```

## Naming Conventions

### Workflow Names
- Use descriptive names: `CI`, `Deploy to Production`, `Update Dependencies`
- Keep names concise but clear

### Job Names
- Use verb phrases: `lint`, `test`, `deploy`, `build-assets`
- Group related jobs with prefixes: `test-unit`, `test-integration`

### Step Names
- Be specific: `Run RSpec tests`, `Set up PostgreSQL`, `Deploy to Heroku`
- Include context when helpful: `Run tests with Ruby 3.2`

## Required Workflows

### Minimum CI Requirements
Every Rails application should have:
1. ✅ Linting (RuboCop)
2. ✅ Security scanning (Brakeman)
3. ✅ Tests (RSpec)
4. ✅ Database setup and migrations check

### Branch Protection
Configure branch protection rules to require:
- ✅ Status checks must pass before merging
- ✅ Require branches to be up to date
- ✅ Require review from code owners

## Workflow Maintenance

### Keep Actions Updated
- Review and update action versions quarterly
- Subscribe to security advisories for actions you use
- Test updates in a separate branch first

### Monitor Workflow Performance
- Review workflow run times monthly
- Optimize slow steps
- Consider splitting long-running jobs

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [MPI Application Workflows](https://github.com/mpimedia/mpi-application-workflows)
- [GitHub Actions Security Best Practices](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
