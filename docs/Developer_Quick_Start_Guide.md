# LazyFranchisor: Developer Quick Start Guide

**Document Version:** 1.0
**Date:** October 29, 2025
**Purpose:** Get new developers up and running quickly

---

## Table of Contents

1. [First Day Setup (30 minutes)](#first-day-setup-30-minutes)
2. [Local Development Environment](#local-development-environment)
3. [Project Repository Structure](#project-repository-structure)
4. [Running the Application Locally](#running-the-application-locally)
5. [Making Your First Contribution](#making-your-first-contribution)
6. [Common Development Tasks](#common-development-tasks)
7. [Troubleshooting](#troubleshooting)
8. [Getting Help](#getting-help)

---

## First Day Setup (30 minutes)

### Prerequisites Checklist

Install the following software:

#### Required Tools

```bash
# 1. Git (version control)
git --version  # Should be 2.40+

# 2. Rust toolchain
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustc --version  # Should be 1.70+

# 3. Node.js and npm
node --version  # Should be 18+
npm --version   # Should be 9+

# 4. Docker Desktop
docker --version         # Should be 24+
docker-compose --version # Should be 2.20+

# 5. PostgreSQL client tools (for local development)
psql --version  # Should be 15+

# 6. Redis CLI (optional, for debugging)
redis-cli --version  # Should be 7+
```

#### Recommended IDE Setup

**VS Code** (recommended):
```bash
# Install VS Code
# Download from: https://code.visualstudio.com/

# Install extensions:
code --install-extension rust-lang.rust-analyzer
code --install-extension tamasfe.even-better-toml
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension bradlc.vscode-tailwindcss
code --install-extension ms-azuretools.vscode-docker
```

**Or IntelliJ IDEA**:
- Install Rust plugin
- Install JavaScript/TypeScript plugin
- Install Docker plugin

---

### Account Access

Request access to:

- [ ] GitHub organization: `github.com/lazyfranchisor`
- [ ] Slack workspace: `lazyfranchisor.slack.com`
- [ ] AWS Console (dev environment only)
- [ ] Linear/Jira (project management)
- [ ] Figma (design files)
- [ ] 1Password (team vault for secrets)

---

## Local Development Environment

### Step 1: Clone the Repository

```bash
# Clone the main repository
git clone https://github.com/lazyfranchisor/lazyfranchisor.git
cd lazyfranchisor

# Verify repository structure
ls -la
# You should see: backend/ frontend/ docs/ infrastructure/
```

---

### Step 2: Setup Environment Variables

#### Backend Environment

```bash
cd backend

# Copy the example environment file
cp .env.example .env

# Edit .env with your local configuration
# Use your favorite editor (vim, nano, code, etc.)
code .env
```

**backend/.env:**
```bash
# Database
DATABASE_URL=postgres://postgres:postgres@localhost:5432/lazyfranchisor_dev

# Redis
REDIS_URL=redis://localhost:6379

# JWT Secret (use a random string for local dev)
JWT_SECRET=local-dev-secret-change-in-production

# Server
SERVER_HOST=0.0.0.0
SERVER_PORT=3000

# Logging
RUST_LOG=debug,sqlx=warn

# AWS (leave empty for local dev)
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=

# Email (optional for local dev)
SMTP_HOST=
SMTP_PORT=
SMTP_USERNAME=
SMTP_PASSWORD=
```

#### Frontend Environment

```bash
cd ../frontend/web

# Copy example environment
cp .env.example .env.local

# Edit .env.local
code .env.local
```

**frontend/web/.env.local:**
```bash
# API URL (points to local backend)
VITE_API_URL=http://localhost:3000/api/v1

# App Environment
VITE_APP_ENV=development

# Feature Flags (optional)
VITE_ENABLE_ANALYTICS=false
VITE_ENABLE_BETA_FEATURES=true
```

---

### Step 3: Start Local Services (Docker)

```bash
# From project root
cd ../..

# Start PostgreSQL and Redis using Docker Compose
docker-compose up -d postgres redis

# Verify services are running
docker-compose ps

# Should show:
# NAME                    STATUS
# lazyfranchisor_postgres_1   Up
# lazyfranchisor_redis_1      Up

# Test PostgreSQL connection
psql postgres://postgres:postgres@localhost:5432/postgres -c "SELECT version();"

# Test Redis connection
redis-cli -h localhost -p 6379 ping
# Should return: PONG
```

---

### Step 4: Setup Backend Database

```bash
cd backend

# Install sqlx CLI (for database migrations)
cargo install sqlx-cli --no-default-features --features postgres

# Create database
sqlx database create

# Run migrations
sqlx migrate run

# Verify migrations
sqlx migrate info

# Expected output:
# Applied migrations:
# 20251101000001_create_users
# 20251101000002_create_roles
# ...

# Seed development data (optional)
cargo run --bin seed-dev-data
```

---

### Step 5: Install Dependencies

#### Backend (Rust)

```bash
cd backend

# Build project (first time will take 5-10 minutes)
cargo build

# Run tests to verify setup
cargo test

# Expected output:
# running 47 tests
# test result: ok. 47 passed; 0 failed; 0 ignored; 0 measured
```

#### Frontend (Web)

```bash
cd ../frontend/web

# Install dependencies
npm install

# Run tests
npm test

# Expected output:
# Test Suites: 12 passed, 12 total
# Tests:       48 passed, 48 total
```

#### Mobile (React Native)

```bash
cd ../mobile

# Install dependencies
npm install

# iOS setup (macOS only)
cd ios
pod install
cd ..

# Note: You don't need to run the mobile app for backend development
```

---

## Project Repository Structure

```
lazyfranchisor/
├── backend/                  # Rust API server
│   ├── Cargo.toml           # Rust dependencies
│   ├── src/
│   │   ├── main.rs          # Application entry point
│   │   ├── lib.rs           # Library root
│   │   ├── config/          # Configuration management
│   │   ├── models/          # Data models (User, Sales, etc.)
│   │   ├── services/        # Business logic
│   │   ├── handlers/        # HTTP request handlers
│   │   ├── middleware/      # Auth, logging, etc.
│   │   ├── db/              # Database utilities
│   │   └── errors.rs        # Error types
│   ├── migrations/          # SQL migrations
│   ├── tests/               # Integration tests
│   └── .env.example         # Environment template
│
├── frontend/
│   ├── web/                 # React web application
│   │   ├── src/
│   │   │   ├── components/  # React components
│   │   │   ├── pages/       # Page components
│   │   │   ├── hooks/       # Custom React hooks
│   │   │   ├── services/    # API client
│   │   │   ├── store/       # Redux store
│   │   │   ├── types/       # TypeScript types
│   │   │   └── utils/       # Helper functions
│   │   ├── public/          # Static assets
│   │   ├── package.json     # Node dependencies
│   │   └── vite.config.ts   # Vite configuration
│   │
│   ├── mobile/              # React Native app
│   │   ├── src/
│   │   ├── android/         # Android project
│   │   ├── ios/             # iOS project
│   │   └── package.json
│   │
│   └── shared/              # Shared code (types, utils)
│
├── infrastructure/          # DevOps and infrastructure
│   ├── terraform/           # Infrastructure as Code
│   ├── k8s/                 # Kubernetes manifests
│   └── docker/              # Dockerfiles
│
├── docs/                    # Documentation
│   ├── Development_Team_Handbook.md
│   ├── API_Documentation.md
│   └── ...
│
├── docker-compose.yml       # Local development services
├── .github/                 # GitHub Actions workflows
└── README.md                # Project overview
```

---

## Running the Application Locally

### Option 1: Run Services Individually (Recommended for Development)

This gives you better control and faster reload times.

#### Terminal 1: Backend API

```bash
cd backend

# Run with auto-reload on code changes
cargo watch -x run

# Or run without watch
cargo run

# Server starts at: http://localhost:3000
# API endpoints at: http://localhost:3000/api/v1/...
# Health check: http://localhost:3000/health
```

Expected output:
```
   Compiling lazyfranchisor v0.1.0
    Finished dev [unoptimized + debuginfo] target(s) in 12.34s
     Running `target/debug/lazyfranchisor`
2025-10-29T10:30:00Z INFO  lazyfranchisor: Starting server on 0.0.0.0:3000
2025-10-29T10:30:00Z INFO  lazyfranchisor: Database connected
2025-10-29T10:30:00Z INFO  lazyfranchisor: Redis connected
2025-10-29T10:30:00Z INFO  lazyfranchisor: Server is running at http://0.0.0.0:3000
```

#### Terminal 2: Frontend Web App

```bash
cd frontend/web

# Start development server
npm run dev

# App starts at: http://localhost:5173
```

Expected output:
```
  VITE v4.5.0  ready in 823 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help
```

#### Terminal 3: Watch Tests (Optional)

```bash
cd backend

# Run tests in watch mode
cargo watch -x test
```

---

### Option 2: Run Everything with Docker Compose

This runs the entire stack in containers (slower for development).

```bash
# From project root
docker-compose up

# Access:
# - Backend API: http://localhost:3000
# - Frontend: http://localhost:5173
# - PostgreSQL: localhost:5432
# - Redis: localhost:6379

# Stop all services
docker-compose down
```

---

### Verify Everything is Working

#### 1. Test Backend API

```bash
# Health check
curl http://localhost:3000/health
# Expected: {"status":"ok"}

# Test registration (should fail without all fields - expected)
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com"}'
# Expected: {"error":"Validation error: ..."}
```

#### 2. Open Frontend

Visit http://localhost:5173 in your browser.

You should see the login page.

---

## Making Your First Contribution

### Step 1: Pick a Task

- Check Linear/Jira for tasks labeled `good-first-issue`
- Or ask your team lead for a starter task

Example task: "Add phone number field to user profile"

---

### Step 2: Create a Feature Branch

```bash
# Always branch from develop
git checkout develop
git pull origin develop

# Create feature branch
git checkout -b feature/add-user-phone-number

# Naming convention:
# feature/  - new features
# bugfix/   - bug fixes
# hotfix/   - urgent production fixes
# refactor/ - code improvements
```

---

### Step 3: Make Changes

#### Backend: Add Phone Number to User Model

**1. Create migration:**
```bash
cd backend

# Create new migration file
sqlx migrate add add_user_phone_number
```

This creates: `migrations/YYYYMMDDHHMMSS_add_user_phone_number.sql`

Edit the file:
```sql
-- Add phone number column
ALTER TABLE users
ADD COLUMN phone VARCHAR(20);

-- Add index for phone lookups
CREATE INDEX idx_users_phone ON users(phone);
```

**2. Update Rust model:**
```rust
// backend/src/models/user.rs

#[derive(Debug, Serialize, Deserialize, sqlx::FromRow)]
pub struct User {
    pub id: Uuid,
    pub email: String,
    #[serde(skip_serializing)]
    pub password_hash: String,
    pub first_name: Option<String>,
    pub last_name: Option<String>,
    pub phone: Option<String>,  // NEW FIELD
    pub role_id: Uuid,
    pub is_active: bool,
    pub created_at: DateTime<Utc>,
    pub updated_at: DateTime<Utc>,
}
```

**3. Update API endpoints:**
```rust
// backend/src/handlers/user_handler.rs

#[derive(Deserialize, Validate)]
pub struct UpdateUserRequest {
    pub first_name: Option<String>,
    pub last_name: Option<String>,
    #[validate(phone)]  // Validate phone format
    pub phone: Option<String>,  // NEW FIELD
}
```

**4. Run migration:**
```bash
sqlx migrate run
```

**5. Write tests:**
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_update_user_phone() {
        // Test implementation
    }
}
```

---

#### Frontend: Add Phone Field to UI

**1. Update TypeScript types:**
```typescript
// frontend/web/src/types/models.ts

export interface User {
  id: string;
  email: string;
  firstName?: string;
  lastName?: string;
  phone?: string;  // NEW FIELD
  roleId: string;
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
}
```

**2. Update profile form:**
```typescript
// frontend/web/src/components/features/profile/ProfileForm.tsx

export const ProfileForm = () => {
  const [formData, setFormData] = useState({
    firstName: user?.firstName || '',
    lastName: user?.lastName || '',
    phone: user?.phone || '',  // NEW FIELD
  });

  return (
    <form onSubmit={handleSubmit}>
      <Input
        label="First Name"
        value={formData.firstName}
        onChange={(e) => setFormData({ ...formData, firstName: e.target.value })}
      />

      <Input
        label="Last Name"
        value={formData.lastName}
        onChange={(e) => setFormData({ ...formData, lastName: e.target.value })}
      />

      {/* NEW FIELD */}
      <Input
        label="Phone Number"
        type="tel"
        value={formData.phone}
        onChange={(e) => setFormData({ ...formData, phone: e.target.value })}
        placeholder="+1 (555) 123-4567"
      />

      <Button type="submit">Save Changes</Button>
    </form>
  );
};
```

**3. Write tests:**
```typescript
// frontend/web/src/components/features/profile/ProfileForm.test.tsx

describe('ProfileForm', () => {
  it('renders phone number field', () => {
    render(<ProfileForm />);
    expect(screen.getByLabelText('Phone Number')).toBeInTheDocument();
  });

  it('updates phone number', async () => {
    render(<ProfileForm />);

    const phoneInput = screen.getByLabelText('Phone Number');
    fireEvent.change(phoneInput, { target: { value: '+1234567890' } });

    expect(phoneInput).toHaveValue('+1234567890');
  });
});
```

---

### Step 4: Test Your Changes

```bash
# Run backend tests
cd backend
cargo test

# Run frontend tests
cd ../frontend/web
npm test

# Manual testing:
# 1. Start backend and frontend
# 2. Open http://localhost:5173
# 3. Navigate to profile page
# 4. Update phone number
# 5. Verify it saves correctly
```

---

### Step 5: Commit and Push

```bash
# Check what changed
git status

# Stage changes
git add .

# Commit with conventional commit message
git commit -m "feat(user): add phone number field to user profile

- Added phone column to users table
- Updated User model with phone field
- Added phone input to profile form
- Added validation for phone format
- Added tests for phone field

Closes #123"

# Push to remote
git push origin feature/add-user-phone-number
```

---

### Step 6: Create Pull Request

1. Go to GitHub: https://github.com/lazyfranchisor/lazyfranchisor
2. Click "Compare & pull request"
3. Fill out PR template:

```markdown
## Description
Added phone number field to user profile

## Type of Change
- [x] New feature
- [ ] Bug fix
- [ ] Breaking change

## Related Issues
Closes #123

## Changes Made
- Added `phone` column to `users` table
- Updated Rust `User` model
- Added phone input field to profile form
- Added tests for new field

## Screenshots
[Add screenshot of profile form with phone field]

## Testing
- [x] Unit tests pass
- [x] Integration tests pass
- [x] Manual testing completed

## Checklist
- [x] Code follows style guidelines
- [x] Self-review completed
- [x] Tests added
- [x] Documentation updated (if needed)
```

4. Request review from team lead
5. Address any feedback
6. Once approved, squash and merge

---

## Common Development Tasks

### Running Tests

```bash
# Backend - all tests
cd backend
cargo test

# Backend - specific test
cargo test test_create_user

# Backend - with output
cargo test -- --nocapture

# Frontend - all tests
cd frontend/web
npm test

# Frontend - watch mode
npm test -- --watch

# Frontend - coverage
npm test -- --coverage
```

---

### Database Operations

```bash
# Create a new migration
sqlx migrate add migration_name

# Run migrations
sqlx migrate run

# Revert last migration
sqlx migrate revert

# Check migration status
sqlx migrate info

# Reset database (WARNING: deletes all data)
sqlx database drop
sqlx database create
sqlx migrate run
```

---

### Code Quality Checks

```bash
# Rust formatting
cargo fmt

# Rust linting
cargo clippy

# Fix clippy warnings automatically
cargo clippy --fix

# TypeScript/React linting
npm run lint

# Fix lint errors automatically
npm run lint -- --fix

# Type checking
npm run type-check
```

---

### Debugging

#### Backend Debugging

Add logging:
```rust
use tracing::{info, debug, error};

#[instrument]
pub async fn create_user(data: CreateUserRequest) -> Result<User> {
    info!("Creating user with email: {}", data.email);

    debug!("Hashing password");
    let password_hash = hash_password(&data.password)?;

    debug!("Inserting into database");
    let user = sqlx::query_as::<_, User>(
        "INSERT INTO users (email, password_hash) VALUES ($1, $2) RETURNING *"
    )
    .bind(&data.email)
    .bind(&password_hash)
    .fetch_one(&pool)
    .await?;

    info!("User created successfully: {}", user.id);
    Ok(user)
}
```

View logs:
```bash
# All logs
RUST_LOG=debug cargo run

# Specific module
RUST_LOG=lazyfranchisor::services::user_service=debug cargo run
```

---

#### Frontend Debugging

Use browser DevTools:
1. Open browser DevTools (F12)
2. Go to Network tab to inspect API requests
3. Go to Console tab to see logs
4. Use React DevTools to inspect component state

Add debug logging:
```typescript
console.log('Form data:', formData);
console.debug('API response:', response);
console.error('Error occurred:', error);
```

---

### Viewing API Documentation

```bash
# Start backend
cd backend
cargo run

# Open Swagger UI in browser
open http://localhost:3000/api/docs

# Or view OpenAPI spec
curl http://localhost:3000/api/openapi.json
```

---

## Troubleshooting

### Backend Won't Start

**Problem:** `Error: Connection refused to database`

**Solution:**
```bash
# Check if PostgreSQL is running
docker-compose ps postgres

# If not running, start it
docker-compose up -d postgres

# Verify connection
psql postgres://postgres:postgres@localhost:5432/postgres -c "SELECT 1"
```

---

**Problem:** `Error: sqlx migration failed`

**Solution:**
```bash
# Reset database and re-run migrations
sqlx database drop
sqlx database create
sqlx migrate run
```

---

**Problem:** `Compilation error: trait not implemented`

**Solution:**
```bash
# Clean build artifacts and rebuild
cargo clean
cargo build
```

---

### Frontend Won't Start

**Problem:** `Module not found` errors

**Solution:**
```bash
# Delete node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

---

**Problem:** `Network error when calling API`

**Solution:**
1. Check backend is running: `curl http://localhost:3000/health`
2. Check CORS settings in backend
3. Verify API URL in `.env.local` is correct: `VITE_API_URL=http://localhost:3000/api/v1`

---

### Database Issues

**Problem:** `Migration already applied` error

**Solution:**
```bash
# Check migration status
sqlx migrate info

# If stuck, manually revert from database
psql postgres://postgres:postgres@localhost:5432/lazyfranchisor_dev

# Inside psql:
DELETE FROM _sqlx_migrations WHERE version = <version_number>;
\q

# Then re-run migration
sqlx migrate run
```

---

**Problem:** `Too many connections` error

**Solution:**
```bash
# Restart PostgreSQL
docker-compose restart postgres

# Or increase max_connections in PostgreSQL config
```

---

### Docker Issues

**Problem:** `Port already in use`

**Solution:**
```bash
# Find what's using the port
lsof -i :3000  # or :5432, :6379

# Kill the process
kill -9 <PID>

# Or use different ports in docker-compose.yml
```

---

**Problem:** Docker containers not starting

**Solution:**
```bash
# View logs
docker-compose logs postgres

# Rebuild containers
docker-compose down
docker-compose up --build
```

---

## Getting Help

### Resources

1. **Documentation**
   - Development Team Handbook: `/docs/Development_Team_Handbook.md`
   - API Documentation: http://localhost:3000/api/docs
   - Database Schema: `/docs/Database_Schema.md`

2. **Code Examples**
   - Check existing code for patterns
   - Look at similar features in the codebase
   - Review closed PRs for examples

3. **External Resources**
   - Rust Book: https://doc.rust-lang.org/book/
   - Axum Docs: https://docs.rs/axum
   - React Docs: https://react.dev/
   - TypeScript Handbook: https://www.typescriptlang.org/docs/

---

### Asking for Help

**Good Question:**
```
I'm working on adding phone validation to the user profile (PR #456).
I've implemented validation in the frontend, but I'm not sure about
the best way to validate phone numbers in Rust.

I tried using the `phonenumber` crate, but I'm getting a compilation
error: "trait `Validate` is not implemented for `PhoneNumber`"

Here's my code: [link to code]

What's the recommended approach for phone validation in our backend?
```

**Channels to Ask:**
- Slack `#engineering` - General questions
- Slack `#engineering-backend` - Backend-specific
- Slack `#engineering-frontend` - Frontend-specific
- Your team lead - Architecture decisions
- PR comments - Code review questions

---

### Emergency Contacts

**On-call Engineer:** See Slack channel #engineering-oncall

**Team Leads:**
- Backend: @senior-backend-engineer
- Frontend: @senior-frontend-engineer
- DevOps: @devops-engineer
- CTO: @cto (escalations only)

---

## Next Steps

✅ You've completed the quick start!

**What to do next:**

1. [ ] Read the full Development Team Handbook
2. [ ] Complete your first task and create a PR
3. [ ] Participate in your first sprint planning
4. [ ] Shadow a senior engineer for a day
5. [ ] Review and understand the system architecture
6. [ ] Set up your IDE with recommended settings
7. [ ] Join the next team retrospective

**Week 1 Goals:**
- Environment set up and running
- First PR submitted
- Understand project structure
- Meet the team

**Week 2 Goals:**
- First PR merged
- Contributing to sprint
- Familiar with coding standards
- Comfortable with git workflow

---

**Welcome to the team! 🎉**

If you have any questions, don't hesitate to ask in Slack. We're here to help!

---

**Document Control:**
- **Version:** 1.0
- **Last Updated:** October 29, 2025
- **Owner:** CTO / Engineering Lead
- **Maintained by:** Engineering Team

*End of Developer Quick Start Guide*
