# Software Features with Technical Data: LazyFranchisor

**1. Introduction**

*   **1.1 Overview of LazyFranchisor:**
    This document provides a detailed technical specification for LazyFranchisor, a multi-platform franchising management system. It outlines the core functionalities, user interactions, and underlying technical components for both the initial Minimum Viable Product (MVP) and planned expanded features.
*   **1.2 Scope of Document:**
    This document covers the technical details for the Accounting, Warehouse Management, Point of Sale (POS), Auditing, SOP, SCM, ERP, CRM, and Central Kitchen Management modules. It includes user stories, API specifications, data models, and conceptual diagrams (flow, UML class, sequence) where appropriate. Cross-cutting concerns such as authentication and geo-location support are also addressed.

**2. Core Feature: Accounting (Current MVP & Expanded)**

*   **2.1 Description:**
    The Accounting module provides essential functionalities for tracking sales and revenue, generating basic financial reports, and will expand to include automated royalty invoicing, expense management, advanced reporting, budgeting, and integration with external accounting software.
*   **2.2 User Stories:**
    *   **As a Franchisee,** I want to record daily sales transactions so that I can track my revenue.
    *   **As a Franchisor Admin,** I want to view aggregated sales reports across all franchisees so that I can monitor overall network performance.
    *   **As a Franchisee,** I want to generate a basic profit/loss report for my location so that I can understand my financial standing.
    *   **As a Franchisor Admin,** I want the system to automatically generate royalty invoices for franchisees based on their sales data.
    *   **As a Franchisee,** I want to record and categorize my business expenses for better financial tracking.
    *   **As a Financial Manager,** I want to generate detailed financial statements (P&L, Balance Sheet) and set budgets.
*   **2.3 Technical Details:**
    *   **2.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/sales`: Record a new sales transaction.
            *   Request Body: `SalesTransaction` object (see Data Model).
            *   Response: `SalesTransaction` object with generated ID.
        *   `GET /api/v1/sales/{id}`: Retrieve a specific sales transaction.
        *   `GET /api/v1/sales?franchisee_id={id}&start_date={date}&end_date={date}`: Retrieve sales transactions for a specific franchisee within a date range.
        *   `GET /api/v1/reports/sales/summary?franchisee_id={id}&period={daily|weekly|monthly}`: Generate a sales summary report for a franchisee.
        *   `GET /api/v1/reports/sales/network_summary?period={daily|weekly|monthly}`: Generate an aggregated sales summary report for the entire network (Franchisor Admin only).
        *   `POST /api/v1/royalties/generate`: Trigger royalty invoice generation.
        *   `GET /api/v1/royalties?franchisee_id={id}`: Retrieve royalty invoices.
        *   `POST /api/v1/expenses`: Record a new expense.
        *   `GET /api/v1/reports/financials/pnl`: Generate Profit & Loss statement.
    *   **2.3.2 Data Model (Key Entities - Refer to Conceptual Data Model for full details):**
        *   `SalesTransaction`: `id`, `franchisee_id`, `employee_id`, `timestamp`, `total_amount`, `discount_applied`, `payment_method`, `status`.
        *   `SalesLineItem`: `id`, `sales_transaction_id`, `product_id`, `quantity`, `unit_price`, `subtotal`.
        *   `Product`: `id`, `name`, `sku`, `price`.
        *   `Franchisee`: `id`, `name`, `location` (PostGIS `GEOMETRY(Point, 4326)`).
        *   `RoyaltyInvoice`: `id`, `franchisee_id`, `period_start`, `period_end`, `amount_due`, `status`.
        *   `Expense`: `id`, `franchisee_id`, `amount`, `category`, `description`, `date`.
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
        This class diagram depicts the relationships between key entities within the Accounting module, such as `SalesTransaction`, `SalesLineItem`, `Product`, `Franchisee`, `RoyaltyInvoice`, and `Expense`.
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
            class RoyaltyInvoice {
                +UUID id
                +UUID franchisee_id
                +Date period_start
                +Date period_end
                +Decimal amount_due
                +String status
            }
            class Expense {
                +UUID id
                +UUID franchisee_id
                +Decimal amount
                +String category
                +String description
                +Date date
            }

            SalesTransaction "1" -- "*" SalesLineItem : contains
            SalesLineItem "*" -- "1" Product : refers to
            SalesTransaction "1" -- "1" Franchisee : belongs to
            RoyaltyInvoice "1" -- "1" Franchisee : for
            Expense "1" -- "1" Franchisee : incurred by
        ```

**3. Core Feature: Warehouse Management (Current MVP & Expanded)**

*   **3.1 Description:**
    The Warehouse Management module facilitates the efficient handling of inventory, including order fulfillment (picking and packing), managing stock transfers, and will expand to include detailed inventory tracking (batch, expiry), receiving/putaway processes, cycle counting, and supplier integration.
*   **3.2 User Stories:**
    *   **As a Franchisee,** I want to request a stock transfer from another franchisee or a central warehouse so that I can replenish my inventory.
    *   **As a Warehouse Manager (or Franchisee),** I want to fulfill stock transfer requests by picking and packing items so that they can be shipped.
    *   **As a Franchisee,** I want to receive transferred stock and update my inventory levels so that my stock is accurate.
    *   **As a Warehouse Manager,** I want to track inventory by specific bin locations, batch numbers, and expiry dates.
    *   **As a Warehouse Manager,** I want a streamlined process for receiving new goods and putting them away in designated locations.
    *   **As an Inventory Auditor,** I want to perform cycle counts and physical inventories to verify stock accuracy.
*   **3.3 Technical Details:**
    *   **3.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/stock-transfers`: Initiate a new stock transfer request.
            *   Request Body: `StockTransfer` object (see Data Model).
            *   Response: `StockTransfer` object with generated ID.
        *   `PUT /api/v1/stock-transfers/{id}/status`: Update the status of a stock transfer (e.g., `shipped`, `received`).
        *   `GET /api/v1/stock-transfers?from_franchisee_id={id}&to_franchisee_id={id}&status={status}`: Retrieve stock transfers.
        *   `GET /api/v1/inventory?franchisee_id={id}&product_id={id}`: Retrieve current inventory levels for a location/product.
        *   `PUT /api/v1/inventory/{id}/adjust`: Adjust inventory quantity (e.g., for receiving transferred stock).
        *   `GET /api/v1/inventory/detailed?franchisee_id={id}`: Retrieve detailed inventory (including batch/expiry).
        *   `POST /api/v1/receiving`: Record receipt of goods.
        *   `POST /api/v1/inventory/cycle-count`: Record cycle count results.
    *   **3.3.2 Data Model (Key Entities):**
        *   `InventoryItem`: `id`, `franchisee_id`, `product_id`, `quantity_on_hand`, `location` (e.g., shelf, bin), `last_updated`, `batch_number`, `expiry_date`.
        *   `StockTransfer`: `id`, `from_franchisee_id`, `to_franchisee_id`, `initiated_by_user_id`, `timestamp`, `status`.
        *   `StockTransferLineItem`: `id`, `stock_transfer_id`, `product_id`, `quantity`.
        *   `ReceivingRecord`: `id`, `franchisee_id`, `supplier_id`, `timestamp`, `items_received`.
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
        This class diagram illustrates the relationships between `InventoryItem`, `StockTransfer`, `StockTransferLineItem`, and `ReceivingRecord` within the Warehouse Management module.
        ```mermaid
        classDiagram
            class InventoryItem {
                +UUID id
                +UUID franchisee_id
                +UUID product_id
                +Integer quantity_on_hand
                +String location
                +DateTime last_updated
                +String batch_number
                +Date expiry_date
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
            class ReceivingRecord {
                +UUID id
                +UUID franchisee_id
                +UUID supplier_id
                +DateTime timestamp
                +List~ReceivedItem~ items_received
            }

            InventoryItem "1" -- "1" Product : refers to
            StockTransfer "1" -- "*" StockTransferLineItem : contains
            StockTransferLineItem "*" -- "1" Product : refers to
            StockTransfer "1" -- "1" Franchisee : from
            StockTransfer "1" -- "1" Franchisee : to
            ReceivingRecord "1" -- "*" InventoryItem : affects
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

**4. Core Feature: POS (Point of Sale) (Current MVP & Expanded)**

*   **4.1 Description:**
    The POS module enables franchisees to process sales transactions efficiently, apply discounts and promotions, ensures real-time integration with inventory management, and will expand to include order management (dine-in, takeout, delivery), table management, employee management, gift card/loyalty programs, and offline mode.
*   **4.2 User Stories:**
    *   **As a Cashier,** I want to apply a discount to a customer's order so that they receive the correct price.
    *   **As a Cashier,** I want the POS system to automatically update inventory levels when a sale is made so that stock is accurate.
    *   **As a Franchisor Admin,** I want to define and manage promotions that can be applied at POS so that franchisees can offer them.
    *   **As a Customer,** I want to place an order for delivery through the system.
    *   **As a Manager,** I want to manage employee clock-in/out and permissions directly from the POS.
    *   **As a Customer,** I want to use a gift card or loyalty points to pay for my order.
*   **4.3 Technical Details:**
    *   **4.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/pos/transactions`: Process a new POS transaction (includes sales, discounts, and triggers inventory update).
        *   `GET /api/v1/promotions`: Retrieve a list of active promotions.
        *   `POST /api/v1/promotions`: Create a new promotion (Franchisor Admin only).
        *   `PUT /api/v1/promotions/{id}`: Update an existing promotion (Franchisor Admin only).
        *   `POST /api/v1/orders`: Place a new order (dine-in, takeout, delivery).
        *   `PUT /api/v1/orders/{id}/status`: Update order status (e.g., `preparing`, `ready`, `delivered`).
        *   `POST /api/v1/employees/clock-in`: Employee clock-in.
        *   `POST /api/v1/employees/clock-out`: Employee clock-out.
        *   `POST /api/v1/payments/gift-card`: Process gift card payment.
    *   **4.3.2 Data Model (Key Entities):**
        *   `SalesTransaction` (as above)
        *   `SalesLineItem` (as above)
        *   `Product` (as above)
        *   `InventoryItem` (as above)
        *   `Discount`: `id`, `name`, `description`, `type` (e.g., `percentage`, `fixed_amount`), `value`, `start_date`, `end_date`, `applicable_products` (array of product IDs or categories).
        *   `Order`: `id`, `franchisee_id`, `customer_id`, `type` (dine-in, takeout, delivery), `status`, `total_amount`, `delivery_address`.
        *   `EmployeeShift`: `id`, `employee_id`, `clock_in`, `clock_out`.
        *   `GiftCard`: `id`, `code`, `balance`, `status`.
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
        This class diagram shows the relationships between `SalesTransaction`, `SalesLineItem`, `Product`, `Discount`, `InventoryItem`, `Order`, `EmployeeShift`, and `GiftCard` within the POS module.
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
            class Order {
                +UUID id
                +UUID franchisee_id
                +UUID customer_id
                +String type
                +String status
                +Decimal total_amount
                +String delivery_address
            }
            class EmployeeShift {
                +UUID id
                +UUID employee_id
                +DateTime clock_in
                +DateTime clock_out
            }
            class GiftCard {
                +UUID id
                +String code
                +Decimal balance
                +String status
            }

            SalesTransaction "1" -- "*" SalesLineItem : contains
            SalesLineItem "*" -- "1" Product : refers to
            SalesTransaction "1" -- "*" Discount : applies
            SalesTransaction "1" -- "1" InventoryItem : updates
            Order "1" -- "*" SalesLineItem : contains
            Order "1" -- "1" Franchisee : belongs to
            EmployeeShift "1" -- "1" Employee : for
            SalesTransaction "1" -- "1" GiftCard : uses
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

**5. Core Feature: Auditing**

*   **5.1 Description:**
    The Auditing module provides tools for franchisors to maintain brand standards, quality, and regulatory compliance across the network through customizable checklists, scheduled inspections, and corrective action tracking.
*   **5.2 User Stories:**
    *   **As a Franchisor Admin,** I want to create customizable audit checklists for different types of inspections (e.g., operational, quality, food safety).
    *   **As an Auditor,** I want to schedule and conduct inspections at franchisee locations, submitting photo/video evidence.
    *   **As a Franchisee,** I want to view audit results and track corrective actions required for my location.
    *   **As a Franchisor Admin,** I want a dashboard to monitor compliance status across all franchisees.
*   **5.3 Technical Details:**
    *   **5.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/audits/checklists`: Create a new audit checklist.
        *   `GET /api/v1/audits/checklists/{id}`: Retrieve a specific checklist.
        *   `POST /api/v1/audits/inspections`: Schedule a new inspection.
        *   `PUT /api/v1/audits/inspections/{id}/submit`: Submit inspection results (including media).
        *   `GET /api/v1/audits/reports?franchisee_id={id}`: Retrieve audit reports.
        *   `PUT /api/v1/audits/corrective-actions/{id}/status`: Update corrective action status.
    *   **5.3.2 Data Model (Key Entities):**
        *   `AuditChecklist`: `id`, `name`, `description`, `items` (array of checklist questions/criteria).
        *   `Inspection`: `id`, `checklist_id`, `franchisee_id`, `auditor_id`, `scheduled_date`, `completed_date`, `results` (JSONB), `status`.
        *   `CorrectiveAction`: `id`, `inspection_id`, `description`, `assigned_to_franchisee_id`, `due_date`, `status`.
    *   **5.3.3 Flow Diagram (Conceptual: Audit Process):**
        ```mermaid
        graph TD
            A[Franchisor Admin] --> B{Create/Manage Audit Checklist};
            B --> C[Schedule Inspection];
            C --> D[Auditor Conducts Inspection];
            D --> E{Submit Inspection Results (with Media)};
            E --> F[Generate Audit Report];
            F --> G[Identify Corrective Actions];
            G --> H[Franchisee Implements Corrective Actions];
            H --> I[Verify Corrective Actions];
            I --> J[Update Compliance Status];
        ```

**6. Core Feature: Standard Operating Procedures (SOP)**

*   **6.1 Description:**
    The SOP module provides a centralized platform for creating, managing, and distributing Standard Operating Procedures, ensuring consistency, quality, and compliance across all franchise locations.
*   **6.2 User Stories:**
    *   **As a Franchisor Admin,** I want to create and publish SOPs for various operational tasks, with version control.
    *   **As a Franchisee,** I want to easily access the latest version of all relevant SOPs for my operations.
    *   **As a Manager,** I want to assign SOP-related tasks to employees and track their completion.
    *   **As a Franchisor Admin,** I want to link SOPs to training modules and monitor franchisee/employee training completion.
*   **6.3 Technical Details:**
    *   **6.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/sops`: Create a new SOP document.
        *   `GET /api/v1/sops?category={category}`: Retrieve SOPs.
        *   `PUT /api/v1/sops/{id}/version`: Create a new version of an SOP.
        *   `POST /api/v1/sops/tasks`: Assign an SOP-related task.
        *   `PUT /api/v1/sops/tasks/{id}/complete`: Mark a task as complete.
    *   **6.3.2 Data Model (Key Entities):**
        *   `SOPDocument`: `id`, `title`, `content` (Markdown/HTML), `version`, `category`, `published_date`, `last_updated_by_user_id`.
        *   `SOPTask`: `id`, `sop_id`, `assigned_to_user_id`, `due_date`, `status`, `description`.
        *   `SOPTrainingRecord`: `id`, `sop_id`, `user_id`, `completion_date`.

**7. Core Feature: Supply Chain Management (SCM)**

*   **7.1 Description:**
    The SCM module optimizes the flow of goods and services from suppliers to customers across the franchise network, including centralized purchasing, vendor management, automated reordering, and inter-store transfer optimization.
*   **7.2 User Stories:**
    *   **As a Franchisor Admin,** I want to manage a centralized list of approved vendors and negotiate bulk purchasing agreements.
    *   **As a Franchisee,** I want to place orders with approved vendors through the system.
    *   **As a Warehouse Manager,** I want the system to suggest reorder quantities based on sales and inventory levels.
    *   **As a Franchisor Admin,** I want to monitor supply chain performance and vendor compliance.
*   **7.3 Technical Details:**
    *   **7.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/vendors`: Add a new vendor.
        *   `GET /api/v1/vendors`: Retrieve approved vendors.
        *   `POST /api/v1/purchase-orders`: Create a new purchase order.
        *   `GET /api/v1/purchase-orders?franchisee_id={id}`: Retrieve purchase orders.
        *   `GET /api/v1/scm/reorder-suggestions?franchisee_id={id}`: Get reorder suggestions.
    *   **7.3.2 Data Model (Key Entities):**
        *   `Vendor`: `id`, `name`, `contact_info`, `products_supplied`, `contract_details`.
        *   `PurchaseOrder`: `id`, `franchisee_id`, `vendor_id`, `order_date`, `status`, `items` (array of `PurchaseOrderItem`).
        *   `PurchaseOrderItem`: `product_id`, `quantity`, `unit_price`.

**8. Core Feature: Enterprise Resource Planning (ERP)**

*   **8.1 Description:**
    The ERP module aims to unify business processes and data across multiple locations, providing comprehensive multi-location management, granular user access control, contract management, and asset management.
*   **8.2 User Stories:**
    *   **As a Franchisor Admin,** I want to manage all franchisee locations and their configurations from a central dashboard.
    *   **As an Administrator,** I want to define precise access permissions for different user roles and individuals.
    *   **As a Legal Manager,** I want to store and manage all franchise agreements and receive automated renewal reminders.
    *   **As an Operations Manager,** I want to track and manage company assets (e.g., equipment, vehicles) across all locations.
*   **8.3 Technical Details:**
    *   **8.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/franchisees`: Create a new franchisee record.
        *   `GET /api/v1/franchisees/{id}/config`: Retrieve franchisee-specific configurations.
        *   `POST /api/v1/users/roles`: Define new user roles and permissions.
        *   `PUT /api/v1/users/{id}/permissions`: Update individual user permissions.
        *   `POST /api/v1/contracts`: Upload and manage a new contract.
        *   `GET /api/v1/assets?franchisee_id={id}`: Retrieve assets for a franchisee.
    *   **8.3.2 Data Model (Key Entities):**
        *   `Franchisee` (expanded): `id`, `name`, `location`, `configuration` (JSONB), `contract_id`.
        *   `User`: `id`, `name`, `email`, `password_hash`, `role_id`.
        *   `Role`: `id`, `name`, `permissions` (array of permission strings).
        *   `Contract`: `id`, `type`, `document_url`, `start_date`, `end_date`, `renewal_date`, `franchisee_id`.
        *   `Asset`: `id`, `name`, `type`, `serial_number`, `franchisee_id`, `purchase_date`, `warranty_expiry`.

**9. Core Feature: Customer Relationship Management (CRM)**

*   **9.1 Description:**
    The CRM module is designed to manage interactions with franchise leads, current franchisees, and end customers, including lead management, customer contact management, marketing automation, and customer support ticketing.
*   **9.2 User Stories:**
    *   **As a Franchisor Sales Manager,** I want to track and manage potential franchisee leads through a sales pipeline.
    *   **As a Franchisee,** I want to store and manage my local customer contact information and purchase history.
    *   **As a Marketing Manager,** I want to create and send email marketing campaigns to customers.
    *   **As a Support Agent,** I want to manage customer inquiries and support requests through a ticketing system.
*   **9.3 Technical Details:**
    *   **9.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/crm/leads`: Create a new franchisee lead.
        *   `PUT /api/v1/crm/leads/{id}/status`: Update lead status.
        *   `POST /api/v1/crm/customers`: Add a new local customer.
        *   `GET /api/v1/crm/customers?franchisee_id={id}`: Retrieve local customers.
        *   `POST /api/v1/crm/marketing/campaigns`: Create a new marketing campaign.
        *   `POST /api/v1/crm/tickets`: Create a new support ticket.
    *   **9.3.2 Data Model (Key Entities):**
        *   `FranchiseeLead`: `id`, `name`, `contact_info`, `source`, `status`, `pipeline_stage`.
        *   `Customer`: `id`, `franchisee_id`, `name`, `email`, `phone`, `address`, `purchase_history` (JSONB).
        *   `MarketingCampaign`: `id`, `name`, `type`, `start_date`, `end_date`, `target_audience`.
        *   `SupportTicket`: `id`, `customer_id`, `franchisee_id`, `subject`, `description`, `status`, `assigned_to_user_id`.

**10. Core Feature: Central Kitchen Management**

*   **10.1 Description:**
    For franchises with a central kitchen model, this module provides specific features for efficient production and distribution, including production planning, recipe/ingredient management, centralized raw material inventory, and wastage tracking.
*   **10.2 User Stories:**
    *   **As a Central Kitchen Manager,** I want to plan and schedule production based on demand from franchisees.
    *   **As a Chef,** I want to access a centralized recipe database with ingredient lists and preparation instructions.
    *   **As an Inventory Manager,** I want to manage raw material inventory for the central kitchen.
    *   **As a Production Manager,** I want to track and minimize production waste.
    *   **As a Logistics Manager,** I want to manage delivery schedules from the central kitchen to franchisees.
*   **10.3 Technical Details:**
    *   **10.3.1 API Endpoints (Conceptual):**
        *   `POST /api/v1/central-kitchen/production-plans`: Create a new production plan.
        *   `GET /api/v1/central-kitchen/recipes/{id}`: Retrieve a recipe.
        *   `POST /api/v1/central-kitchen/inventory/raw-materials`: Add raw material inventory.
        *   `POST /api/v1/central-kitchen/wastage`: Record production wastage.
        *   `POST /api/v1/central-kitchen/deliveries`: Schedule a delivery.
    *   **10.3.2 Data Model (Key Entities):**
        *   `ProductionPlan`: `id`, `date`, `items_to_produce` (array of `Product` and `quantity`), `status`.
        *   `Recipe`: `id`, `name`, `ingredients` (array of `Ingredient` and `quantity`), `instructions`, `yield`.
        *   `RawMaterialInventory`: `id`, `material_id`, `quantity_on_hand`, `location`.
        *   `WastageRecord`: `id`, `date`, `product_id`, `quantity`, `reason`.
        *   `DeliverySchedule`: `id`, `delivery_date`, `from_location`, `to_franchisee_id`, `items_delivered`.

**11. Cross-Cutting Concerns**

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