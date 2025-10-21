## Development Guidelines: LazyFranchisor

**1. Introduction**
*   **1.1 Purpose of this Document:**
    This document serves as a comprehensive guide for all developers contributing to the LazyFranchisor project. It outlines the technical stack, project setup procedures, coding standards, architectural principles, and best practices to ensure consistency, quality, and maintainability across the entire codebase.
*   **1.2 Core Principles:**
    *   **Clean Code:** Write code that is easy to read, understand, and modify.
    *   **Test-Driven Development (TDD):** Prioritize writing tests before or alongside feature development to ensure correctness and prevent regressions.
    *   **Security First:** Implement security best practices at every layer of the application.
    *   **Modularity:** Design components and services to be loosely coupled and highly cohesive, facilitating the phased expansion into a comprehensive feature set (Accounting, Auditing, SOP, SCM, ERP, CRM, POS, Warehouse Management, Central Kitchen Management).
    *   **Performance:** Optimize for speed and efficiency, especially in critical paths.
    *   **Documentation:** Maintain clear and concise documentation for code, APIs, and processes.

**2. Project Setup**
*   **2.1 Prerequisites:**
    Before starting development, ensure the following tools are installed on your system:
    *   **Rust:** Install `rustup` from [https://rustup.rs/](https://rustup.rs/).
    *   **Node.js & npm/Yarn:** Install Node.js (LTS version) which includes npm. Yarn is also recommended.
    *   **Docker & Docker Compose:** Install Docker Desktop (or Docker Engine + Docker Compose) from [https://www.docker.com/get-started](https://www.docker.com/get-started).
    *   **Git:** Install Git from [https://git-scm.com/downloads](https://git-scm.com/downloads).
    *   **PostgreSQL Client:** A command-line client or GUI tool (e.g., `psql`, DBeaver, pgAdmin) for database interaction.
*   **2.2 Repository Structure:**
    The LazyFranchisor project will adopt a monorepo strategy to manage all related projects (backend, web frontend, mobile frontend, desktop frontend) within a single Git repository.
    *   `/backend`: Rust/Axum services
    *   `/frontend/web`: React/TypeScript web application
    *   `/frontend/mobile`: React Native/TypeScript mobile application
    *   `/frontend/desktop`: Electron desktop application (likely wrapping the web app)
    *   `/docs`: Project documentation (including this guide)
    *   `/docker`: Docker-related files (Dockerfiles, docker-compose.yml)
    *   `/scripts`: Utility scripts
*   **2.3 Local Development Environment Setup:**
    1.  **Clone the Repository:**
        ```bash
git clone [repository-url]
cd LazyFranchisor
        ```
    2.  **Start Services with Docker Compose:**
        Navigate to the `/docker` directory and run:
        ```bash
docker compose up -d --build
        ```
        This will start the PostgreSQL database (with PostGIS), Redis, and the backend service.
    3.  **Backend Setup:**
        *   Navigate to `/backend`.
        *   Run database migrations: `sqlx migrate run` (or similar command once `sqlx-cli` is set up).
        *   Start the backend service (if not already started by Docker Compose, or for local development outside Docker): `cargo run`
    4.  **Frontend Setup (Web):**
        *   Navigate to `/frontend/web`.
        *   Install dependencies: `npm install` or `yarn install`
        *   Start the development server: `npm start` or `yarn start`
    5.  **Frontend Setup (Mobile):**
        *   Navigate to `/frontend/mobile`.
        *   Install dependencies: `npm install` or `yarn install`
        *   Follow React Native setup instructions for your specific OS and target (iOS/Android).
        *   Start the development server: `npm start` or `yarn start`
    6.  **Frontend Setup (Desktop):**
        *   Navigate to `/frontend/desktop`.
        *   Install dependencies: `npm install` or `yarn install`
        *   Start the application: `npm start` or `yarn start`

**3. Backend (Rust/Axum)**

*   **3.1 Coding Standards:**
    *   Adhere to `rustfmt` for code formatting. Run `cargo fmt` before committing.
    *   Use `clippy` for linting and best practices. Run `cargo clippy` regularly.
    *   Follow Rust's official API Guidelines where applicable.
*   **3.2 Project Structure:**
    *   Organize code into logical modules (e.g., `src/models`, `src/handlers`, `src/services`, `src/db`).
    *   Each microservice should reside in its own crate within the `/backend` directory.
*   **3.3 Database Interactions (`sqlx`):**
    *   Use `sqlx` for all database operations. Leverage its compile-time query checking.
    *   Define database schemas using `sqlx` migrations.
    *   Avoid raw SQL queries directly in application logic; encapsulate them within dedicated data access layers.
*   **3.4 Error Handling Strategy:**
    *   Use Rust's `Result<T, E>` enum for fallible operations.
    *   Define custom error types for application-specific errors.
    *   Map internal errors to appropriate HTTP status codes in API handlers.
*   **3.5 Testing:**
    *   **Unit Tests:** Write unit tests for individual functions and modules. Place them in the same file as the code, within `#[cfg(test)]` blocks.
    *   **Integration Tests:** Write integration tests for API endpoints and service interactions. Place them in `tests/` directory.
    *   Run tests with `cargo test`.
*   **3.6 API Design Principles:**
    *   **RESTful:** Design APIs to be RESTful, using standard HTTP methods (GET, POST, PUT, DELETE) and clear resource-based URLs (e.g., `/api/v1/franchisees`, `/api/v1/sales`).
    *   **Versioning:** Use API versioning (e.g., `/api/v1/`) to allow for future changes without breaking existing clients.
    *   **JSON:** Use JSON for request and response bodies.
    *   **Clear Error Messages:** Provide informative error messages with appropriate HTTP status codes.
*   **3.7 Security Best Practices:**
    *   **Authentication:** Implement JWT-based authentication for all protected endpoints.
    *   **Authorization:** Implement Role-Based Access Control (RBAC) to restrict access based on user roles.
    *   **Input Validation:** Validate all incoming API requests to prevent common vulnerabilities (e.g., SQL injection, XSS).
    *   **Password Hashing:** Store user passwords using strong, modern hashing algorithms (e.g., Argon2, bcrypt).
    *   **Environment Variables:** Store sensitive configuration (e.g., database credentials, JWT secrets) in environment variables, not directly in code.

**4. Frontend (React/React Native/Electron with TypeScript)**

*   **4.1 Coding Standards:**
    *   Adhere to `ESLint` and `Prettier` for code formatting and linting.
    *   Follow React best practices (e.g., functional components, hooks).
*   **4.2 Component Structure and Naming Conventions:**
    *   Organize components into logical directories (e.g., `components`, `pages`, `features`).
    *   Use PascalCase for component names (e.g., `FranchiseeList`, `SalesDashboard`).
    *   Use camelCase for file names (e.g., `franchiseeList.tsx`).
*   **4.3 State Management Strategy:**
    *   For local component state, use React's `useState` and `useReducer` hooks.
    *   For global application state, consider `React Context` for simpler cases or libraries like `Zustand` or `Redux Toolkit` for more complex scenarios.
*   **4.4 Styling Guidelines:**
    *   Use a consistent styling approach (e.g., Tailwind CSS, Styled Components, CSS Modules).
    *   Ensure responsiveness and accessibility across different screen sizes and devices.
*   **4.5 API Integration:**
    *   Use `fetch` API or a library like `Axios` for making HTTP requests to the Rust backend.
    *   Handle API errors gracefully and provide user feedback.
    *   Use TypeScript interfaces/types to define API request and response structures.
*   **4.6 Testing:**
    *   **Unit Tests:** Use `Jest` and `React Testing Library` for testing individual components and utility functions.
    *   **Integration Tests:** Test interactions between components and API calls.
    *   **End-to-End (E2E) Tests:** Consider tools like `Cypress` or `Playwright` for testing full user flows.
*   **4.7 Accessibility Guidelines:**
    *   Ensure all UI elements are accessible to users with disabilities (e.g., proper ARIA attributes, keyboard navigation).

**5. Database (PostgreSQL/PostGIS)**

*   **5.1 Schema Design Principles:**
    *   Follow relational database normalization principles.
    *   Use clear and consistent naming conventions for tables and columns (e.g., `snake_case`).
    *   Define appropriate primary keys, foreign keys, and indexes.
*   **5.2 Migration Strategy:**
    *   Use `sqlx-cli` for managing database migrations.
    *   Each schema change should be a separate, versioned migration.
    *   Migrations should be reversible where possible.
*   **5.3 Spatial Data Best Practices:**
    *   Use PostGIS data types (e.g., `GEOMETRY(Point, 4326)`) for storing geographical data.
    *   Leverage PostGIS functions for spatial queries (e.g., distance calculations, bounding box searches).
    *   Create spatial indexes (e.g., GiST indexes) for performance on spatial queries.

**6. Containerization (Docker)**

*   **6.1 Dockerfile Best Practices:**
    *   Use multi-stage builds to create smaller, more secure images.
    *   Minimize the number of layers.
    *   Use specific base images (e.g., `rust:slim-buster`, `node:lts-alpine`).
    *   Copy only necessary files into the image.
    *   Run applications as non-root users.
*   **6.2 Docker Compose Usage:**
    *   Use `docker-compose.yml` for defining and running multi-container Docker applications for local development.
    *   Define services for backend, database, Redis, and potentially frontend development servers.

**7. Version Control (Git)**

*   **7.1 Branching Strategy:**
    *   Adopt a `GitHub Flow` or `Git Flow` strategy. For an MVP, GitHub Flow (main branch always deployable, feature branches for new work) is often simpler and faster.
    *   Feature branches should be short-lived and merged back into `main` frequently.
*   **7.2 Commit Message Guidelines:**
    *   Use clear, concise, and descriptive commit messages.
    *   Follow a conventional commit format (e.g., `feat: add new feature`, `fix: resolve bug`).

**8. CI/CD (Continuous Integration/Continuous Deployment)**

*   **8.1 Pipeline Overview:**
    *   Automated build, test, and deployment pipelines will be set up (e.g., using Jenkins, GitHub Actions, or GitLab CI).
    *   Each push to a feature branch should trigger unit and integration tests.
    *   Merges to `main` should trigger full test suites, build, and deployment to staging/production environments.
*   **8.2 Deployment Strategy:**
    *   Container-based deployment to cloud platforms (e.g., AWS, GCP, Azure).
    *   Automated rollbacks in case of deployment failures.

**9. Code Review Process**

*   All code changes must be submitted via Pull Requests (PRs).
*   PRs require at least one (or two, depending on complexity) approved review(s) before merging.
*   Reviewers should check for adherence to coding standards, architectural patterns, test coverage, and overall code quality.

**10. Troubleshooting & Support**

*   Refer to the project's `README.md` for common issues and solutions.
*   For specific technical challenges, consult team leads or senior developers.
*   Utilize project communication channels (e.g., Slack, Teams) for questions and discussions.