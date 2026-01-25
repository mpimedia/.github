---
applyTo: "**/*_spec.rb,**/spec/**/*.rb"
---

# RSpec Test Guidelines

These instructions apply to all RSpec test files in Ruby on Rails applications.

## Test Structure

### Use Descriptive Nested Contexts
```ruby
describe UserService do
  describe '#create_user' do
    context 'when parameters are valid' do
      it 'creates a user record' do
        # test implementation
      end
      
      it 'sends a welcome email' do
        # test implementation
      end
    end
    
    context 'when email is invalid' do
      it 'returns an error' do
        # test implementation
      end
      
      it 'does not create a user record' do
        # test implementation
      end
    end
  end
end
```

## Test Data

### Use FactoryBot Factories
```ruby
# ✅ Good: Use factories
let(:user) { create(:user) }
let(:admin) { create(:user, :admin) }
let(:post) { create(:post, author: user) }

# ❌ Avoid: Manual object creation in tests
let(:user) do
  User.create!(
    name: 'Test User',
    email: 'test@example.com',
    password: 'password123'
  )
end
```

### Use let and let! Appropriately
```ruby
# Use let for lazy evaluation (created when first accessed)
let(:user) { create(:user) }

# Use let! for immediate evaluation (created before each test)
let!(:published_posts) { create_list(:post, 3, :published) }

# Use subject when testing a single object
subject(:service) { UserService.new(user) }
```

## Assertions

### Test Behavior, Not Implementation
```ruby
# ✅ Good: Test the outcome
it 'sends a welcome email' do
  expect { service.create_user(params) }
    .to change { ActionMailer::Base.deliveries.count }.by(1)
  
  mail = ActionMailer::Base.deliveries.last
  expect(mail.to).to include(params[:email])
  expect(mail.subject).to eq('Welcome to MPI Media')
end

# ❌ Bad: Test the implementation details
it 'calls the mailer' do
  expect(UserMailer).to receive(:welcome_email).with(user)
  service.create_user(params)
end
```

### Use Specific Matchers
```ruby
# ✅ Good: Specific matchers
expect(user.errors).to be_present
expect(user.email).to eq('test@example.com')
expect(users).to contain_exactly(user1, user2)
expect { service.call }.to raise_error(ArgumentError)

# ❌ Bad: Generic matchers
expect(user.errors.any?).to be true
expect(user.email == 'test@example.com').to be true
```

## Database & Performance

### Use Database Cleaner Strategies
```ruby
# Default: Use transactions for speed
RSpec.configure do |config|
  config.use_transactional_fixtures = true
end

# Only use database_cleaner for feature/system tests
config.before(:each, type: :system) do
  driven_by :selenium_chrome_headless
end
```

### Avoid Unnecessary Database Hits
```ruby
# ✅ Good: Use build for tests that don't need persistence
let(:user) { build(:user) }

# ✅ Good: Use build_stubbed for maximum speed
let(:user) { build_stubbed(:user) }

# ❌ Avoid: create when not needed
let(:user) { create(:user) }  # Only use when you need database persistence
```

## Testing Controllers

### Test Public Interface
```ruby
describe PostsController do
  describe 'GET #index' do
    it 'returns a successful response' do
      get :index
      expect(response).to have_http_status(:success)
    end
    
    it 'assigns all posts' do
      post1 = create(:post)
      post2 = create(:post)
      get :index
      expect(assigns(:posts)).to match_array([post1, post2])
    end
  end
  
  describe 'POST #create' do
    context 'with valid parameters' do
      let(:valid_params) { { post: attributes_for(:post) } }
      
      it 'creates a new post' do
        expect { post :create, params: valid_params }
          .to change(Post, :count).by(1)
      end
      
      it 'redirects to the created post' do
        post :create, params: valid_params
        expect(response).to redirect_to(Post.last)
      end
    end
  end
end
```

## Testing Models

### Validate Presence and Format
```ruby
describe User do
  describe 'validations' do
    it { is_expected.to validate_presence_of(:email) }
    it { is_expected.to validate_presence_of(:name) }
    it { is_expected.to validate_uniqueness_of(:email).case_insensitive }
    
    it 'validates email format' do
      user = build(:user, email: 'invalid')
      expect(user).not_to be_valid
      expect(user.errors[:email]).to be_present
    end
  end
  
  describe 'associations' do
    it { is_expected.to have_many(:posts).dependent(:destroy) }
    it { is_expected.to belong_to(:organization) }
  end
end
```

### Test Model Methods
```ruby
describe '#full_name' do
  it 'returns the combined first and last name' do
    user = build(:user, first_name: 'John', last_name: 'Doe')
    expect(user.full_name).to eq('John Doe')
  end
  
  context 'when last name is missing' do
    it 'returns only the first name' do
      user = build(:user, first_name: 'John', last_name: nil)
      expect(user.full_name).to eq('John')
    end
  end
end
```

## Testing Services

### Setup and Expectations
```ruby
describe UserRegistrationService do
  subject(:service) { described_class.new(params) }
  
  let(:params) do
    {
      email: 'test@example.com',
      password: 'secure_password',
      name: 'Test User'
    }
  end
  
  describe '#call' do
    context 'with valid parameters' do
      it 'creates a user' do
        expect { service.call }.to change(User, :count).by(1)
      end
      
      it 'sends a welcome email' do
        expect { service.call }
          .to change { ActionMailer::Base.deliveries.count }.by(1)
      end
      
      it 'returns success' do
        result = service.call
        expect(result).to be_success
        expect(result.user).to be_persisted
      end
    end
    
    context 'with invalid email' do
      let(:params) { super().merge(email: 'invalid') }
      
      it 'does not create a user' do
        expect { service.call }.not_to change(User, :count)
      end
      
      it 'returns failure with error' do
        result = service.call
        expect(result).to be_failure
        expect(result.errors).to include(:email)
      end
    end
  end
end
```

## Testing Background Jobs

### Use Test Helpers
```ruby
describe SendWelcomeEmailJob do
  describe '#perform' do
    let(:user) { create(:user) }
    
    it 'sends an email to the user' do
      expect { described_class.perform_now(user.id) }
        .to change { ActionMailer::Base.deliveries.count }.by(1)
    end
    
    it 'enqueues the job' do
      expect {
        described_class.perform_later(user.id)
      }.to have_enqueued_job(described_class).with(user.id)
    end
  end
end
```

## Coverage Requirements

- **Minimum 80% coverage** for new code
- **100% coverage** for critical business logic (payments, auth, data integrity)
- Run coverage with: `COVERAGE=true bundle exec rspec`

## Test Organization

### File Structure
```
spec/
├── models/
│   └── user_spec.rb
├── controllers/
│   └── users_controller_spec.rb
├── services/
│   └── user_registration_service_spec.rb
├── jobs/
│   └── send_welcome_email_job_spec.rb
├── helpers/
│   └── application_helper_spec.rb
├── requests/
│   └── api/v1/users_spec.rb
├── system/
│   └── user_registration_spec.rb
└── support/
    ├── factory_bot.rb
    └── shared_examples/
```

## Common Patterns

### Shared Examples
```ruby
# spec/support/shared_examples/authenticatable.rb
RSpec.shared_examples 'authenticatable' do
  it 'requires authentication' do
    expect(response).to redirect_to(login_path)
  end
  
  it 'sets a flash message' do
    expect(flash[:alert]).to be_present
  end
end

# Usage in spec
describe AdminController do
  describe 'GET #index' do
    context 'when not logged in' do
      before { get :index }
      it_behaves_like 'authenticatable'
    end
  end
end
```

### Custom Matchers
```ruby
# spec/support/matchers/have_error_on.rb
RSpec::Matchers.define :have_error_on do |attribute|
  match do |model|
    model.valid?
    model.errors[attribute].present?
  end
  
  failure_message do |model|
    "expected #{model.class} to have error on #{attribute}, but had errors: #{model.errors.full_messages}"
  end
end

# Usage
expect(user).to have_error_on(:email)
```

## Before Committing Tests

✅ **Checklist:**
1. All tests pass: `bundle exec rspec`
2. No pending tests (remove `skip` or `pending`)
3. Tests are independent (can run in any order)
4. Test names are descriptive
5. No commented-out test code
6. Coverage meets requirements

## Resources

- [RSpec Documentation](https://rspec.info/)
- [Better Specs](https://www.betterspecs.org/)
- [FactoryBot Documentation](https://github.com/thoughtbot/factory_bot)
- [Shoulda Matchers](https://github.com/thoughtbot/shoulda-matchers)
