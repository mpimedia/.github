# MPI Media - GitHub Copilot Instructions

Welcome to MPI Media's organization-wide GitHub Copilot instructions. These guidelines help ensure consistent, high-quality code across all our projects.

## 🚨 CRITICAL: Branch Protection Requirements

**ALL changes must be made in feature branches or pull requests. NEVER commit directly to protected branches.**

### Protected Branches
- `main` - Primary production branch
- `master` - Legacy production branch (some repos)
- `develop` - Development integration branch (some repos)

### Required Workflow
1. **Always create a feature branch** before making any changes
2. **Use descriptive branch names** following the pattern:
   - `feature/description-of-feature`
   - `fix/description-of-bug`
   - `refactor/description-of-change`
   - `docs/description-of-docs`
3. **Make your changes** in the feature branch
4. **Create a pull request** when ready for review
5. **Never force push** to protected branches

### Examples
```bash
# ✅ CORRECT: Create a feature branch
git checkout -b feature/add-user-authentication
# Make changes, commit, push to feature branch

# ❌ WRONG: Committing directly to main
git checkout main
git commit -m "Add feature"  # DON'T DO THIS
```


---

## 🚨 CRITICAL: Agent Attribution (Required — No Exceptions)

Every AI agent (Copilot, Claude, or any other) **must** include attribution on every piece of work it produces. This is non-negotiable across all MPI Media repositories.

### Commits

Include a `Co-Authored-By` trailer with the agent's name:

```
Co-Authored-By: GitHub Copilot <noreply@github.com>
```

If you know the specific model, include it:

```
Co-Authored-By: GitHub Copilot (Claude Sonnet 4.5) <noreply@github.com>
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

### Pull Requests

Include the agent's name and model in the PR description footer:

```
Generated with GitHub Copilot (Claude Sonnet 4.5)
```

### Issue Comments

Include a brief attribution line at the end of each comment:

```
— GitHub Copilot (Claude Sonnet 4.5)
```

### PR Review Comments

Include attribution in each review comment or summary.

### Multiple Agents

If multiple agents contribute to the same commit, include a `Co-Authored-By` line for each.
---

## Organization Overview

MPI Media maintains multiple Ruby on Rails applications and supporting repositories:

### Applications
- **Optimus** - Rails application template and pattern source
- **Avails** - Central data repository
- **SFA** - Video clip hosting and search
- **Garden** - Static site generator
- **Harvest** - Ecommerce platform

### Configuration Repositories
- **mpi-application-standards** - Shared development standards and conventions
- **mpi-application-workflows** - Reusable GitHub Actions workflows
- **.github** - Organization-wide templates and defaults

---

## Technology Stack

### Primary Technologies
- **Ruby on Rails** - Primary framework for applications
- **Ruby** - Version specified in `.ruby-version` file
- **PostgreSQL** - Primary database
- **Redis** - Caching and background jobs
- **Sidekiq** - Background job processing
- **RSpec** - Testing framework
- **JavaScript/Node.js** - Frontend assets and tooling

### Development Tools
- **Bundler** - Ruby dependency management
- **RuboCop** - Ruby linting
- **Brakeman** - Security scanning
- **Standard** - Ruby style guide (some repos)

---

## Code Standards

### General Principles
1. **Follow Ruby/Rails conventions** - Prefer convention over configuration
2. **Write self-documenting code** - Clear naming over excessive comments
3. **Keep it simple** - Avoid premature optimization
4. **Test your changes** - All new features should have tests
5. **Security first** - Validate inputs, sanitize outputs, never commit secrets

### Ruby Style
- Use 2 spaces for indentation (no tabs)
- Keep lines under 120 characters when reasonable
- Follow RuboCop rules (check `.rubocop.yml` in repo)
- Prefer double quotes for strings (check repo convention)
- Use meaningful variable and method names

### Rails Patterns
- **Controllers** - Keep thin, delegate business logic to services/models
- **Models** - Use concerns for shared behavior, validate data
- **Services** - Extract complex business logic into service objects
- **Views** - Use partials for reusable components
- **Routes** - Use RESTful routing conventions

### Naming Conventions
- **Classes/Modules** - `PascalCase` (e.g., `UserService`, `PaymentProcessor`)
- **Methods/Variables** - `snake_case` (e.g., `calculate_total`, `user_email`)
- **Constants** - `SCREAMING_SNAKE_CASE` (e.g., `MAX_RETRIES`, `API_TIMEOUT`)
- **Files** - `snake_case.rb` (e.g., `user_service.rb`, `payment_processor.rb`)

---

## Build & Test

### Building the Application
```bash
# Install Ruby dependencies
bundle install

# Install JavaScript dependencies (if applicable)
npm install
# or
yarn install

# Setup database
rails db:create db:migrate

# Seed database (if applicable)
rails db:seed
```

### Running Tests
```bash
# Run full test suite
bundle exec rspec

# Run specific test file
bundle exec rspec spec/models/user_spec.rb

# Run specific test by line number
bundle exec rspec spec/models/user_spec.rb:42

# Run with coverage
COVERAGE=true bundle exec rspec
```

### Linting & Code Quality
```bash
# Run RuboCop linter
bundle exec rubocop

# Auto-fix RuboCop issues
bundle exec rubocop -a

# Run Brakeman security scanner
bundle exec brakeman

# Run Standard (if used)
bundle exec standardrb
```

### Before Committing
1. ✅ Run the test suite: `bundle exec rspec`
2. ✅ Run the linter: `bundle exec rubocop`
3. ✅ Run security scanner: `bundle exec brakeman`
4. ✅ Review your changes: `git diff`
5. ✅ Ensure you're on a feature branch: `git branch --show-current`

---

## Testing Guidelines

### Test Coverage
- **All new features** must include tests
- **Bug fixes** should include regression tests
- Aim for **80%+ code coverage** on new code
- Focus on meaningful tests over coverage metrics

### RSpec Best Practices
```ruby
# Use descriptive test names
describe UserService do
  describe '#create_user' do
    context 'with valid parameters' do
      it 'creates a user record' do
        # Test implementation
      end
    end
    
    context 'with invalid email' do
      it 'returns an error' do
        # Test implementation
      end
    end
  end
end

# Use let for test data
let(:user) { create(:user) }
let(:valid_params) { { name: 'John', email: 'john@example.com' } }

# Use factories (FactoryBot) over fixtures
let(:user) { create(:user, email: 'test@example.com') }

# Test behavior, not implementation
# ✅ Good: Tests the outcome
it 'sends a welcome email' do
  expect { service.create_user(params) }.to change { ActionMailer::Base.deliveries.count }.by(1)
end

# ❌ Bad: Tests the implementation
it 'calls the mailer' do
  expect(UserMailer).to receive(:welcome_email)
  service.create_user(params)
end
```

---

## Security Guidelines

### Never Commit Secrets
- ❌ API keys, passwords, tokens
- ❌ Database credentials
- ❌ Private keys or certificates
- ✅ Use environment variables
- ✅ Use Rails credentials: `rails credentials:edit`

### Input Validation
```ruby
# Always validate user input
class User < ApplicationRecord
  validates :email, presence: true, format: { with: URI::MailTo::EMAIL_REGEXP }
  validates :age, numericality: { greater_than: 0, less_than: 150 }
end

# Sanitize HTML input
class PostsController < ApplicationController
  def create
    @post = Post.new(post_params)
    @post.content = sanitize(@post.content)
    @post.save
  end
end

# Use strong parameters
def user_params
  params.require(:user).permit(:name, :email, :age)
end
```

### Authentication & Authorization
```ruby
# Always authenticate before sensitive actions
class AdminController < ApplicationController
  before_action :authenticate_user!
  before_action :require_admin!
  
  private
  
  def require_admin!
    redirect_to root_path unless current_user.admin?
  end
end
```

### SQL Injection Prevention
```ruby
# ✅ Good: Use parameterized queries
User.where("email = ?", params[:email])
User.where(email: params[:email])

# ❌ Bad: String interpolation (SQL injection risk)
User.where("email = '#{params[:email]}'")
```

---

## Database & Migrations

### Migration Best Practices
```ruby
# Always include rollback logic
class AddIndexToUsers < ActiveRecord::Migration[7.0]
  def change
    add_index :users, :email, unique: true
  end
end

# Use reversible for complex migrations
class AddStatusToOrders < ActiveRecord::Migration[7.0]
  def up
    add_column :orders, :status, :string, default: 'pending'
    # Data migration
    Order.update_all(status: 'pending')
  end
  
  def down
    remove_column :orders, :status
  end
end

# Always add indexes for foreign keys and frequently queried columns
add_index :posts, :user_id
add_index :posts, :published_at
add_index :posts, [:user_id, :published_at]
```

---

## Git & Pull Requests

### Commit Messages
Follow conventional commits format:
```
type(scope): brief description

Longer description if needed

- Bullet points for details
- Breaking changes noted
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

**Examples:**
- `feat(auth): add OAuth login support`
- `fix(api): handle timeout errors gracefully`
- `docs(readme): update installation instructions`

### Pull Request Guidelines
1. **Title** - Clear, descriptive summary
2. **Description** - Context, approach, and testing details
3. **Tests** - Include test coverage information
4. **Screenshots** - For UI changes
5. **Breaking Changes** - Clearly marked if applicable
6. **Links** - Reference related issues or PRs

---

## API Development

### RESTful Endpoints
```ruby
# Use standard REST actions
resources :posts do
  member do
    post :publish
    post :unpublish
  end
  
  collection do
    get :published
  end
end

# Return appropriate status codes
def create
  @post = Post.new(post_params)
  
  if @post.save
    render json: @post, status: :created
  else
    render json: { errors: @post.errors }, status: :unprocessable_entity
  end
end
```

### API Versioning
```ruby
# Version your APIs
namespace :api do
  namespace :v1 do
    resources :posts
  end
  
  namespace :v2 do
    resources :posts
  end
end
```

---

## Performance Considerations

### Database Queries
```ruby
# ✅ Use eager loading to avoid N+1 queries
@posts = Post.includes(:author, :comments).all

# ✅ Use select to limit columns
@users = User.select(:id, :name, :email).all

# ✅ Use batch processing for large datasets
User.find_each(batch_size: 1000) do |user|
  user.update_subscription_status
end

# ❌ Avoid N+1 queries
@posts.each do |post|
  post.author.name  # Triggers a query for each post
end
```

### Caching
```ruby
# Cache expensive operations
def expensive_calculation
  Rails.cache.fetch("calculation/#{id}", expires_in: 1.hour) do
    # Expensive operation here
    complex_computation
  end
end

# Use Russian Doll caching for views
<% cache post do %>
  <%= render post %>
<% end %>
```

---

## Documentation

### When to Add Comments
- **Complex algorithms** - Explain the "why", not the "what"
- **Business logic** - Document non-obvious business rules
- **TODOs** - Mark temporary solutions clearly
- **Public APIs** - Document parameters, return values, exceptions

### RDoc Format
```ruby
# Calculates the total price including tax and discounts
#
# @param subtotal [Float] The pre-tax price
# @param tax_rate [Float] The tax rate as a decimal (e.g., 0.08 for 8%)
# @return [Float] The final price including tax
# @example
#   calculate_total(100.0, 0.08) #=> 108.0
def calculate_total(subtotal, tax_rate)
  subtotal * (1 + tax_rate)
end
```

---

## Continuous Integration

Our projects use GitHub Actions for CI/CD:
- **RSpec tests** - Run on every push and PR
- **RuboCop linting** - Enforce code style
- **Brakeman security scan** - Check for vulnerabilities
- **Heroku deployment** - Auto-deploy on merge to main

Check `.github/workflows/` for specific workflow configurations.

---

## Repository-Specific Instructions

Individual repositories may have additional instructions in their own `.github/copilot-instructions.md` file. Always check the repository's documentation for:
- Specific setup requirements
- Custom rake tasks
- Deployment procedures
- Additional testing requirements
- Project-specific conventions

---

## Need Help?

### Resources
- [MPI Application Standards](https://github.com/mpimedia/mpi-application-standards)
- [MPI Application Workflows](https://github.com/mpimedia/mpi-application-workflows)
- [Ruby on Rails Guides](https://guides.rubyonrails.org/)
- [RSpec Documentation](https://rspec.info/)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)

### Contact
For questions about these guidelines, contact the development team or open an issue in the `.github` repository.

---

**Last Updated:** 2026-01-25  
**Version:** 1.0.0  
**Maintained by:** MPI Media DevOps Team
