# Software Features with Technical Data: LazyFranchisor MVP

**1. Introduction**

*   **1.1 Overview of LazyFranchisor MVP:**
    This document provides a detailed technical specification for the Minimum Viable Product (MVP) of LazyFranchisor, a multi-platform franchising management system. It outlines the core functionalities, user interactions, and underlying technical components for the initial release.
*   **1.2 Scope of Document:**
    This document covers the technical details for the Accounting, Warehouse Management, and Point of Sale (POS) modules, including their respective user stories, API specifications, data models, and conceptual flow diagrams. It also addresses cross-cutting concerns such as authentication and geo-location support.

**2. Core Feature: Accounting**

*   **2.1 Description:**
    The Accounting module focuses on providing essential functionalities for tracking sales and revenue, and generating basic financial reports for both franchisors and individual franchisees.
*   **2.2 User Stories:**
    *   **As a Franchisee,** I want to record daily sales transactions so that I can track my revenue.
    *   **As a Franchisor Admin,** I want to view aggregated sales reports across all franchisees so that I can monitor overall network performance.
    *   **As a Franchisee,** I want to generate a basic profit/loss report for my location so that I can understand my financial standing.
*   **2.3 Technical Details:**
    *   **2.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/sales`: Record a new sales transaction.
            *   Request Body: `SalesTransaction` object (see Data Model).
            *   Response: `SalesTransaction` object with generated ID.
        *   `GET /api/v1/sales/{id}`: Retrieve a specific sales transaction.
        *   `GET /api/v1/sales?franchisee_id={id}&start_date={date}&end_date={date}`: Retrieve sales transactions for a specific franchisee within a date range.
        *   `GET /api/v1/reports/sales/summary?franchisee_id={id}&period={daily|weekly|monthly}`: Generate a sales summary report for a franchisee.
        *   `GET /api/v1/reports/sales/network_summary?period={daily|weekly|monthly}`: Generate an aggregated sales summary report for the entire network (Franchisor Admin only).
    *   **2.3.2 Data Model (Key Entities - Refer to Conceptual Data Model for full details):**
        *   `SalesTransaction`: `id`, `franchisee_id`, `employee_id`, `timestamp`, `total_amount`, `discount_applied`, `payment_method`, `status`.
        *   `SalesLineItem`: `id`, `sales_transaction_id`, `product_id`, `quantity`, `unit_price`, `subtotal`.
        *   `Product`: `id`, `name`, `sku`, `price`.
        *   `Franchisee`: `id`, `name`, `location` (PostGIS `GEOMETRY(Point, 4326)`).
    *   **2.3.3 Flow Diagram (Conceptual: Sales Transaction Processing):**
        This flow diagram illustrates the high-level steps involved when a sales transaction is initiated, from the POS system to inventory updates and persistence in the database.
        ```mermaid
        graph TD
            A[Franchisee POS] --> B{Record Sale Request};
            B --> C{Validate Input};
            C -- Valid --> D[Create SalesTransaction & SalesLineItems];
            D --> E[Update Inventory (via Warehouse Module)];
            E --> F[Persist to PostgreSQL];
            F --> G[Return Success Response];
            C -- Invalid --> H[Return Error Response];
            E -- Inventory Update Failed --> I[Rollback Transaction & Return Error];
        ```
    *   **2.3.4 UML Diagram (Conceptual: Class Diagram for Accounting Entities):**
        This class diagram depicts the relationships between key entities within the Accounting module, such as `SalesTransaction`, `SalesLineItem`, `Product`, and `Franchisee`.
        ```mermaid
        classDiagram
            class SalesTransaction {
                +UUID id
                +UUID franchisee_id
                +UUID employee_id
                +DateTime timestamp
                +Decimal total_amount
                +Decimal discount_applied
                +String payment_method
                +String status
            }
            class SalesLineItem {
                +UUID id
                +UUID sales_transaction_id
                +UUID product_id
                +Integer quantity
                +Decimal unit_price
                +Decimal subtotal
            }
            class Product {
                +UUID id
                +String name
                +String sku
                +Decimal price
            }
            class Franchisee {
                +UUID id
                +String name
                +Point location
            }

            SalesTransaction "1" -- "*" SalesLineItem : contains
            SalesLineItem "*" -- "1" Product : refers to
            SalesTransaction "1" -- "1" Franchisee : belongs to
        ```

**3. Core Feature: Warehouse Management**

*   **3.1 Description:**
    The Warehouse Management module facilitates the efficient handling of inventory, including order fulfillment (picking and packing) and managing stock transfers between different locations within the franchise network.
*   **3.2 User Stories:**
    *   **As a Franchisee,** I want to request a stock transfer from another franchisee or a central warehouse so that I can replenish my inventory.
    *   **As a Warehouse Manager (or Franchisee),** I want to fulfill stock transfer requests by picking and packing items so that they can be shipped.
    *   **As a Franchisee,** I want to receive transferred stock and update my inventory levels so that my stock is accurate.
*   **3.3 Technical Details:**
    *   **3.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/stock-transfers`: Initiate a new stock transfer request.
            *   Request Body: `StockTransfer` object (see Data Model).
            *   Response: `StockTransfer` object with generated ID.
        *   `PUT /api/v1/stock-transfers/{id}/status`: Update the status of a stock transfer (e.g., `shipped`, `received`).
        *   `GET /api/v1/stock-transfers?from_franchisee_id={id}&to_franchisee_id={id}&status={status}`: Retrieve stock transfers.
        *   `GET /api/v1/inventory?franchisee_id={id}&product_id={id}`: Retrieve current inventory levels for a location/product.
        *   `PUT /api/v1/inventory/{id}/adjust`: Adjust inventory quantity (e.g., for receiving transferred stock).
    *   **3.3.2 Data Model (Key Entities):**
        *   `InventoryItem`: `id`, `franchisee_id`, `product_id`, `quantity_on_hand`, `location` (e.g., shelf, bin), `last_updated`.
        *   `StockTransfer`: `id`, `from_franchisee_id`, `to_franchisee_id`, `initiated_by_user_id`, `timestamp`, `status`.
        *   `StockTransferLineItem`: `id`, `stock_transfer_id`, `product_id`, `quantity`.
    *   **3.3.3 Flow Diagram (Conceptual: Stock Transfer Request & Fulfillment):**
        This flow diagram outlines the process of requesting and fulfilling a stock transfer, from initiation by a franchisee to the final receipt and inventory update.
        ```mermaid
        graph TD
            A[Requesting Franchisee] --> B{Initiate Stock Transfer Request};
            B --> C[Create StockTransfer & StockTransferLineItems];
            C --> D[Persist to PostgreSQL];
            D --> E[Notify Fulfilling Location];
            E --> F{Fulfilling Location Picks & Packs};
            F --> G{Update StockTransfer Status to 'Shipped'};
            G --> H[Persist Status Update];
            H --> I[Notify Requesting Franchisee];
            I --> J{Requesting Franchisee Receives Stock};
            J --> K{Update Inventory Levels};
            K --> L[Persist Inventory Update];
            L --> M{Update StockTransfer Status to 'Received'};
            M --> N[Persist Final Status];
        ```
    *   **3.3.4 UML Diagram (Conceptual: Class Diagram for Warehouse Entities):**
        This class diagram illustrates the relationships between `InventoryItem`, `StockTransfer`, and `StockTransferLineItem` within the Warehouse Management module.
        ```mermaid
        classDiagram
            class InventoryItem {
                +UUID id
                +UUID franchisee_id
                +UUID product_id
                +Integer quantity_on_hand
                +String location
                +DateTime last_updated
            }
            class StockTransfer {
                +UUID id
                +UUID from_franchisee_id
                +UUID to_franchisee_id
                +UUID initiated_by_user_id
                +DateTime timestamp
                +String status
            }
            class StockTransferLineItem {
                +UUID id
                +UUID stock_transfer_id
                +UUID product_id
                +Integer quantity
            }

            InventoryItem "1" -- "1" Product : refers to
            StockTransfer "1" -- "*" StockTransferLineItem : contains
            StockTransferLineItem "*" -- "1" Product : refers to
            StockTransfer "1" -- "1" Franchisee : from
            StockTransfer "1" -- "1" Franchisee : to
        ```
    *   **3.3.5 Sequence Diagram (Conceptual: Stock Transfer Request & Processing):**
        This sequence diagram details the interactions between the Requesting Franchisee's client, the LazyFranchisor Backend, and the Fulfilling Location's client during a stock transfer.
        ```mermaid
        sequenceDiagram
            actor RF as Requesting Franchisee (Client)
            participant LB as LazyFranchisor Backend
            actor FL as Fulfilling Location (Client)
            database DB as PostgreSQL Database

            RF->>LB: POST /api/v1/stock-transfers (Request Transfer)
            activate LB
            LB->>DB: Save StockTransfer & LineItems (Status: Pending)
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB->>FL: Notify Fulfilling Location (e.g., WebSocket/Push)
            LB-->>RF: 201 Created (Transfer Request ID)
            deactivate LB

            FL->>LB: GET /api/v1/stock-transfers/{id} (Fetch Transfer Details)
            activate LB
            LB->>DB: Retrieve Transfer Details
            activate DB
            DB-->>LB: Transfer Details
            deactivate DB
            LB-->>FL: 200 OK (Transfer Details)
            deactivate LB

            FL->>FL: Pick & Pack Items
            FL->>LB: PUT /api/v1/stock-transfers/{id}/status (Status: Shipped)
            activate LB
            LB->>DB: Update StockTransfer Status
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB->>RF: Notify Requesting Franchisee (e.g., WebSocket/Push)
            LB-->>FL: 200 OK
            deactivate LB

            RF->>LB: GET /api/v1/stock-transfers/{id} (Check Status)
            activate LB
            LB->>DB: Retrieve Transfer Status
            activate DB
            DB-->>LB: Status: Shipped
            deactivate DB
            LB-->>RF: 200 OK (Status: Shipped)
            deactivate LB

            RF->>RF: Receive & Verify Items
            RF->>LB: PUT /api/v1/stock-transfers/{id}/status (Status: Received)
            activate LB
            LB->>DB: Update StockTransfer Status
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB->>DB: Adjust InventoryItem Quantities (for RF)
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB-->>RF: 200 OK
            deactivate LB
        ```

**4. Core Feature: POS (Point of Sale)**

*   **4.1 Description:**
    The POS module enables franchisees to process sales transactions efficiently, apply discounts and promotions, and ensures real-time integration with inventory management.
*   **4.2 User Stories:**
    *   **As a Cashier,** I want to apply a discount to a customer's order so that they receive the correct price.
    *   **As a Cashier,** I want the POS system to automatically update inventory levels when a sale is made so that stock is accurate.
    *   **As a Franchisor Admin,** I want to define and manage promotions that can be applied at POS so that franchisees can offer them.
*   **4.3 Technical Details:**
    *   **4.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/pos/transactions`: Process a new POS transaction (includes sales, discounts, and triggers inventory update).
            *   Request Body: `POSTransactionRequest` object (includes `SalesTransaction` details and applied `Discount` IDs).
            *   Response: `SalesTransaction` object.
        *   `GET /api/v1/promotions`: Retrieve a list of active promotions.
        *   `POST /api/v1/promotions`: Create a new promotion (Franchisor Admin only).
        *   `PUT /api/v1/promotions/{id}`: Update an existing promotion (Franchisor Admin only).
    *   **4.3.2 Data Model (Key Entities):**
        *   `SalesTransaction` (as above)
        *   `SalesLineItem` (as above)
        *   `Product` (as above)
        *   `InventoryItem` (as above)
        *   `Discount`: `id`, `name`, `description`, `type` (e.g., `percentage`, `fixed_amount`), `value`, `start_date`, `end_date`, `applicable_products` (array of product IDs or categories).
    *   **4.3.3 Flow Diagram (Conceptual: POS Transaction with Discount & Inventory Update):**
        This flow diagram illustrates the process of a POS transaction, including discount application and the subsequent update to inventory levels.
        ```mermaid
        graph TD
            A[Cashier at POS] --> B{Process Transaction Request};
            B --> C{Validate Products & Quantities};
            C --> D{Apply Discounts/Promotions};
            D --> E{Calculate Total & Create SalesTransaction};
            E --> F[Update Inventory (via Warehouse Module)];
            F --> G[Persist SalesTransaction & SalesLineItems];
            G --> H[Return Success Response];
            C -- Invalid --> I[Return Error Response];
            F -- Inventory Update Failed --> J[Rollback Transaction & Return Error];
        ```
    *   **4.3.4 UML Diagram (Conceptual: Class Diagram for POS Entities):**
        This class diagram shows the relationships between `SalesTransaction`, `SalesLineItem`, `Product`, `Discount`, and `InventoryItem` within the POS module.
        ```mermaid
        classDiagram
            class SalesTransaction {
                +UUID id
                +Decimal total_amount
                +Decimal discount_applied
                ...
            }
            class SalesLineItem {
                +UUID id
                +Integer quantity
                +Decimal subtotal
                ...
            }
            class Product {
                +UUID id
                +String name
                +Decimal price
                ...
            }
            class Discount {
                +UUID id
                +String name
                +String type
                +Decimal value
                +Date start_date
                +Date end_date
                +List~UUID~ applicable_products
            }
            class InventoryItem {
                +UUID id
                +Integer quantity_on_hand
                ...
            }

            SalesTransaction "1" -- "*" SalesLineItem : contains
            SalesLineItem "*" -- "1" Product : refers to
            SalesTransaction "1" -- "*" Discount : applies
            SalesTransaction "1" -- "1" InventoryItem : updates
        ```
    *   **4.3.5 Sequence Diagram (Conceptual: POS Transaction Processing):**
        This sequence diagram details the interactions during a POS transaction, from the Cashier's client to the LazyFranchisor Backend and the database, including inventory updates.
        ```mermaid
        sequenceDiagram
            actor C as Cashier (POS Client)
            participant LB as LazyFranchisor Backend
            database DB as PostgreSQL Database

            C->>LB: POST /api/v1/pos/transactions (Process Sale)
            activate LB
            LB->>LB: Validate Request & Apply Discounts
            LB->>DB: Fetch Product & Inventory Details
            activate DB
            DB-->>LB: Product & Inventory Data
            deactivate DB
            LB->>DB: Create SalesTransaction & SalesLineItems
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB->>DB: Update InventoryItem Quantities
            activate DB
            DB-->>LB: Confirmation
            deactivate DB
            LB-->>C: 201 Created (SalesTransaction ID)
            deactivate LB
        ```

**5. Cross-Cutting Concerns**

*   **5.1 Authentication & Authorization:**
    *   **Mechanism:** JWT (JSON Web Tokens) for stateless authentication.
    *   **Flow:** User logs in, receives a JWT. This token is then sent with subsequent requests in the `Authorization` header.
    *   **Authorization:** Role-Based Access Control (RBAC) will be implemented, where user roles (Franchisor Admin, Franchisee Owner, Employee) determine access to specific API endpoints and data.
    *   **5.1.1 Sequence Diagram (Conceptual: User Authentication):**
        This sequence diagram illustrates the process of a user logging in and obtaining a JWT for subsequent authenticated requests.
        ```mermaid
        sequenceDiagram
            actor U as User (Client)
            participant LB as LazyFranchisor Backend
            database DB as PostgreSQL Database

            U->>LB: POST /api/v1/auth/login (Credentials)
            activate LB
            LB->>DB: Verify User Credentials
            activate DB
            DB-->>LB: User Details (if valid)
            deactivate DB
            alt Credentials Valid
                LB->>LB: Generate JWT
                LB-->>U: 200 OK (JWT)
            else Credentials Invalid
                LB-->>U: 401 Unauthorized
            end
            deactivate LB

            U->>LB: GET /api/v1/protected-resource (with JWT in Authorization header)
            activate LB
            LB->>LB: Validate JWT & Check User Permissions (RBAC)
            alt Token Valid & Authorized
                LB->>DB: Fetch Protected Data
                activate DB
                DB-->>LB: Data
                deactivate DB
                LB-->>U: 200 OK (Protected Data)
            else Token Invalid or Unauthorized
                LB-->>U: 403 Forbidden / 401 Unauthorized
            end
            deactivate LB
        ```
*   **5.2 Geo-location Support:**
    *   **Storage:** Franchisee locations will be stored as `GEOMETRY(Point, 4326)` data type in PostgreSQL using the PostGIS extension.
    *   **Basic Queries:** Initial MVP will support storing and retrieving location data. Future enhancements will include spatial queries (e.g., finding nearest franchisees, defining delivery zones).
*   **5.3 Containerization:**
    *   All backend services (Rust/Axum), database (PostgreSQL/PostGIS), and caching (Redis) will be deployed as Docker containers.
    *   `docker-compose.yml` will define the multi-container application for local development and testing.
    *   Frontend applications will also have Dockerfiles for consistent build and deployment.