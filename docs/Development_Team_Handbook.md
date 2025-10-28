# LazyFranchisor: Development Team Handbook

**Document Version:** 1.0
**Date:** October 29, 2025
**Purpose:** Comprehensive guide for development team structure, processes, and best practices

---

## Table of Contents

1. [Team Structure & Roles](#team-structure--roles)
2. [Technology Stack Deep Dive](#technology-stack-deep-dive)
3. [Development Environment Setup](#development-environment-setup)
4. [Coding Standards & Best Practices](#coding-standards--best-practices)
5. [Git Workflow & Branching Strategy](#git-workflow--branching-strategy)
6. [Code Review Process](#code-review-process)
7. [Testing Strategy](#testing-strategy)
8. [CI/CD Pipeline](#cicd-pipeline)
9. [Sprint Planning & Agile Process](#sprint-planning--agile-process)
10. [Architecture Decision Records](#architecture-decision-records)
11. [Performance & Optimization](#performance--optimization)
12. [Security Guidelines](#security-guidelines)
13. [Deployment Procedures](#deployment-procedures)
14. [Monitoring & Debugging](#monitoring--debugging)
15. [Team Communication](#team-communication)

---

## Team Structure & Roles

### Organizational Chart

```mermaid
graph TD
    CEO[CEO/Founder] --> CTO[CTO/Engineering Lead]
    CEO --> CPO[Chief Product Officer]

    CTO --> BACKEND[Backend Team]
    CTO --> FRONTEND[Frontend Team]
    CTO --> DEVOPS[DevOps Team]
    CTO --> QA[QA Team]

    CPO --> PM1[Product Manager - Core]
    CPO --> PM2[Product Manager - Growth]
    CPO --> DESIGN[Design Team]

    BACKEND --> BE1[Senior Backend Engineer - Rust]
    BACKEND --> BE2[Backend Engineer - Rust #1]
    BACKEND --> BE3[Backend Engineer - Rust #2]

    FRONTEND --> FE1[Senior Frontend Engineer]
    FRONTEND --> FE2[Frontend Engineer - Web]
    FRONTEND --> FE3[Mobile Engineer - React Native]

    DEVOPS --> DO1[DevOps Engineer]
    DEVOPS --> DO2[Site Reliability Engineer]

    QA --> QA1[QA Lead]
    QA --> QA2[QA Engineer - Automation]

    DESIGN --> D1[Product Designer]
    DESIGN --> D2[UX Researcher]
```

---

### Core Team Roles (Phase 1-2: Months 1-6)

#### 1. CTO / Engineering Lead

**Responsibilities:**
- Overall technical vision and architecture
- Technology stack decisions
- Team hiring and mentoring
- Code quality oversight
- Performance and scalability planning
- Stakeholder communication

**Key Metrics:**
- System uptime: 99.9%+
- Team velocity trends
- Code quality scores
- Time-to-market for features

**Reporting:** CEO

**Salary Range:** $150K - $200K + equity (2-4%)

---

#### 2. Senior Backend Engineer (Rust)

**Responsibilities:**
- Design and implement core backend services
- API design and documentation
- Database schema design
- Performance optimization
- Mentor junior engineers
- Code review leadership

**Required Skills:**
- 5+ years backend development
- 2+ years Rust experience
- Strong PostgreSQL knowledge
- Microservices architecture
- API design (REST, GraphQL)
- Performance tuning

**Key Deliverables:**
- Authentication service
- Core API gateway
- Data models and migrations
- Integration with external services

**Reporting:** CTO

**Salary Range:** $130K - $160K + equity (0.5-1%)

---

#### 3. Backend Engineers (Rust) - 2 positions

**Responsibilities:**
- Implement features per technical specs
- Write unit and integration tests
- Database migrations
- API endpoint development
- Bug fixes and maintenance
- Documentation

**Required Skills:**
- 2+ years backend development
- 1+ year Rust (or strong systems programming background)
- SQL proficiency
- RESTful API development
- Git workflow
- Testing frameworks

**Key Deliverables:**
- Feature development (accounting, warehouse, POS modules)
- Test coverage >80%
- API documentation

**Reporting:** Senior Backend Engineer

**Salary Range:** $100K - $130K + equity (0.25-0.5%)

---

#### 4. Senior Frontend Engineer

**Responsibilities:**
- Frontend architecture and component library
- State management design
- Performance optimization
- UI/UX implementation
- Mentor junior engineers
- Design system implementation

**Required Skills:**
- 5+ years frontend development
- Expert React/TypeScript
- State management (Redux, Context API)
- CSS-in-JS, responsive design
- Performance optimization
- Accessibility (WCAG 2.1)

**Key Deliverables:**
- Component library setup
- Authentication flows
- Core dashboard layouts
- Reusable UI components

**Reporting:** CTO

**Salary Range:** $120K - $150K + equity (0.5-1%)

---

#### 5. Frontend Engineer (Web)

**Responsibilities:**
- Implement UI components
- Integrate with backend APIs
- Write frontend tests
- Browser compatibility testing
- Bug fixes
- Documentation

**Required Skills:**
- 2+ years React development
- TypeScript proficiency
- CSS/SCSS
- API integration
- Testing (Jest, React Testing Library)
- Git workflow

**Key Deliverables:**
- Feature UI implementation
- Form validation and error handling
- Responsive layouts

**Reporting:** Senior Frontend Engineer

**Salary Range:** $90K - $120K + equity (0.25-0.5%)

---

#### 6. Mobile Engineer (React Native)

**Responsibilities:**
- iOS and Android app development
- Native module integration
- Mobile-specific UX
- App store deployment
- Performance optimization
- Push notification setup

**Required Skills:**
- 2+ years mobile development
- React Native experience
- iOS/Android platform knowledge
- App store deployment experience
- Mobile UX patterns
- Native debugging

**Key Deliverables:**
- Mobile app MVP
- iOS and Android builds
- App store listings
- Push notification integration

**Reporting:** Senior Frontend Engineer

**Salary Range:** $90K - $120K + equity (0.25-0.5%)

---

#### 7. DevOps Engineer

**Responsibilities:**
- AWS infrastructure setup
- CI/CD pipeline configuration
- Container orchestration (Docker, Kubernetes)
- Monitoring and alerting
- Deployment automation
- Security hardening

**Required Skills:**
- 3+ years DevOps experience
- AWS services (EC2, RDS, S3, CloudFront)
- Docker and Kubernetes
- Infrastructure as Code (Terraform)
- CI/CD tools (GitHub Actions)
- Monitoring (Prometheus, Grafana)

**Key Deliverables:**
- Production environment setup
- CI/CD pipelines
- Monitoring dashboards
- Backup and disaster recovery

**Reporting:** CTO

**Salary Range:** $110K - $140K + equity (0.5-1%)

---

#### 8. Product Manager

**Responsibilities:**
- Product roadmap planning
- Feature prioritization
- User story creation
- Sprint planning
- Stakeholder communication
- User feedback analysis

**Required Skills:**
- 3+ years product management
- SaaS experience preferred
- Agile/Scrum methodology
- User research
- Analytics (Mixpanel, Amplitude)
- Technical understanding

**Key Deliverables:**
- Product roadmap (18 months)
- Sprint backlogs
- User stories with acceptance criteria
- Product metrics dashboards

**Reporting:** CPO (or CEO in early stage)

**Salary Range:** $100K - $130K + equity (0.5-1%)

---

#### 9. Product Designer

**Responsibilities:**
- UI/UX design for web and mobile
- Design system creation
- User research and testing
- Wireframing and prototyping
- Design QA
- Design documentation

**Required Skills:**
- 3+ years product design
- Figma expertise
- User research methodologies
- Interaction design
- Visual design
- Prototyping

**Key Deliverables:**
- Design system in Figma
- High-fidelity mockups
- Interactive prototypes
- User flow diagrams

**Reporting:** Product Manager (or CPO)

**Salary Range:** $90K - $120K + equity (0.25-0.5%)

---

### Extended Team Roles (Phase 3-4: Months 7-12)

#### Additional Hires:
- **QA Lead** (Month 7) - Test automation, QA strategy
- **QA Engineer** (Month 8) - Test execution, bug tracking
- **Site Reliability Engineer** (Month 9) - Production reliability
- **Backend Engineer #3** (Month 10) - Scale team capacity
- **Frontend Engineer #2** (Month 11) - Scale team capacity
- **UX Researcher** (Month 12) - User research, usability testing

---

### Team Growth Timeline

```mermaid
gantt
    title Team Hiring Timeline (18 Months)
    dateFormat YYYY-MM-DD

    section Leadership
    CTO/Engineering Lead          :2025-11-01, 30d
    Product Manager              :2025-11-15, 30d

    section Backend Team
    Senior Backend Engineer      :2025-11-15, 30d
    Backend Engineer #1          :2025-12-01, 30d
    Backend Engineer #2          :2025-12-15, 30d
    Backend Engineer #3          :2026-10-01, 30d

    section Frontend Team
    Senior Frontend Engineer     :2025-12-01, 30d
    Frontend Engineer - Web      :2025-12-15, 30d
    Mobile Engineer              :2026-01-01, 30d
    Frontend Engineer #2         :2026-11-01, 30d

    section DevOps & SRE
    DevOps Engineer              :2025-12-01, 30d
    Site Reliability Engineer    :2026-09-01, 30d

    section Design
    Product Designer             :2025-11-15, 30d
    UX Researcher               :2026-12-01, 30d

    section QA
    QA Lead                      :2026-07-01, 30d
    QA Engineer - Automation     :2026-08-01, 30d

    section Milestones
    Core Team Complete           :milestone, 2026-01-31, 0d
    MVP Team Complete            :milestone, 2026-05-01, 0d
    Full Team Assembled          :milestone, 2027-01-01, 0d
```

---

### Team Size by Phase

**Phase 1 (Months 1-3):** 7 people
- 1 CTO/Engineering Lead
- 1 Senior Backend Engineer
- 2 Backend Engineers
- 1 Senior Frontend Engineer
- 1 DevOps Engineer
- 1 Product Manager

**Phase 2 (Months 4-6):** 9 people
- Add: 1 Frontend Engineer (Web)
- Add: 1 Mobile Engineer
- Add: 1 Product Designer

**Phase 3 (Months 7-9):** 12 people
- Add: 1 QA Lead
- Add: 1 QA Engineer
- Add: 1 Site Reliability Engineer

**Phase 4 (Months 10-12):** 14 people
- Add: 1 Backend Engineer #3
- Add: 1 Frontend Engineer #2

**Phase 5 (Months 13-18):** 16+ people
- Add: 1 UX Researcher
- Add: Additional engineers as needed

---

## Technology Stack Deep Dive

### Backend Architecture

#### Core Technologies

**1. Rust with Axum Framework**

Why Rust?
- Memory safety without garbage collection
- Performance comparable to C/C++
- Strong type system prevents many bugs
- Excellent concurrency model
- Growing ecosystem for web services

Why Axum?
- Built on Tokio (async runtime)
- Minimal boilerplate
- Type-safe routing
- Excellent middleware support
- Strong community support

**Example Service Structure:**
```rust
// src/main.rs
use axum::{
    routing::{get, post},
    Router,
    http::StatusCode,
    Json,
};
use tower_http::cors::CorsLayer;
use std::net::SocketAddr;

#[tokio::main]
async fn main() {
    // Initialize tracing
    tracing_subscriber::fmt::init();

    // Build application router
    let app = Router::new()
        .route("/health", get(health_check))
        .route("/api/v1/auth/login", post(login))
        .route("/api/v1/auth/register", post(register))
        .layer(CorsLayer::permissive());

    // Run server
    let addr = SocketAddr::from(([0, 0, 0, 0], 3000));
    tracing::info!("Server listening on {}", addr);

    axum::Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .unwrap();
}

async fn health_check() -> (StatusCode, &'static str) {
    (StatusCode::OK, "OK")
}
```

---

**2. PostgreSQL with PostGIS**

Database Configuration:
```sql
-- Connection settings
max_connections = 100
shared_buffers = 4GB
effective_cache_size = 12GB
work_mem = 64MB
maintenance_work_mem = 1GB

-- Performance tuning
random_page_cost = 1.1
effective_io_concurrency = 200
checkpoint_completion_target = 0.9

-- PostGIS extension
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;
```

**Migration Example (using sqlx):**
```sql
-- migrations/20251101000001_create_users.sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    role_id UUID REFERENCES roles(id),
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role_id ON users(role_id);

-- Trigger for updated_at
CREATE TRIGGER set_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

---

**3. Redis for Caching**

Cache Strategy:
```rust
use redis::AsyncCommands;

pub struct CacheService {
    client: redis::Client,
}

impl CacheService {
    pub async fn get_or_set<T, F>(
        &self,
        key: &str,
        ttl_seconds: usize,
        fetch_fn: F,
    ) -> Result<T>
    where
        T: serde::de::DeserializeOwned + serde::Serialize,
        F: FnOnce() -> Result<T>,
    {
        let mut con = self.client.get_async_connection().await?;

        // Try cache first
        if let Ok(cached) = con.get::<_, String>(key).await {
            if let Ok(value) = serde_json::from_str(&cached) {
                return Ok(value);
            }
        }

        // Fetch from source
        let value = fetch_fn()?;

        // Store in cache
        let serialized = serde_json::to_string(&value)?;
        let _: () = con.set_ex(key, serialized, ttl_seconds).await?;

        Ok(value)
    }
}
```

Cache Usage:
- Session tokens: 15 minutes TTL
- User permissions: 5 minutes TTL
- Product catalog: 1 hour TTL
- Dashboard aggregations: 5 minutes TTL

---

### Frontend Architecture

#### Web Application (React + TypeScript)

**Project Structure:**
```
frontend/web/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Table/
│   │   │   └── Modal/
│   │   ├── layout/
│   │   │   ├── Header/
│   │   │   ├── Sidebar/
│   │   │   └── Footer/
│   │   └── features/
│   │       ├── auth/
│   │       ├── accounting/
│   │       ├── warehouse/
│   │       └── pos/
│   ├── pages/
│   │   ├── Login/
│   │   ├── Dashboard/
│   │   ├── Sales/
│   │   └── Inventory/
│   ├── hooks/
│   │   ├── useAuth.ts
│   │   ├── useApi.ts
│   │   └── usePermissions.ts
│   ├── services/
│   │   ├── api.ts
│   │   ├── auth.ts
│   │   └── storage.ts
│   ├── store/
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── salesSlice.ts
│   │   │   └── inventorySlice.ts
│   │   └── store.ts
│   ├── types/
│   │   ├── models.ts
│   │   └── api.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── helpers.ts
│   ├── styles/
│   │   ├── theme.ts
│   │   └── globals.css
│   ├── App.tsx
│   └── main.tsx
├── package.json
├── tsconfig.json
├── vite.config.ts
└── .eslintrc.js
```

**State Management (Redux Toolkit):**
```typescript
// src/store/slices/authSlice.ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { authService } from '../../services/auth';
import type { User, LoginCredentials } from '../../types/models';

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
}

const initialState: AuthState = {
  user: null,
  token: localStorage.getItem('token'),
  isAuthenticated: false,
  isLoading: false,
  error: null,
};

export const login = createAsyncThunk(
  'auth/login',
  async (credentials: LoginCredentials) => {
    const response = await authService.login(credentials);
    return response.data;
  }
);

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    logout: (state) => {
      state.user = null;
      state.token = null;
      state.isAuthenticated = false;
      localStorage.removeItem('token');
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(login.pending, (state) => {
        state.isLoading = true;
        state.error = null;
      })
      .addCase(login.fulfilled, (state, action) => {
        state.isLoading = false;
        state.user = action.payload.user;
        state.token = action.payload.token;
        state.isAuthenticated = true;
        localStorage.setItem('token', action.payload.token);
      })
      .addCase(login.rejected, (state, action) => {
        state.isLoading = false;
        state.error = action.error.message || 'Login failed';
      });
  },
});

export const { logout } = authSlice.actions;
export default authSlice.reducer;
```

---

**API Service:**
```typescript
// src/services/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api/v1';

const api = axios.create({
  baseURL: API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor - add auth token
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor - handle errors
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Token expired or invalid
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

---

#### Mobile Application (React Native)

**Project Structure:**
```
frontend/mobile/
├── android/
├── ios/
├── src/
│   ├── components/
│   ├── screens/
│   │   ├── LoginScreen/
│   │   ├── DashboardScreen/
│   │   ├── POSScreen/
│   │   └── InventoryScreen/
│   ├── navigation/
│   │   ├── RootNavigator.tsx
│   │   └── AuthNavigator.tsx
│   ├── services/
│   ├── hooks/
│   ├── store/
│   ├── types/
│   └── utils/
├── package.json
└── app.json
```

---

### DevOps & Infrastructure

#### AWS Architecture

```mermaid
graph TB
    subgraph "Public Internet"
        USERS[Users]
        MOBILE[Mobile Apps]
    end

    subgraph "AWS Cloud"
        R53[Route 53<br/>DNS]
        CF[CloudFront<br/>CDN]

        subgraph "VPC - us-east-1"
            subgraph "Public Subnet"
                ALB[Application<br/>Load Balancer]
                NAT[NAT Gateway]
            end

            subgraph "Private Subnet - AZ1"
                API1[API Server<br/>EC2/ECS]
                REDIS1[Redis<br/>Primary]
            end

            subgraph "Private Subnet - AZ2"
                API2[API Server<br/>EC2/ECS]
                REDIS2[Redis<br/>Replica]
            end

            subgraph "Database Subnet"
                RDS_PRIMARY[(RDS PostgreSQL<br/>Primary)]
                RDS_STANDBY[(RDS PostgreSQL<br/>Standby)]
            end
        end

        S3[S3 Buckets<br/>Static Assets]
        SM[Secrets Manager]
        CW[CloudWatch<br/>Monitoring]
    end

    USERS --> R53
    MOBILE --> R53
    R53 --> CF
    CF --> ALB
    CF --> S3
    ALB --> API1
    ALB --> API2
    API1 --> REDIS1
    API2 --> REDIS2
    API1 --> RDS_PRIMARY
    API2 --> RDS_PRIMARY
    RDS_PRIMARY -.Replication.-> RDS_STANDBY
    REDIS1 -.Replication.-> REDIS2
    API1 --> S3
    API2 --> S3
    API1 --> SM
    API2 --> SM
    API1 --> CW
    API2 --> CW
```

---

#### Infrastructure as Code (Terraform)

**Example Configuration:**
```hcl
# infrastructure/terraform/main.tf

terraform {
  required_version = ">= 1.0"

  backend "s3" {
    bucket = "lazyfranchisor-terraform-state"
    key    = "production/terraform.tfstate"
    region = "us-east-1"
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name        = "lazyfranchisor-vpc"
    Environment = var.environment
  }
}

# RDS PostgreSQL
resource "aws_db_instance" "postgres" {
  identifier              = "lazyfranchisor-db"
  engine                  = "postgres"
  engine_version          = "15.3"
  instance_class          = "db.t3.large"
  allocated_storage       = 100
  storage_type            = "gp3"
  storage_encrypted       = true

  db_name  = "lazyfranchisor"
  username = var.db_username
  password = var.db_password

  multi_az               = true
  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "sun:04:00-sun:05:00"

  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name

  skip_final_snapshot = false
  final_snapshot_identifier = "lazyfranchisor-final-snapshot-${timestamp()}"

  tags = {
    Name        = "lazyfranchisor-postgres"
    Environment = var.environment
  }
}
```

---

## Development Environment Setup

### Prerequisites

**Required Software:**
- Git 2.40+
- Rust 1.70+ with Cargo
- Node.js 18+ with npm/yarn
- Docker Desktop
- PostgreSQL 15+ (local development)
- Redis 7+ (local development)
- VS Code or IntelliJ IDEA

---

### Local Setup Guide

#### 1. Clone Repository

```bash
# Clone the main repository
git clone https://github.com/lazyfranchisor/lazyfranchisor.git
cd lazyfranchisor

# Create feature branch
git checkout -b feature/your-feature-name
```

---

#### 2. Backend Setup (Rust)

```bash
cd backend

# Install Rust if not already installed
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Install required tools
cargo install sqlx-cli --no-default-features --features postgres
cargo install cargo-watch

# Copy environment template
cp .env.example .env

# Edit .env with your local configuration
# DATABASE_URL=postgres://postgres:password@localhost:5432/lazyfranchisor
# REDIS_URL=redis://localhost:6379
# JWT_SECRET=your-dev-secret-key

# Start local PostgreSQL and Redis
docker-compose up -d postgres redis

# Run migrations
sqlx database create
sqlx migrate run

# Run tests
cargo test

# Start development server (with auto-reload)
cargo watch -x run
```

**Backend will be available at:** `http://localhost:3000`

---

#### 3. Frontend Setup (Web)

```bash
cd frontend/web

# Install dependencies
npm install

# Copy environment template
cp .env.example .env.local

# Edit .env.local
# VITE_API_URL=http://localhost:3000/api/v1

# Start development server
npm run dev
```

**Frontend will be available at:** `http://localhost:5173`

---

#### 4. Mobile Setup (React Native)

```bash
cd frontend/mobile

# Install dependencies
npm install

# iOS setup (macOS only)
cd ios
pod install
cd ..

# Start Metro bundler
npm start

# Run on iOS simulator (macOS only)
npm run ios

# Run on Android emulator
npm run android
```

---

### Docker Development Environment

**Complete stack with Docker Compose:**

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: lazyfranchisor
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    command: >
      postgres
      -c shared_buffers=256MB
      -c max_connections=200

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:password@postgres:5432/lazyfranchisor
      REDIS_URL: redis://redis:6379
      RUST_LOG: debug
    volumes:
      - ./backend:/app
      - cargo_cache:/usr/local/cargo
    depends_on:
      - postgres
      - redis
    command: cargo watch -x run

  frontend:
    build:
      context: ./frontend/web
      dockerfile: Dockerfile.dev
    ports:
      - "5173:5173"
    environment:
      VITE_API_URL: http://localhost:3000/api/v1
    volumes:
      - ./frontend/web:/app
      - node_modules:/app/node_modules
    depends_on:
      - backend

volumes:
  postgres_data:
  redis_data:
  cargo_cache:
  node_modules:
```

**Usage:**
```bash
# Start entire stack
docker-compose up -d

# View logs
docker-compose logs -f

# Stop stack
docker-compose down

# Rebuild after changes
docker-compose up -d --build
```

---

## Coding Standards & Best Practices

### Rust Coding Standards

#### 1. Project Structure

```
backend/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── config/
│   │   └── mod.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── user.rs
│   │   └── sales.rs
│   ├── services/
│   │   ├── mod.rs
│   │   ├── auth_service.rs
│   │   └── sales_service.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── auth_handler.rs
│   │   └── sales_handler.rs
│   ├── middleware/
│   │   ├── mod.rs
│   │   ├── auth.rs
│   │   └── logging.rs
│   ├── utils/
│   │   ├── mod.rs
│   │   └── validators.rs
│   └── errors.rs
├── migrations/
└── tests/
    ├── integration/
    └── fixtures/
```

---

#### 2. Error Handling

**Define Custom Error Types:**
```rust
// src/errors.rs
use axum::{
    http::StatusCode,
    response::{IntoResponse, Response},
    Json,
};
use serde_json::json;

#[derive(Debug)]
pub enum AppError {
    DatabaseError(sqlx::Error),
    NotFound(String),
    Unauthorized,
    ValidationError(String),
    InternalError(String),
}

impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        let (status, error_message) = match self {
            AppError::DatabaseError(e) => {
                tracing::error!("Database error: {:?}", e);
                (StatusCode::INTERNAL_SERVER_ERROR, "Database error")
            }
            AppError::NotFound(msg) => (StatusCode::NOT_FOUND, msg.as_str()),
            AppError::Unauthorized => (StatusCode::UNAUTHORIZED, "Unauthorized"),
            AppError::ValidationError(msg) => (StatusCode::BAD_REQUEST, msg.as_str()),
            AppError::InternalError(msg) => {
                tracing::error!("Internal error: {}", msg);
                (StatusCode::INTERNAL_SERVER_ERROR, "Internal server error")
            }
        };

        let body = Json(json!({
            "error": error_message,
        }));

        (status, body).into_response()
    }
}

impl From<sqlx::Error> for AppError {
    fn from(err: sqlx::Error) -> Self {
        AppError::DatabaseError(err)
    }
}

pub type Result<T> = std::result::Result<T, AppError>;
```

---

#### 3. Naming Conventions

- **Files:** snake_case (e.g., `auth_service.rs`)
- **Structs/Enums:** PascalCase (e.g., `UserModel`, `AuthError`)
- **Functions/Variables:** snake_case (e.g., `get_user_by_id`)
- **Constants:** SCREAMING_SNAKE_CASE (e.g., `MAX_CONNECTIONS`)
- **Traits:** PascalCase (e.g., `UserRepository`)

**Example:**
```rust
const MAX_LOGIN_ATTEMPTS: u32 = 5;

pub struct UserService {
    repository: Arc<dyn UserRepository>,
}

impl UserService {
    pub async fn authenticate_user(
        &self,
        email: &str,
        password: &str,
    ) -> Result<User> {
        // Implementation
    }
}
```

---

#### 4. Documentation

**Always document public APIs:**
```rust
/// Authenticates a user with email and password.
///
/// # Arguments
///
/// * `email` - User's email address
/// * `password` - Plain text password (will be hashed)
///
/// # Returns
///
/// Returns `Ok(User)` if authentication succeeds, or `Err(AppError)` otherwise.
///
/// # Errors
///
/// This function will return an error if:
/// * User not found with the given email
/// * Password does not match
/// * Database connection fails
///
/// # Example
///
/// ```
/// let user = auth_service.authenticate_user("user@example.com", "password").await?;
/// ```
pub async fn authenticate_user(
    &self,
    email: &str,
    password: &str,
) -> Result<User> {
    // Implementation
}
```

---

#### 5. Testing

**Unit Tests:**
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_authenticate_user_success() {
        let mock_repo = MockUserRepository::new();
        let service = UserService::new(Arc::new(mock_repo));

        let result = service
            .authenticate_user("test@example.com", "password")
            .await;

        assert!(result.is_ok());
    }

    #[tokio::test]
    async fn test_authenticate_user_wrong_password() {
        let mock_repo = MockUserRepository::new();
        let service = UserService::new(Arc::new(mock_repo));

        let result = service
            .authenticate_user("test@example.com", "wrongpassword")
            .await;

        assert!(matches!(result, Err(AppError::Unauthorized)));
    }
}
```

**Integration Tests:**
```rust
// tests/integration/auth_tests.rs
use lazyfranchisor::{create_app, config::Config};
use axum::body::Body;
use axum::http::{Request, StatusCode};
use tower::ServiceExt;

#[tokio::test]
async fn test_login_endpoint() {
    let config = Config::test();
    let app = create_app(config).await;

    let response = app
        .oneshot(
            Request::builder()
                .method("POST")
                .uri("/api/v1/auth/login")
                .header("content-type", "application/json")
                .body(Body::from(
                    r#"{"email":"test@example.com","password":"password"}"#
                ))
                .unwrap()
        )
        .await
        .unwrap();

    assert_eq!(response.status(), StatusCode::OK);
}
```

---

### TypeScript/React Coding Standards

#### 1. Component Structure

**Functional Components with TypeScript:**
```typescript
// src/components/features/sales/SalesForm.tsx
import React, { useState } from 'react';
import { useDispatch } from 'react-redux';
import { createSale } from '../../../store/slices/salesSlice';
import { Button, Input, Form } from '../../common';
import type { SaleFormData } from '../../../types/models';

interface SalesFormProps {
  onSuccess?: () => void;
  onCancel?: () => void;
}

export const SalesForm: React.FC<SalesFormProps> = ({ onSuccess, onCancel }) => {
  const dispatch = useDispatch();
  const [formData, setFormData] = useState<SaleFormData>({
    amount: 0,
    description: '',
    date: new Date().toISOString(),
  });
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setIsSubmitting(true);
    setError(null);

    try {
      await dispatch(createSale(formData)).unwrap();
      onSuccess?.();
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <Form onSubmit={handleSubmit}>
      <Input
        label="Amount"
        type="number"
        value={formData.amount}
        onChange={(e) => setFormData({ ...formData, amount: parseFloat(e.target.value) })}
        required
      />

      <Input
        label="Description"
        value={formData.description}
        onChange={(e) => setFormData({ ...formData, description: e.target.value })}
      />

      {error && <div className="error">{error}</div>}

      <div className="button-group">
        <Button type="submit" disabled={isSubmitting}>
          {isSubmitting ? 'Submitting...' : 'Submit'}
        </Button>
        <Button type="button" variant="secondary" onClick={onCancel}>
          Cancel
        </Button>
      </div>
    </Form>
  );
};
```

---

#### 2. Custom Hooks

```typescript
// src/hooks/useApi.ts
import { useState, useEffect } from 'react';
import api from '../services/api';

interface UseApiOptions<T> {
  initialData?: T;
  skip?: boolean;
}

export function useApi<T>(
  endpoint: string,
  options: UseApiOptions<T> = {}
) {
  const [data, setData] = useState<T | undefined>(options.initialData);
  const [isLoading, setIsLoading] = useState(!options.skip);
  const [error, setError] = useState<Error | null>(null);

  const refetch = async () => {
    setIsLoading(true);
    setError(null);

    try {
      const response = await api.get<T>(endpoint);
      setData(response.data);
    } catch (err) {
      setError(err instanceof Error ? err : new Error('Unknown error'));
    } finally {
      setIsLoading(false);
    }
  };

  useEffect(() => {
    if (!options.skip) {
      refetch();
    }
  }, [endpoint, options.skip]);

  return { data, isLoading, error, refetch };
}
```

---

#### 3. Type Definitions

```typescript
// src/types/models.ts

export interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  roleId: string;
  role?: Role;
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
}

export interface Role {
  id: string;
  name: string;
  permissions: Permission[];
}

export interface Permission {
  id: string;
  resource: string;
  action: 'create' | 'read' | 'update' | 'delete';
}

export interface SalesTransaction {
  id: string;
  franchiseeId: string;
  totalAmount: number;
  paymentMethod: 'cash' | 'card' | 'digital';
  status: 'completed' | 'pending' | 'cancelled';
  lineItems: SalesLineItem[];
  createdAt: string;
}

export interface SalesLineItem {
  id: string;
  transactionId: string;
  productId: string;
  product?: Product;
  quantity: number;
  unitPrice: number;
  subtotal: number;
  discount: number;
}

export interface Product {
  id: string;
  sku: string;
  name: string;
  description: string;
  categoryId: string;
  unitPrice: number;
  isActive: boolean;
}

// API Response types
export interface ApiResponse<T> {
  data: T;
  message?: string;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

export interface ApiError {
  error: string;
  details?: Record<string, string[]>;
}
```

---

#### 4. ESLint Configuration

```javascript
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 2022,
    sourceType: 'module',
    ecmaFeatures: {
      jsx: true,
    },
  },
  plugins: ['@typescript-eslint', 'react', 'react-hooks'],
  rules: {
    '@typescript-eslint/explicit-function-return-type': 'off',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    'react/react-in-jsx-scope': 'off',
    'react/prop-types': 'off',
    'no-console': ['warn', { allow: ['warn', 'error'] }],
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
};
```

---

## Git Workflow & Branching Strategy

### Branch Naming Convention

```
feature/    - New features (feature/user-authentication)
bugfix/     - Bug fixes (bugfix/login-error-handling)
hotfix/     - Production hotfixes (hotfix/critical-security-patch)
release/    - Release branches (release/v1.0.0)
refactor/   - Code refactoring (refactor/api-structure)
docs/       - Documentation updates (docs/api-documentation)
test/       - Test additions (test/sales-integration-tests)
```

---

### Gitflow Workflow

```mermaid
gitGraph
    commit id: "Initial commit"
    branch develop
    checkout develop
    commit id: "Setup project"

    branch feature/auth
    checkout feature/auth
    commit id: "Add login endpoint"
    commit id: "Add registration"
    checkout develop
    merge feature/auth

    branch feature/sales
    checkout feature/sales
    commit id: "Add sales model"
    commit id: "Add sales API"
    checkout develop
    merge feature/sales

    branch release/v1.0.0
    checkout release/v1.0.0
    commit id: "Bump version"
    commit id: "Update changelog"

    checkout main
    merge release/v1.0.0 tag: "v1.0.0"

    checkout develop
    merge release/v1.0.0

    checkout main
    branch hotfix/security-patch
    commit id: "Fix vulnerability"
    checkout main
    merge hotfix/security-patch tag: "v1.0.1"

    checkout develop
    merge hotfix/security-patch
```

---

### Commit Message Convention

**Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `perf`: Performance improvements
- `test`: Test additions or modifications
- `chore`: Build process or tooling changes
- `ci`: CI/CD changes

**Examples:**
```bash
# Feature
git commit -m "feat(auth): add JWT token refresh endpoint

Implemented automatic token refresh to improve UX.
Tokens are now refreshed 5 minutes before expiration.

Closes #123"

# Bug fix
git commit -m "fix(sales): correct tax calculation for multi-state transactions

Fixed rounding error in tax calculation that caused
discrepancies in reports.

Fixes #456"

# Breaking change
git commit -m "feat(api): change authentication to use OAuth2

BREAKING CHANGE: API authentication now requires OAuth2.
Old JWT tokens will no longer work after v2.0.0.

Migration guide: docs/migration-v2.md"
```

---

### Pull Request Process

#### 1. Create Feature Branch

```bash
git checkout develop
git pull origin develop
git checkout -b feature/sales-dashboard
```

---

#### 2. Make Changes & Commit

```bash
# Make changes
# ...

# Stage changes
git add .

# Commit with conventional message
git commit -m "feat(dashboard): add sales summary cards"

# Push to remote
git push -u origin feature/sales-dashboard
```

---

#### 3. Create Pull Request

**PR Template:**
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Related Issues
Closes #123

## Changes Made
- Added sales summary cards to dashboard
- Implemented real-time updates
- Added loading states

## Screenshots (if applicable)
[Add screenshots here]

## Testing
- [ ] Unit tests pass (`cargo test` / `npm test`)
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Code coverage >80%

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests added/updated
```

---

#### 4. Code Review

**Reviewer Checklist:**
- [ ] Code quality and readability
- [ ] Tests coverage and quality
- [ ] Performance considerations
- [ ] Security vulnerabilities
- [ ] Error handling
- [ ] Documentation
- [ ] Breaking changes identified

**Review Comments:**
- Use GitHub's suggestion feature
- Be constructive and specific
- Praise good solutions
- Request changes only when necessary

---

#### 5. Merge Strategy

**Squash and Merge** (preferred for features):
```bash
# All commits squashed into one
git checkout develop
git merge --squash feature/sales-dashboard
git commit -m "feat(dashboard): add sales summary cards (#123)"
```

**Merge Commit** (for release branches):
```bash
git checkout main
git merge --no-ff release/v1.0.0
git tag -a v1.0.0 -m "Release v1.0.0"
```

---

## Code Review Process

### Code Review Guidelines

#### What to Look For

**1. Functionality**
- Does the code do what it's supposed to do?
- Are edge cases handled?
- Is error handling appropriate?

**2. Code Quality**
- Is the code readable and maintainable?
- Are variable/function names descriptive?
- Is the code DRY (Don't Repeat Yourself)?
- Is complexity minimized?

**3. Testing**
- Are there sufficient tests?
- Do tests cover edge cases?
- Are tests readable and maintainable?

**4. Security**
- Are inputs validated?
- Is sensitive data protected?
- Are there SQL injection vulnerabilities?
- Is authentication/authorization correct?

**5. Performance**
- Are there obvious performance issues?
- Are database queries optimized?
- Is caching used appropriately?

**6. Documentation**
- Are public APIs documented?
- Are complex algorithms explained?
- Is README updated if needed?

---

### Code Review Etiquette

**For Authors:**
- Keep PRs small and focused
- Provide context in PR description
- Respond promptly to feedback
- Don't take criticism personally
- Ask questions if feedback is unclear

**For Reviewers:**
- Review promptly (within 24 hours)
- Be respectful and constructive
- Explain the "why" behind suggestions
- Distinguish between required changes and suggestions
- Praise good solutions

**Example Comments:**

❌ **Bad:**
```
This code is bad. Rewrite it.
```

✅ **Good:**
```
Consider using a HashMap here instead of iterating through the Vec.
This would improve lookup time from O(n) to O(1).

Example:
```rust
let user_map: HashMap<Uuid, User> = users
    .into_iter()
    .map(|u| (u.id, u))
    .collect();
```
```

---

## Testing Strategy

### Test Pyramid

```
           /\
          /  \
         / E2E\         ~5% - End-to-End Tests
        /------\
       /        \
      /Integration\    ~15% - Integration Tests
     /------------\
    /              \
   /  Unit Tests   \   ~80% - Unit Tests
  /________________\
```

---

### Backend Testing (Rust)

#### Unit Tests

```rust
// src/services/sales_service.rs

pub struct SalesService {
    repository: Arc<dyn SalesRepository>,
}

impl SalesService {
    pub fn calculate_total(&self, line_items: &[LineItem]) -> Decimal {
        line_items.iter()
            .map(|item| item.quantity * item.unit_price - item.discount)
            .sum()
    }
}

#[cfg(test)]
mod tests {
    use super::*;
    use rust_decimal_macros::dec;

    #[test]
    fn test_calculate_total_single_item() {
        let service = SalesService::new(Arc::new(MockSalesRepository::new()));
        let items = vec![LineItem {
            quantity: dec!(2),
            unit_price: dec!(10.50),
            discount: dec!(1.00),
        }];

        let total = service.calculate_total(&items);

        assert_eq!(total, dec!(20.00)); // (2 * 10.50) - 1.00
    }

    #[test]
    fn test_calculate_total_multiple_items() {
        let service = SalesService::new(Arc::new(MockSalesRepository::new()));
        let items = vec![
            LineItem {
                quantity: dec!(2),
                unit_price: dec!(10.00),
                discount: dec!(0),
            },
            LineItem {
                quantity: dec!(1),
                unit_price: dec!(5.00),
                discount: dec!(0.50),
            },
        ];

        let total = service.calculate_total(&items);

        assert_eq!(total, dec!(24.50)); // 20.00 + 4.50
    }
}
```

---

#### Integration Tests

```rust
// tests/integration/sales_tests.rs

use lazyfranchisor::create_app;
use sqlx::PgPool;
use axum::body::Body;
use axum::http::{Request, StatusCode};
use tower::ServiceExt;

async fn setup_database() -> PgPool {
    let pool = PgPool::connect("postgres://localhost/test_db").await.unwrap();
    sqlx::migrate!("./migrations").run(&pool).await.unwrap();
    pool
}

#[tokio::test]
async fn test_create_sale_endpoint() {
    let pool = setup_database().await;
    let app = create_app(pool.clone()).await;

    let request_body = json!({
        "franchisee_id": "123e4567-e89b-12d3-a456-426614174000",
        "payment_method": "cash",
        "line_items": [
            {
                "product_id": "223e4567-e89b-12d3-a456-426614174000",
                "quantity": 2,
                "unit_price": 10.50
            }
        ]
    });

    let response = app
        .oneshot(
            Request::builder()
                .method("POST")
                .uri("/api/v1/sales")
                .header("content-type", "application/json")
                .header("authorization", "Bearer test-token")
                .body(Body::from(request_body.to_string()))
                .unwrap()
        )
        .await
        .unwrap();

    assert_eq!(response.status(), StatusCode::CREATED);

    // Cleanup
    sqlx::query("TRUNCATE TABLE sales CASCADE")
        .execute(&pool)
        .await
        .unwrap();
}
```

---

### Frontend Testing (React)

#### Component Tests

```typescript
// src/components/features/sales/SalesForm.test.tsx

import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import { SalesForm } from './SalesForm';
import salesReducer from '../../../store/slices/salesSlice';

const createMockStore = () => configureStore({
  reducer: {
    sales: salesReducer,
  },
});

describe('SalesForm', () => {
  it('renders form fields', () => {
    const store = createMockStore();
    render(
      <Provider store={store}>
        <SalesForm />
      </Provider>
    );

    expect(screen.getByLabelText('Amount')).toBeInTheDocument();
    expect(screen.getByLabelText('Description')).toBeInTheDocument();
    expect(screen.getByText('Submit')).toBeInTheDocument();
  });

  it('submits form with valid data', async () => {
    const store = createMockStore();
    const onSuccess = jest.fn();

    render(
      <Provider store={store}>
        <SalesForm onSuccess={onSuccess} />
      </Provider>
    );

    fireEvent.change(screen.getByLabelText('Amount'), {
      target: { value: '100' },
    });
    fireEvent.change(screen.getByLabelText('Description'), {
      target: { value: 'Test sale' },
    });

    fireEvent.click(screen.getByText('Submit'));

    await waitFor(() => {
      expect(onSuccess).toHaveBeenCalled();
    });
  });

  it('displays error on submission failure', async () => {
    const store = createMockStore();

    // Mock API failure
    jest.spyOn(global, 'fetch').mockRejectedValueOnce(new Error('API Error'));

    render(
      <Provider store={store}>
        <SalesForm />
      </Provider>
    );

    fireEvent.click(screen.getByText('Submit'));

    await waitFor(() => {
      expect(screen.getByText('API Error')).toBeInTheDocument();
    });
  });
});
```

---

#### Hook Tests

```typescript
// src/hooks/useApi.test.ts

import { renderHook, waitFor } from '@testing-library/react';
import { useApi } from './useApi';

global.fetch = jest.fn();

describe('useApi', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('fetches data successfully', async () => {
    const mockData = { id: 1, name: 'Test' };
    (fetch as jest.Mock).mockResolvedValueOnce({
      ok: true,
      json: async () => mockData,
    });

    const { result } = renderHook(() => useApi('/api/test'));

    expect(result.current.isLoading).toBe(true);

    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
      expect(result.current.data).toEqual(mockData);
      expect(result.current.error).toBeNull();
    });
  });

  it('handles errors correctly', async () => {
    (fetch as jest.Mock).mockRejectedValueOnce(new Error('Network error'));

    const { result } = renderHook(() => useApi('/api/test'));

    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
      expect(result.current.data).toBeUndefined();
      expect(result.current.error).toEqual(new Error('Network error'));
    });
  });
});
```

---

### Test Coverage Requirements

**Minimum Coverage:**
- Overall: 80%
- New features: 90%
- Critical paths: 100%

**Measuring Coverage:**

```bash
# Rust
cargo tarpaulin --out Html

# TypeScript/React
npm run test -- --coverage
```

---

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
# .github/workflows/ci.yml

name: CI Pipeline

on:
  push:
    branches: [develop, main]
  pull_request:
    branches: [develop, main]

env:
  CARGO_TERM_COLOR: always
  RUST_BACKTRACE: 1

jobs:
  test-backend:
    name: Test Backend (Rust)
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v3

      - name: Install Rust
        uses: actions-rs/toolchain@v1
        with:
          profile: minimal
          toolchain: stable
          override: true
          components: rustfmt, clippy

      - name: Cache cargo registry
        uses: actions/cache@v3
        with:
          path: ~/.cargo/registry
          key: ${{ runner.os }}-cargo-registry-${{ hashFiles('**/Cargo.lock') }}

      - name: Cache cargo index
        uses: actions/cache@v3
        with:
          path: ~/.cargo/git
          key: ${{ runner.os }}-cargo-index-${{ hashFiles('**/Cargo.lock') }}

      - name: Cache cargo build
        uses: actions/cache@v3
        with:
          path: target
          key: ${{ runner.os }}-cargo-build-target-${{ hashFiles('**/Cargo.lock') }}

      - name: Check formatting
        run: cargo fmt --all -- --check
        working-directory: ./backend

      - name: Run clippy
        run: cargo clippy --all-targets --all-features -- -D warnings
        working-directory: ./backend

      - name: Run migrations
        run: |
          cargo install sqlx-cli --no-default-features --features postgres
          sqlx database create
          sqlx migrate run
        working-directory: ./backend
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test_db

      - name: Run tests
        run: cargo test --all-features
        working-directory: ./backend
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test_db
          REDIS_URL: redis://localhost:6379

      - name: Generate coverage
        run: |
          cargo install cargo-tarpaulin
          cargo tarpaulin --out Xml
        working-directory: ./backend
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test_db

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./backend/cobertura.xml
          flags: backend

  test-frontend:
    name: Test Frontend (React)
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
          cache-dependency-path: frontend/web/package-lock.json

      - name: Install dependencies
        run: npm ci
        working-directory: ./frontend/web

      - name: Run ESLint
        run: npm run lint
        working-directory: ./frontend/web

      - name: Run tests
        run: npm test -- --coverage
        working-directory: ./frontend/web

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./frontend/web/coverage/coverage-final.json
          flags: frontend

      - name: Build
        run: npm run build
        working-directory: ./frontend/web

  build-docker:
    name: Build Docker Images
    runs-on: ubuntu-latest
    needs: [test-backend, test-frontend]
    if: github.event_name == 'push'

    steps:
      - uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push backend
        uses: docker/build-push-action@v4
        with:
          context: ./backend
          push: true
          tags: lazyfranchisor/backend:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

      - name: Build and push frontend
        uses: docker/build-push-action@v4
        with:
          context: ./frontend/web
          push: true
          tags: lazyfranchisor/frontend:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build-docker]
    if: github.ref == 'refs/heads/develop'
    environment:
      name: staging
      url: https://staging.lazyfranchisor.com

    steps:
      - uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Deploy to ECS
        run: |
          # Update ECS service with new task definition
          aws ecs update-service \
            --cluster lazyfranchisor-staging \
            --service api \
            --force-new-deployment

      - name: Notify Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Staging deployment completed'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()
```

---

*(Document continues with Sprint Planning, Architecture Decision Records, Performance Guidelines, Security Guidelines, Deployment Procedures, Monitoring & Debugging, and Team Communication sections...)*

---

**Document Control:**
- **Version:** 1.0
- **Last Updated:** October 29, 2025
- **Next Review:** Monthly
- **Owner:** CTO / Engineering Lead

*End of Development Team Handbook - Part 1*
