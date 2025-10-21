# LazyFranchisor: Multi-Platform Franchising Management System

## Project Overview

LazyFranchisor is a cutting-edge, multi-platform franchising management system designed to streamline operations, enhance oversight, and drive growth for both franchisors and their franchisees. This Minimum Viable Product (MVP) focuses on core functionalities to deliver immediate value and establish a robust, scalable platform for future expansion.

Our vision is to empower franchisors and franchisees with an intuitive, integrated, and efficient system that simplifies daily operations, improves data visibility, and fosters seamless collaboration across the entire network.

## Key Features (MVP)

The initial release of LazyFranchisor focuses on the following essential modules:

*   **Accounting:**
    *   Centralized tracking of sales and revenue across the franchise network.
    *   Basic reporting capabilities for financial insights (e.g., sales summaries, revenue trends).
*   **Warehouse Management:**
    *   Streamlined order fulfillment processes (picking and packing).
    *   Efficient management of stock transfers between central warehouses and individual franchisee locations.
*   **Point of Sale (POS):**
    *   Intuitive processing of sales transactions.
    *   Flexible application and management of discounts and promotions.
    *   Real-time integration with inventory levels for accurate stock management.
*   **Geo-location Support:**
    *   Foundation for managing franchisee locations geographically, enabling future features like delivery tracking and location-based services.

## Technology Stack

LazyFranchisor is built with a modern, high-performance, and scalable technology stack:

### Backend (MVP Server)

*   **Language:** Rust
*   **Web Framework:** Axum (for high performance, memory safety, and concurrency)
*   **Database:** PostgreSQL with PostGIS extension (for robust relational data and advanced geographical queries)
*   **ORM/Database Access:** `sqlx` (asynchronous, compile-time checked SQL queries)
*   **Caching/Messaging:** Redis
*   **Authentication:** JWT (JSON Web Tokens)
*   **Containerization:** Docker (for consistent deployment and environment management)

### Frontend (Multi-Platform)

*   **Web Application:** React with TypeScript
*   **Mobile Application:** React Native with TypeScript (for iOS and Android, maximizing code reuse)
*   **Desktop Application:** Electron (packaging the web application for dedicated desktop use)

## Architecture Overview

LazyFranchisor adopts a microservices-oriented architecture, where different functionalities (e.g., Accounting, Warehouse, POS) are developed and deployed as independent services. All components are containerized using Docker, ensuring portability and scalability.

The multi-platform frontend applications (Web, Mobile, Desktop) interact with the Rust backend via RESTful APIs, secured with JWT-based authentication.

## Getting Started (Local Development Setup)

To set up LazyFranchisor for local development, follow these steps:

### Prerequisites

Ensure you have the following tools installed:

*   **Rust:** Install `rustup` from [https://rustup.rs/](https://rustup.rs/)
*   **Node.js & npm/Yarn:** Install Node.js (LTS version)
*   **Docker & Docker Compose:** Install Docker Desktop from [https://www.docker.com/get-started](https://www.docker.com/get-started)
*   **Git:** Install Git from [https://git-scm.com/downloads](https://git-scm.com/downloads)

### Setup Instructions

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/lohnxongmixay/LazyFranchisor.git
    cd LazyFranchisor
    ```

2.  **Start Backend Services with Docker Compose:**
    Navigate to the `docker` directory and bring up the database, Redis, and backend services:
    ```bash
    cd docker
    docker compose up -d --build
    ```
    This will start PostgreSQL (with PostGIS), Redis, and the Rust backend.

3.  **Run Backend Database Migrations:**
    Once the database is running, apply the necessary migrations for the backend:
    ```bash
    cd ../backend
    # Ensure sqlx-cli is installed: cargo install sqlx-cli
    sqlx migrate run
    ```

4.  **Setup and Run Frontend Applications:**

    *   **Web Application:**
        ```bash
        cd ../frontend/web
        npm install # or yarn install
        npm start   # or yarn start
        ```

    *   **Mobile Application:**
        ```bash
        cd ../frontend/mobile
        npm install # or yarn install
        # Follow React Native environment setup for iOS/Android: https://reactnative.dev/docs/environment-setup
        npm start   # or yarn start
        ```

    *   **Desktop Application:**
        ```bash
        cd ../frontend/desktop
        npm install # or yarn install
        npm start   # or yarn start
        ```

## Documentation

Comprehensive documentation for LazyFranchisor is available in the `docs/` directory:

*   **Executive Presentation:** `docs/Executive_Presentation.md`
*   **Development Guidelines:** `docs/Development_Guidelines.md`
*   **Software Features with Technical Data:** `docs/Software_Features_Technical_Data.md`
*   **System Overview & User Guide:** `docs/Professional_Documentation_User_Guide.md`

## Contributing

We welcome contributions to LazyFranchisor! Please refer to the `Development Guidelines` for coding standards and the `Code Review Process` section for submission instructions.

## License

[Specify your project's license here, e.g., MIT, Apache 2.0, etc.]