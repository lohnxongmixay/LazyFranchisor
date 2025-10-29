# LazyFranchisor: AI Development Prompts

**Document Version:** 1.0
**Date:** October 29, 2025
**Purpose:** Comprehensive AI prompts for accelerated development of LazyFranchisor features

---

## Table of Contents

1. [How to Use These Prompts](#how-to-use-these-prompts)
2. [Backend Development Prompts](#backend-development-prompts)
3. [Frontend Development Prompts](#frontend-development-prompts)
4. [Database & Migrations Prompts](#database--migrations-prompts)
5. [Testing & QA Prompts](#testing--qa-prompts)
6. [Security & Performance Prompts](#security--performance-prompts)
7. [DevOps & Infrastructure Prompts](#devops--infrastructure-prompts)
8. [Feature-Specific Prompts](#feature-specific-prompts)

---

## How to Use These Prompts

### Best Practices

1. **Provide Context**: Always include relevant files, database schemas, and existing code patterns
2. **Reference Documentation**: Point the AI to our coding standards and architecture decisions
3. **Be Specific**: Include exact field names, types, and business requirements
4. **Request Tests**: Always ask for comprehensive tests alongside implementation
5. **Iterate**: Use follow-up prompts to refine and improve generated code

### Prompt Template Structure

```
Context: [Brief description of the feature/task]
Reference Files: [Existing code to follow as examples]
Requirements: [Specific requirements and acceptance criteria]
Technical Constraints: [Database schema, API contracts, etc.]
Expected Deliverables: [Implementation + Tests + Documentation]
```

---

## Backend Development Prompts

### Prompt 1: Create New API Endpoint (Generic Template)

```
I'm building the LazyFranchisor franchise management system using Rust with Axum framework.

CONTEXT:
I need to create a new API endpoint for [FEATURE_NAME].

REFERENCE FILES:
- Review the existing authentication handler in our codebase for patterns: [provide auth_handler.rs]
- Follow the error handling pattern from: [provide errors.rs]
- Database models follow this structure: [provide example model]

REQUIREMENTS:
1. Create [HTTP_METHOD] /api/v1/[RESOURCE] endpoint
2. Request body should include: [list fields with types]
3. Response should return: [describe response structure]
4. Authentication: Requires JWT token with [ROLE/PERMISSION]
5. Authorization: Check [SPECIFIC_PERMISSION]
6. Validation: [list validation rules]
7. Business Logic: [describe business rules]

TECHNICAL CONSTRAINTS:
- Database: PostgreSQL with sqlx
- Use async/await with Tokio runtime
- Follow our error handling pattern (AppError enum)
- Return appropriate HTTP status codes
- Log important operations with tracing

EXPECTED DELIVERABLES:
1. Handler function in handlers/[resource]_handler.rs
2. Request/Response structs with serde + validator
3. Service layer function in services/[resource]_service.rs
4. Database queries with compile-time checking
5. Unit tests for service layer
6. Integration tests for API endpoint
7. Update OpenAPI/Swagger documentation
8. Error handling for all edge cases

CODING STANDARDS:
- Follow our Rust coding standards from: docs/Development_Team_Handbook.md
- Use descriptive variable names
- Add doc comments for public functions
- Handle all Result types explicitly
- Use ? operator for error propagation

Please provide the complete implementation with all files needed.
```

---

### Prompt 2: Authentication Service

```
I'm building the LazyFranchisor authentication system in Rust with Axum.

CONTEXT:
Implement a complete JWT-based authentication service with user registration, login, token refresh, and password reset.

DATABASE SCHEMA:
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    role_id UUID REFERENCES roles(id),
    is_active BOOLEAN DEFAULT true,
    email_verified BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE roles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT
);

CREATE TABLE permissions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resource VARCHAR(100) NOT NULL,
    action VARCHAR(50) NOT NULL,
    UNIQUE(resource, action)
);

CREATE TABLE role_permissions (
    role_id UUID REFERENCES roles(id) ON DELETE CASCADE,
    permission_id UUID REFERENCES permissions(id) ON DELETE CASCADE,
    PRIMARY KEY (role_id, permission_id)
);
```

REQUIREMENTS:
1. User Registration:
   - Validate email format
   - Check email uniqueness
   - Hash password with Argon2
   - Create user record
   - Send verification email (mock for now)
   - Return JWT token

2. User Login:
   - Validate credentials
   - Check if user is active
   - Verify password with Argon2
   - Generate JWT access token (15 min expiry)
   - Generate refresh token (7 days expiry)
   - Return both tokens + user info

3. Token Refresh:
   - Validate refresh token
   - Generate new access token
   - Return new access + refresh tokens

4. Password Reset:
   - Request reset (email with token)
   - Validate reset token
   - Update password

5. JWT Middleware:
   - Extract token from Authorization header
   - Validate token signature and expiry
   - Extract user_id and role
   - Add to request extensions

6. Permission Checking:
   - Check if user has specific permission
   - Cache permissions in Redis (5 min TTL)

SECURITY REQUIREMENTS:
- Use Argon2id for password hashing
- JWT secret from environment variable
- Rate limiting on login endpoint (5 attempts per 15 min)
- Log all authentication events
- Secure password reset tokens (expire in 1 hour)

DEPENDENCIES:
```toml
argon2 = "0.5"
jsonwebtoken = "9.2"
validator = { version = "0.16", features = ["derive"] }
```

EXPECTED DELIVERABLES:
1. models/user.rs - User, Role, Permission models
2. services/auth_service.rs - Business logic
3. handlers/auth_handler.rs - HTTP handlers
4. middleware/auth.rs - JWT validation middleware
5. middleware/permission.rs - Permission checking
6. Complete unit tests (80%+ coverage)
7. Integration tests for all endpoints
8. Error handling for all cases
9. OpenAPI documentation for all endpoints

Please implement following our coding standards and include comprehensive error handling.
```

---

### Prompt 3: Sales Transaction Service

```
I'm building the sales tracking module for LazyFranchisor in Rust with Axum.

CONTEXT:
Create a complete sales transaction system with line items, discounts, tax calculation, and real-time inventory updates.

DATABASE SCHEMA:
```sql
CREATE TABLE sales_transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    franchisee_id UUID NOT NULL REFERENCES franchisees(id),
    user_id UUID NOT NULL REFERENCES users(id),
    transaction_number VARCHAR(50) UNIQUE NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    discount_amount DECIMAL(10, 2) DEFAULT 0,
    tax_amount DECIMAL(10, 2) NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    payment_method VARCHAR(20) NOT NULL CHECK (payment_method IN ('cash', 'card', 'digital')),
    payment_reference VARCHAR(100),
    status VARCHAR(20) NOT NULL DEFAULT 'completed' CHECK (status IN ('pending', 'completed', 'cancelled', 'refunded')),
    notes TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE sales_line_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    transaction_id UUID NOT NULL REFERENCES sales_transactions(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    quantity DECIMAL(10, 2) NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    discount DECIMAL(10, 2) DEFAULT 0,
    tax_rate DECIMAL(5, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_sales_franchisee_date ON sales_transactions(franchisee_id, created_at DESC);
CREATE INDEX idx_sales_status ON sales_transactions(status);
CREATE INDEX idx_sales_transaction_number ON sales_transactions(transaction_number);
```

BUSINESS REQUIREMENTS:
1. Create Sale:
   - Validate all line items exist in inventory
   - Check inventory availability (quantity in stock)
   - Calculate subtotal per line item: (quantity × unit_price) - discount
   - Calculate tax per line item: subtotal × tax_rate
   - Calculate transaction totals
   - Generate unique transaction number (format: SALE-YYYYMMDD-XXXX)
   - Deduct inventory quantities (atomic transaction)
   - Create sales transaction with line items
   - Return complete transaction with populated product details

2. List Sales:
   - Filter by franchisee_id (required)
   - Filter by date range (optional)
   - Filter by status (optional)
   - Filter by payment_method (optional)
   - Pagination (cursor-based, 50 items per page)
   - Sort by created_at DESC
   - Include line item count and total amount

3. Get Sale Details:
   - Get transaction by ID
   - Include all line items with product details
   - Include user who created the transaction
   - Calculate derived fields (profit margin if cost data available)

4. Void/Cancel Sale:
   - Only if status = 'completed'
   - Update status to 'cancelled'
   - Restore inventory quantities
   - Log the cancellation with reason
   - Prevent cancellation after 24 hours

5. Sales Summary Report:
   - Aggregate by date range
   - Group by franchisee_id (optional)
   - Calculate: total_sales, total_transactions, average_transaction, top_products
   - Support daily, weekly, monthly grouping

TECHNICAL REQUIREMENTS:
- Use database transactions for atomic operations
- Implement optimistic locking for inventory updates
- Cache product data in Redis (1 hour TTL)
- Use Decimal type for all monetary values
- Real-time inventory synchronization
- Event logging for all sales (for auditing)

VALIDATION:
- Quantity must be > 0
- Unit price must be >= 0
- Discount must be >= 0 and < subtotal
- Payment method must be valid enum
- At least 1 line item required

ERROR HANDLING:
- Insufficient inventory → 400 Bad Request
- Invalid product ID → 404 Not Found
- Transaction not found → 404 Not Found
- Cannot void old transaction → 400 Bad Request
- Database errors → 500 Internal Server Error

EXPECTED DELIVERABLES:
1. models/sales.rs - SalesTransaction, SalesLineItem models
2. services/sales_service.rs - Complete business logic
3. handlers/sales_handler.rs - All HTTP endpoints
4. Database queries with transactions
5. Inventory integration (inventory_service.rs)
6. Unit tests for calculations and business logic
7. Integration tests for all endpoints
8. Load test for concurrent sale creation (JMeter or similar)
9. API documentation

Include comprehensive error handling and logging. Follow our coding standards.
```

---

### Prompt 4: Warehouse Transfer System

```
I'm implementing the warehouse transfer system for LazyFranchisor in Rust.

CONTEXT:
Create a system for managing inventory transfers between central warehouse and franchisee locations, including approval workflow, shipping tracking, and automatic inventory updates.

DATABASE SCHEMA:
```sql
CREATE TABLE stock_transfers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    transfer_number VARCHAR(50) UNIQUE NOT NULL,
    source_location_id UUID REFERENCES locations(id),
    destination_location_id UUID NOT NULL REFERENCES locations(id),
    requested_by_user_id UUID NOT NULL REFERENCES users(id),
    approved_by_user_id UUID REFERENCES users(id),
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'in_transit', 'delivered', 'rejected', 'cancelled')),
    priority VARCHAR(20) DEFAULT 'normal' CHECK (priority IN ('low', 'normal', 'high', 'urgent')),
    notes TEXT,
    approved_at TIMESTAMPTZ,
    shipped_at TIMESTAMPTZ,
    delivered_at TIMESTAMPTZ,
    tracking_number VARCHAR(100),
    carrier VARCHAR(100),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE stock_transfer_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    transfer_id UUID NOT NULL REFERENCES stock_transfers(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    quantity_requested DECIMAL(10, 2) NOT NULL,
    quantity_approved DECIMAL(10, 2),
    quantity_shipped DECIMAL(10, 2),
    quantity_received DECIMAL(10, 2),
    unit_cost DECIMAL(10, 2),
    notes TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_transfers_destination ON stock_transfers(destination_location_id, status);
CREATE INDEX idx_transfers_status ON stock_transfers(status);
```

WORKFLOW STATES:
```
pending → approved → in_transit → delivered
   ↓         ↓
rejected  cancelled
```

BUSINESS REQUIREMENTS:

1. Request Transfer:
   - Franchisee requests stock from central warehouse
   - Specify products and quantities needed
   - Calculate estimated cost
   - Generate transfer number (format: XFER-YYYYMMDD-XXXX)
   - Set status to 'pending'
   - Notify warehouse manager

2. Approve Transfer:
   - Warehouse manager reviews request
   - Can adjust quantities based on availability
   - Check warehouse inventory before approval
   - Set status to 'approved'
   - Record approved_by_user_id and approved_at
   - Optionally set priority

3. Reject Transfer:
   - Provide rejection reason
   - Set status to 'rejected'
   - Notify requester

4. Ship Transfer:
   - Update quantities_shipped (may differ from approved if partial shipment)
   - Deduct from warehouse inventory (atomic transaction)
   - Enter tracking number and carrier
   - Set status to 'in_transit'
   - Record shipped_at timestamp
   - Send notification with tracking info

5. Receive Transfer:
   - Franchisee confirms receipt
   - Update quantities_received
   - Add to franchisee inventory (atomic transaction)
   - Handle discrepancies (received ≠ shipped)
   - Set status to 'delivered'
   - Record delivered_at timestamp
   - Calculate transfer duration metrics

6. Cancel Transfer:
   - Only if status = 'pending' or 'approved'
   - Cannot cancel if already shipped
   - Set status to 'cancelled'
   - Notify all parties

ADDITIONAL FEATURES:

7. Transfer History:
   - List all transfers for a franchisee
   - Filter by status, date range, priority
   - Pagination and sorting
   - Include summary statistics

8. Inventory Reconciliation:
   - Compare quantities_shipped vs quantities_received
   - Flag discrepancies for investigation
   - Generate discrepancy reports

9. Transfer Analytics:
   - Average transfer time by priority
   - Most frequently transferred products
   - Transfer volume trends
   - Warehouse fulfillment rate

TECHNICAL REQUIREMENTS:
- Use database transactions for inventory updates
- Implement state machine for status transitions
- Event sourcing for transfer history (audit trail)
- Real-time notifications (WebSocket or Server-Sent Events)
- Concurrent transfer handling (prevent overselling)
- Retry logic for failed inventory updates

VALIDATION:
- Cannot transfer to same location
- Quantities must be positive
- Cannot skip workflow states
- Only authorized users can approve
- Inventory must be available before shipping

NOTIFICATIONS:
- Email on status changes
- Push notifications to mobile app
- In-app notification center
- SMS for urgent transfers

EXPECTED DELIVERABLES:
1. models/stock_transfer.rs
2. services/stock_transfer_service.rs
3. handlers/stock_transfer_handler.rs
4. State machine implementation
5. Inventory integration with locking
6. Notification service integration
7. Comprehensive unit tests
8. Integration tests for complete workflow
9. API documentation with state diagram
10. Performance tests for concurrent transfers

Follow our coding standards and include extensive error handling.
```

---

## Frontend Development Prompts

### Prompt 5: React Dashboard Component

```
I'm building the main dashboard for LazyFranchisor using React with TypeScript.

CONTEXT:
Create a comprehensive franchise dashboard showing key metrics, recent activity, and quick actions.

DESIGN REFERENCE:
- Modern, clean design similar to Stripe Dashboard
- Use our existing component library (Material-UI / Ant Design)
- Responsive design (desktop, tablet, mobile)
- Dark mode support

API ENDPOINTS:
```typescript
GET /api/v1/dashboard/summary
Response: {
  today_sales: { amount: number, transaction_count: number, change_percent: number },
  weekly_sales: { amount: number, change_percent: number },
  monthly_sales: { amount: number, change_percent: number },
  inventory_alerts: { low_stock_count: number, out_of_stock_count: number },
  pending_transfers: number,
  recent_transactions: Transaction[],
  top_products: { product: Product, quantity: number, revenue: number }[]
}
```

REQUIREMENTS:

1. Layout Structure:
   ```
   Header (Logo, User Menu)
   ├── Sidebar (Navigation)
   └── Main Content
       ├── KPI Cards (4 cards: Today, Week, Month, Inventory)
       ├── Charts Row (Sales Trend Chart + Product Mix Chart)
       ├── Recent Transactions Table
       └── Quick Actions Section
   ```

2. KPI Cards:
   - Today's Sales: Amount, transaction count, % change from yesterday
   - This Week: Total sales, % change from last week
   - This Month: Total sales, % change from last month
   - Inventory Status: Alert count with breakdown
   - Green/Red indicators for positive/negative changes
   - Click to drill down to detailed view

3. Sales Trend Chart:
   - Line chart showing last 30 days of sales
   - Selectable time periods (7d, 30d, 90d, 1y)
   - Hover tooltips with date and amount
   - Compare with previous period (dashed line)

4. Product Mix Chart:
   - Pie chart or bar chart of top 5 products
   - Show product name, quantity sold, revenue
   - Click to view product details

5. Recent Transactions Table:
   - Last 10 transactions
   - Columns: Transaction #, Date/Time, Amount, Payment Method, Status
   - Status badges with colors
   - Click row to view transaction details
   - "View All" button to transactions page

6. Quick Actions:
   - New Sale (button → opens POS modal or navigates to POS)
   - Stock Transfer Request
   - View Reports
   - Inventory Check

7. Real-time Updates:
   - Refresh data every 30 seconds
   - WebSocket connection for instant transaction updates
   - Loading skeletons during data fetch
   - Error handling with retry option

TECHNICAL REQUIREMENTS:

State Management:
```typescript
// Use React Query for data fetching
import { useQuery } from '@tanstack/react-query';

const { data, isLoading, error, refetch } = useQuery({
  queryKey: ['dashboard-summary'],
  queryFn: fetchDashboardSummary,
  refetchInterval: 30000, // 30 seconds
});
```

TypeScript Types:
```typescript
interface DashboardSummary {
  today_sales: SalesMetric;
  weekly_sales: SalesMetric;
  monthly_sales: SalesMetric;
  inventory_alerts: InventoryAlerts;
  pending_transfers: number;
  recent_transactions: Transaction[];
  top_products: TopProduct[];
}

interface SalesMetric {
  amount: number;
  transaction_count?: number;
  change_percent: number;
}

interface InventoryAlerts {
  low_stock_count: number;
  out_of_stock_count: number;
}

interface Transaction {
  id: string;
  transaction_number: string;
  total_amount: number;
  payment_method: 'cash' | 'card' | 'digital';
  status: 'completed' | 'pending' | 'cancelled';
  created_at: string;
}

interface TopProduct {
  product: Product;
  quantity: number;
  revenue: number;
}
```

Component Structure:
```
Dashboard/
├── Dashboard.tsx (main container)
├── Dashboard.test.tsx
├── Dashboard.module.css
├── components/
│   ├── KPICard.tsx
│   ├── SalesTrendChart.tsx
│   ├── ProductMixChart.tsx
│   ├── RecentTransactionsTable.tsx
│   ├── QuickActions.tsx
│   └── LoadingSkeleton.tsx
└── hooks/
    ├── useDashboardData.ts
    └── useRealTimeUpdates.ts
```

PERFORMANCE REQUIREMENTS:
- Initial load < 2 seconds
- Chart rendering < 500ms
- Memoize expensive calculations
- Virtualize long lists
- Lazy load chart libraries
- Code split dashboard from other routes

ACCESSIBILITY:
- ARIA labels for all interactive elements
- Keyboard navigation support
- Screen reader friendly
- Color contrast ratio > 4.5:1

ERROR STATES:
- Network error → Show error banner with retry button
- No data → Show empty state with helpful message
- Partial data → Show what's available, indicate missing data

EXPECTED DELIVERABLES:
1. Complete Dashboard component with all subcomponents
2. TypeScript types and interfaces
3. React Query hooks for data fetching
4. Recharts/Chart.js implementation for visualizations
5. Responsive CSS (mobile-first approach)
6. Unit tests for all components (Jest + React Testing Library)
7. Integration tests for data flow
8. Storybook stories for component documentation
9. Performance optimization (React.memo, useMemo, useCallback)

Follow our React coding standards and accessibility guidelines.
```

---

### Prompt 6: POS (Point of Sale) Interface

```
I'm building a Point of Sale interface for LazyFranchisor using React with TypeScript.

CONTEXT:
Create a full-featured POS system for franchisees to process sales transactions quickly and efficiently.

DESIGN REQUIREMENTS:
- Split-screen layout (Products on left, Cart on right)
- Touch-friendly (large buttons, minimum 44x44px tap targets)
- Fast product search and category filtering
- Barcode scanner support
- Offline capability with sync when online
- Receipt printing

LAYOUT:
```
┌─────────────────────────────────────────────────────────┐
│ Header: Franchisee Name | Current User | Shift Status   │
├──────────────────────┬──────────────────────────────────┤
│                      │  Cart Items                      │
│  Product Search      │  ├─ Product 1  Qty: 2  $20.00   │
│  [Search Bar]        │  ├─ Product 2  Qty: 1  $15.00   │
│                      │  └─ Product 3  Qty: 3  $45.00   │
│  Categories          │                                  │
│  [Food] [Beverage]   │  Subtotal:    $80.00            │
│  [Retail] [Other]    │  Discount:    -$5.00            │
│                      │  Tax (10%):    $7.50            │
│  Product Grid        │  Total:       $82.50            │
│  ┌────┬────┬────┐    │                                  │
│  │Prod│Prod│Prod│    │  [Apply Discount] [Add Note]    │
│  │ 1  │ 2  │ 3  │    │                                  │
│  └────┴────┴────┘    │  [Cash] [Card] [Digital Wallet] │
│  ┌────┬────┬────┐    │                                  │
│  │Prod│Prod│Prod│    │  [Clear Cart] [Complete Sale]   │
│  │ 4  │ 5  │ 6  │    │                                  │
│  └────┴────┴────┘    │                                  │
└──────────────────────┴──────────────────────────────────┘
```

FEATURES:

1. Product Selection:
   - Search by name, SKU, or barcode
   - Filter by category
   - Grid view with product image, name, price
   - Keyboard shortcut support (F1-F12 for favorites)
   - Barcode scanner input (USB or Bluetooth)

2. Cart Management:
   - Add product to cart (click or scan)
   - Update quantity (inline editing or +/- buttons)
   - Remove item from cart
   - Apply discounts (per-item or cart-wide)
   - Add notes to items or entire order
   - Save cart (for later completion)
   - Clear cart (with confirmation)

3. Discount Application:
   - Percentage discount
   - Fixed amount discount
   - Coupon code validation
   - Manager approval for discounts > 20%
   - Discount reason required

4. Payment Processing:
   - Cash: Calculate change
   - Card: Integration with payment terminal (mock for now)
   - Digital wallet: QR code payment
   - Split payment (partial cash + partial card)
   - Payment status tracking

5. Receipt Generation:
   - Auto-print after successful payment
   - Email receipt option
   - SMS receipt option
   - Reprint last receipt
   - Receipt preview before printing

6. Offline Mode:
   - Detect network status
   - Queue transactions locally (IndexedDB)
   - Sync when connection restored
   - Visual indicator of offline status
   - Conflict resolution

API ENDPOINTS:
```typescript
GET /api/v1/products?search={query}&category={id}
POST /api/v1/sales
POST /api/v1/discounts/validate
POST /api/v1/receipts/{id}/email
```

STATE MANAGEMENT:
```typescript
// Redux Toolkit slice
interface POSState {
  cart: CartItem[];
  selectedCategory: string | null;
  searchQuery: string;
  discount: Discount | null;
  paymentMethod: PaymentMethod | null;
  isOffline: boolean;
  queuedTransactions: Transaction[];
}

interface CartItem {
  product: Product;
  quantity: number;
  unitPrice: number;
  discount: number;
  notes?: string;
}

interface Discount {
  type: 'percentage' | 'fixed';
  value: number;
  reason: string;
  approvedBy?: string;
}
```

KEYBOARD SHORTCUTS:
- F1-F12: Quick add favorite products
- Ctrl+F: Focus search
- Ctrl+D: Apply discount
- Ctrl+P: Complete sale
- Esc: Clear cart
- +/-: Adjust quantity of selected item

VALIDATION:
- Cannot complete sale with empty cart
- Payment amount must equal total
- Negative quantities not allowed
- Product availability check before adding
- Manager approval for large discounts

ERROR HANDLING:
- Product not found → Show error, clear search
- Insufficient inventory → Warning, allow override with manager approval
- Payment failed → Retry option, different payment method
- Offline → Queue locally, show sync status

PERFORMANCE:
- Product search debounced (300ms)
- Virtualized product grid for large catalogs
- Optimistic UI updates
- Lazy load product images
- Service worker for offline support

EXPECTED DELIVERABLES:
1. POS component with all subcomponents:
   - ProductSearch.tsx
   - ProductGrid.tsx
   - CategoryFilter.tsx
   - Cart.tsx
   - CartItem.tsx
   - DiscountModal.tsx
   - PaymentModal.tsx
   - ReceiptPreview.tsx
2. Redux Toolkit slice for POS state
3. Custom hooks:
   - useBarcode.ts (barcode scanner integration)
   - useOfflineSync.ts (offline queue management)
   - usePrinter.ts (receipt printing)
4. Service worker for offline capability
5. IndexedDB integration for local storage
6. Complete unit tests (Jest + RTL)
7. Integration tests for complete sale flow
8. E2E tests (Cypress or Playwright)
9. Performance optimization
10. Accessibility compliance (WCAG 2.1 AA)

Follow our coding standards and include comprehensive error handling.
```

---

## Database & Migrations Prompts

### Prompt 7: Database Schema Design

```
I need to design a database schema for [FEATURE_NAME] in the LazyFranchisor system.

CONTEXT:
Create a complete PostgreSQL database schema with tables, relationships, indexes, and constraints.

REQUIREMENTS:
[Describe the feature and data requirements]

ENTITIES:
1. [Entity 1]
   - Fields: [list fields with types and constraints]
   - Relationships: [describe relationships]

2. [Entity 2]
   - Fields: [list fields]
   - Relationships: [describe relationships]

BUSINESS RULES:
- [List business rules that should be enforced at database level]
- [Referential integrity requirements]
- [Data validation constraints]

PERFORMANCE REQUIREMENTS:
- Common queries: [describe typical queries]
- Expected data volume: [estimated row counts]
- Query performance targets: [< X ms for common queries]

EXPECTED DELIVERABLES:
1. Complete CREATE TABLE statements with:
   - Primary keys (UUID with gen_random_uuid())
   - Foreign keys with ON DELETE/ON UPDATE actions
   - CHECK constraints for data validation
   - NOT NULL constraints where appropriate
   - DEFAULT values
   - Timestamps (created_at, updated_at)

2. Indexes for:
   - Foreign keys
   - Commonly queried columns
   - Composite indexes for common query patterns
   - Partial indexes where applicable

3. Triggers for:
   - updated_at timestamp updates
   - Audit logging (if needed)
   - Data validation (if complex)

4. Comments explaining:
   - Table purposes
   - Complex constraints
   - Index rationale

5. Migration file (up and down migrations)

6. Seed data for development/testing

FOLLOW THESE STANDARDS:
- Use UUIDs for primary keys
- snake_case for table and column names
- Plural table names (users, sales_transactions)
- Add created_at and updated_at to all tables
- Use TIMESTAMPTZ for timestamps
- Use DECIMAL(10,2) for currency
- Add appropriate indexes
- Use CHECK constraints for enums
- Add foreign key constraints with cascade rules
- Document complex constraints

Please provide the complete schema with explanations.
```

---

### Prompt 8: Database Migration

```
I need to create a database migration for LazyFranchisor using sqlx.

CONTEXT:
[Describe the change needed]

CURRENT SCHEMA:
```sql
[Provide relevant existing tables]
```

CHANGE REQUIREMENTS:
1. [Describe change #1]
2. [Describe change #2]
3. [Describe change #3]

DATA MIGRATION NEEDS:
- [Any existing data that needs to be transformed]
- [Default values for new columns]
- [Data validation needed]

EXPECTED DELIVERABLES:
1. Up migration (forward migration):
   - ALTER TABLE or CREATE TABLE statements
   - Data migration queries (if needed)
   - Create indexes
   - Add constraints

2. Down migration (rollback):
   - Reverse all changes
   - Restore data (if possible)
   - Remove indexes
   - Drop constraints

3. Testing plan:
   - Test on sample data
   - Verify data integrity
   - Check performance impact
   - Validate constraints

4. Migration safety checklist:
   - No data loss
   - Backward compatible (if applicable)
   - Can be rolled back safely
   - Performance impact assessed

SAFETY REQUIREMENTS:
- No DROP COLUMN without backup
- No data type changes that could truncate data
- Test on production copy first
- Include transaction wrapper if multiple statements
- Add timeout for long-running migrations

NAMING:
File: YYYYMMDDHHMMSS_descriptive_name.sql
Example: 20251029120000_add_phone_to_users.sql

Please provide both up and down migrations with safety checks.
```

---

## Testing & QA Prompts

### Prompt 9: Unit Tests for Backend Service

```
I need comprehensive unit tests for a Rust service in LazyFranchisor.

CONTEXT:
Create unit tests for [SERVICE_NAME] with >80% code coverage.

SERVICE CODE:
```rust
[Provide the service implementation]
```

DEPENDENCIES:
- Database: PostgreSQL (mock with sqlx::MockPool)
- Redis: (mock with redis-test crate)
- External services: [list and provide mock strategy]

TEST REQUIREMENTS:

1. Happy Path Tests:
   - [Test scenario 1]
   - [Test scenario 2]
   - Test all public functions
   - Verify correct return values
   - Verify side effects (database writes, cache updates)

2. Error Handling Tests:
   - Database connection failures
   - Invalid input data
   - Business rule violations
   - Not found scenarios
   - Unauthorized access
   - Concurrent access conflicts

3. Edge Cases:
   - Empty inputs
   - Maximum values
   - Minimum values
   - Boundary conditions
   - Null/None values

4. Integration Points:
   - Mock all database calls
   - Mock all Redis calls
   - Mock external service calls
   - Verify correct parameters passed to mocks

TEST STRUCTURE:
```rust
#[cfg(test)]
mod tests {
    use super::*;
    use mockall::predicate::*;
    use mockall::mock;

    // Mock dependencies
    mock! {
        // Mock trait implementations
    }

    // Helper functions for test data
    fn create_test_user() -> User { ... }
    fn create_test_transaction() -> Transaction { ... }

    // Test cases
    #[tokio::test]
    async fn test_create_success() {
        // Given: Setup mocks and test data
        // When: Call function
        // Then: Assert results
    }

    #[tokio::test]
    async fn test_create_duplicate_error() {
        // Test error case
    }
}
```

COVERAGE REQUIREMENTS:
- Line coverage: >80%
- Branch coverage: >75%
- Function coverage: 100%

EXPECTED DELIVERABLES:
1. Complete test suite with:
   - Setup/teardown functions
   - Mock implementations
   - Test data builders
   - Helper functions
   - Comprehensive assertions

2. Test for each public function
3. Test for each error path
4. Test for edge cases
5. Documentation comments explaining complex test scenarios
6. Performance tests (if applicable)

ASSERTIONS:
- Use assert_eq! for value comparisons
- Use assert! for boolean conditions
- Use matches! for pattern matching
- Check error types and messages
- Verify mock call counts and parameters

Please provide tests following our testing standards with clear Given/When/Then structure.
```

---

### Prompt 10: Integration Tests for API Endpoints

```
I need integration tests for LazyFranchisor API endpoints.

CONTEXT:
Create integration tests for [MODULE_NAME] that test the complete HTTP request/response flow.

API ENDPOINTS TO TEST:
```
POST /api/v1/[resource]
GET /api/v1/[resource]
GET /api/v1/[resource]/{id}
PUT /api/v1/[resource]/{id}
DELETE /api/v1/[resource]/{id}
```

TEST ENVIRONMENT:
- Use test database (separate from development)
- Reset database before each test
- Use real PostgreSQL and Redis instances
- Mock external services only

TEST STRUCTURE:
```rust
// tests/integration/[resource]_tests.rs

use lazyfranchisor::create_app;
use sqlx::PgPool;
use axum::body::Body;
use axum::http::{Request, StatusCode};
use tower::ServiceExt;
use serde_json::json;

async fn setup_test_db() -> PgPool {
    let pool = PgPool::connect("postgres://test:test@localhost/test_db")
        .await
        .unwrap();

    // Run migrations
    sqlx::migrate!("./migrations")
        .run(&pool)
        .await
        .unwrap();

    pool
}

async fn cleanup_test_db(pool: &PgPool) {
    sqlx::query("TRUNCATE TABLE [tables] CASCADE")
        .execute(pool)
        .await
        .unwrap();
}

#[tokio::test]
async fn test_create_[resource]_success() {
    // Given: Setup test database and app
    let pool = setup_test_db().await;
    let app = create_app(pool.clone()).await;

    // Create authentication token
    let token = create_test_token(&pool).await;

    // When: Make POST request
    let response = app
        .oneshot(
            Request::builder()
                .method("POST")
                .uri("/api/v1/[resource]")
                .header("content-type", "application/json")
                .header("authorization", format!("Bearer {}", token))
                .body(Body::from(
                    json!({
                        "field1": "value1",
                        "field2": "value2"
                    }).to_string()
                ))
                .unwrap()
        )
        .await
        .unwrap();

    // Then: Assert response
    assert_eq!(response.status(), StatusCode::CREATED);

    let body = hyper::body::to_bytes(response.into_body())
        .await
        .unwrap();
    let created: [Resource] = serde_json::from_slice(&body).unwrap();

    assert_eq!(created.field1, "value1");
    assert_eq!(created.field2, "value2");
    assert!(created.id.is_some());

    // Verify in database
    let db_record = sqlx::query_as::<_, [Resource]>(
        "SELECT * FROM [resources] WHERE id = $1"
    )
    .bind(created.id)
    .fetch_one(&pool)
    .await
    .unwrap();

    assert_eq!(db_record.field1, created.field1);

    // Cleanup
    cleanup_test_db(&pool).await;
}
```

TEST SCENARIOS:

1. Create Resource:
   - ✅ Valid data → 201 Created
   - ❌ Missing required field → 400 Bad Request
   - ❌ Invalid data type → 400 Bad Request
   - ❌ Duplicate unique field → 409 Conflict
   - ❌ No authentication → 401 Unauthorized
   - ❌ Insufficient permissions → 403 Forbidden

2. Get Resource List:
   - ✅ Empty list → 200 OK with empty array
   - ✅ With results → 200 OK with array
   - ✅ Pagination works → correct page size
   - ✅ Filtering works → filtered results
   - ✅ Sorting works → correct order
   - ❌ No authentication → 401 Unauthorized

3. Get Resource by ID:
   - ✅ Exists → 200 OK with resource
   - ❌ Not found → 404 Not Found
   - ❌ Invalid UUID → 400 Bad Request
   - ❌ No authentication → 401 Unauthorized

4. Update Resource:
   - ✅ Valid data → 200 OK with updated resource
   - ❌ Not found → 404 Not Found
   - ❌ Invalid data → 400 Bad Request
   - ❌ Optimistic locking conflict → 409 Conflict
   - ❌ No permission → 403 Forbidden

5. Delete Resource:
   - ✅ Exists → 204 No Content
   - ❌ Not found → 404 Not Found
   - ❌ Has dependencies → 400 Bad Request
   - ❌ No permission → 403 Forbidden

ADDITIONAL TESTS:
- Rate limiting enforcement
- CORS headers present
- Content-Type validation
- Response time < 500ms (performance assertion)
- Concurrent request handling
- Database transaction rollback on error

EXPECTED DELIVERABLES:
1. Integration test file for each resource
2. Test setup/teardown utilities
3. Test data fixtures
4. Authentication helper functions
5. Response assertion helpers
6. Database verification helpers
7. All tests passing
8. Documentation on running tests

Please implement following our testing standards with comprehensive coverage.
```

---

### Prompt 11: End-to-End (E2E) Tests

```
I need end-to-end tests for LazyFranchisor using Playwright or Cypress.

CONTEXT:
Create E2E tests that simulate real user workflows from login to completion.

USER WORKFLOWS TO TEST:

1. Complete Sales Transaction Flow:
   - User logs in
   - Navigates to POS
   - Searches for product
   - Adds products to cart
   - Applies discount
   - Completes payment
   - Verifies receipt
   - Checks inventory updated

2. Stock Transfer Request Flow:
   - User logs in as franchisee
   - Navigates to inventory
   - Requests stock transfer
   - Fills transfer form
   - Submits request
   - Manager logs in
   - Approves transfer
   - Verifies status updated
   - Notifications sent

3. User Management Flow:
   - Admin logs in
   - Creates new user
   - Assigns role
   - Sets permissions
   - User receives email
   - New user logs in
   - Verifies access

TEST FRAMEWORK: Playwright

STRUCTURE:
```typescript
// e2e/sales-transaction.spec.ts

import { test, expect } from '@playwright/test';

test.describe('Sales Transaction Flow', () => {
  test.beforeEach(async ({ page }) => {
    // Navigate to app
    await page.goto('http://localhost:3000');

    // Login
    await page.fill('[data-testid="email-input"]', 'user@test.com');
    await page.fill('[data-testid="password-input"]', 'password123');
    await page.click('[data-testid="login-button"]');

    // Wait for dashboard
    await expect(page).toHaveURL(/.*dashboard/);
  });

  test('should complete a sale successfully', async ({ page }) => {
    // Navigate to POS
    await page.click('[data-testid="pos-nav-link"]');
    await expect(page).toHaveURL(/.*pos/);

    // Search for product
    await page.fill('[data-testid="product-search"]', 'Burger');
    await page.waitForSelector('[data-testid="product-card"]');

    // Add product to cart
    await page.click('[data-testid="product-card"]:first-child');
    await expect(page.locator('[data-testid="cart-item"]')).toHaveCount(1);

    // Verify cart total
    const total = await page.textContent('[data-testid="cart-total"]');
    expect(total).toContain('$');

    // Apply discount
    await page.click('[data-testid="apply-discount-button"]');
    await page.fill('[data-testid="discount-amount"]', '5');
    await page.fill('[data-testid="discount-reason"]', 'Loyalty reward');
    await page.click('[data-testid="confirm-discount"]');

    // Complete sale with cash
    await page.click('[data-testid="payment-cash"]');
    await page.fill('[data-testid="cash-received"]', '50');
    await page.click('[data-testid="complete-sale"]');

    // Verify success message
    await expect(page.locator('[data-testid="success-message"]'))
      .toHaveText(/Transaction completed/);

    // Verify receipt displayed
    await expect(page.locator('[data-testid="receipt"]')).toBeVisible();

    // Verify transaction in history
    await page.click('[data-testid="sales-nav-link"]');
    await expect(page.locator('[data-testid="transaction-row"]:first-child'))
      .toBeVisible();
  });

  test('should handle insufficient inventory', async ({ page }) => {
    // TODO: Test error case
  });

  test('should validate discount requires approval for >20%', async ({ page }) => {
    // TODO: Test manager approval workflow
  });
});
```

TEST REQUIREMENTS:

For each workflow:
1. Happy path (everything works)
2. Error scenarios (network errors, validation errors)
3. Edge cases (boundary values, empty states)
4. Authentication/authorization checks
5. Responsive design (test on mobile, tablet, desktop)
6. Accessibility (keyboard navigation, screen reader)
7. Performance (page load times)

SELECTORS:
- Use data-testid attributes (not CSS classes or IDs)
- Avoid brittle selectors
- Use semantic selectors when possible

ASSERTIONS:
- Verify URL changes
- Check visibility of elements
- Verify text content
- Check element count
- Validate form values
- Check for error messages
- Verify API calls made (network tab)

EXPECTED DELIVERABLES:
1. E2E test files for each major workflow
2. Test configuration (playwright.config.ts)
3. Test utilities and helpers
4. Page Object Model classes
5. Test fixtures for data
6. Visual regression tests (screenshots)
7. Video recordings of test runs
8. CI/CD integration
9. Documentation on running tests

BEST PRACTICES:
- Keep tests independent (no shared state)
- Use explicit waits (avoid hard-coded delays)
- Clean up test data after each test
- Run in isolated browser contexts
- Parallel execution where possible
- Retry flaky tests (max 2 retries)
- Take screenshots on failure

Please implement comprehensive E2E tests following our standards.
```

---

### Prompt 12: QA Test Plan

```
I need a comprehensive QA test plan for [FEATURE_NAME] in LazyFranchisor.

CONTEXT:
Create a detailed test plan covering functional, non-functional, and regression testing.

FEATURE DESCRIPTION:
[Describe the feature and its requirements]

TEST PLAN STRUCTURE:

1. TEST SCOPE
   1.1 In Scope:
       - [Feature components to test]
       - [Integration points]
       - [User workflows]

   1.2 Out of Scope:
       - [What will not be tested]
       - [Deferred testing]

2. TEST STRATEGY
   2.1 Test Levels:
       - Unit Testing (80% coverage)
       - Integration Testing
       - System Testing
       - User Acceptance Testing (UAT)

   2.2 Test Types:
       - Functional Testing
       - API Testing
       - UI Testing
       - Performance Testing
       - Security Testing
       - Accessibility Testing
       - Compatibility Testing (browsers, devices)

3. TEST SCENARIOS

   3.1 Functional Test Cases:
   ```
   Test Case ID: TC_[MODULE]_001
   Title: [Test case title]
   Preconditions: [Setup required]
   Test Steps:
   1. [Step 1]
   2. [Step 2]
   3. [Step 3]
   Expected Result: [What should happen]
   Actual Result: [To be filled during execution]
   Status: [Pass/Fail/Blocked]
   Priority: [High/Medium/Low]
   ```

   3.2 Negative Test Cases:
   - Invalid inputs
   - Boundary conditions
   - Error handling
   - Unauthorized access

   3.3 Edge Cases:
   - Empty data
   - Maximum limits
   - Concurrent users
   - Network interruptions

4. API TESTING
   4.1 Endpoints to Test:
   - [List all endpoints]

   4.2 For each endpoint test:
   - Request/Response validation
   - Status codes
   - Error responses
   - Authentication
   - Rate limiting
   - Performance (response time < 500ms)

   4.3 Postman Collection:
   ```json
   {
     "info": { "name": "[Feature] API Tests" },
     "item": [
       {
         "name": "Create [Resource]",
         "request": {
           "method": "POST",
           "header": [],
           "body": {},
           "url": ""
         },
         "test": "pm.test(...)"
       }
     ]
   }
   ```

5. PERFORMANCE TESTING
   5.1 Load Testing:
   - Expected concurrent users: [number]
   - Peak load: [number]
   - Response time targets: [metrics]

   5.2 Stress Testing:
   - Breaking point
   - Recovery behavior

   5.3 Tools: JMeter, k6, or Locust

6. SECURITY TESTING
   6.1 Authentication:
   - Token expiration
   - Invalid tokens
   - Session management

   6.2 Authorization:
   - Role-based access control
   - Permission boundaries

   6.3 Input Validation:
   - SQL injection attempts
   - XSS attempts
   - CSRF protection

   6.4 OWASP Top 10 Checklist:
   - [ ] Injection
   - [ ] Broken Authentication
   - [ ] Sensitive Data Exposure
   - [ ] XML External Entities (XXE)
   - [ ] Broken Access Control
   - [ ] Security Misconfiguration
   - [ ] Cross-Site Scripting (XSS)
   - [ ] Insecure Deserialization
   - [ ] Using Components with Known Vulnerabilities
   - [ ] Insufficient Logging & Monitoring

7. ACCESSIBILITY TESTING
   7.1 WCAG 2.1 AA Compliance:
   - [ ] Keyboard navigation
   - [ ] Screen reader support
   - [ ] Color contrast (4.5:1 minimum)
   - [ ] Focus indicators
   - [ ] ARIA labels
   - [ ] Form labels
   - [ ] Error identification

   7.2 Tools:
   - axe DevTools
   - WAVE
   - Lighthouse
   - NVDA/JAWS screen readers

8. COMPATIBILITY TESTING
   8.1 Browsers:
   - [ ] Chrome (latest)
   - [ ] Firefox (latest)
   - [ ] Safari (latest)
   - [ ] Edge (latest)

   8.2 Devices:
   - [ ] Desktop (1920x1080, 1366x768)
   - [ ] Tablet (iPad, Android tablet)
   - [ ] Mobile (iPhone, Android)

9. REGRESSION TESTING
   - Test cases from previous releases
   - Automated regression test suite
   - Smoke tests for quick validation

10. TEST DATA
    - Production-like test data
    - Edge case data
    - Invalid data for negative tests
    - Data privacy considerations

11. DEFECT MANAGEMENT
    - Defect tracking in [Jira/Linear]
    - Severity classification:
      - Critical: Blocker, production down
      - High: Major feature broken
      - Medium: Minor issue, workaround available
      - Low: Cosmetic issue

    - Bug report template:
    ```
    Title: [Clear, concise description]
    Steps to Reproduce:
    1. [Step 1]
    2. [Step 2]
    Expected Result: [What should happen]
    Actual Result: [What actually happened]
    Environment: [Browser, OS, version]
    Screenshots/Video: [Attach]
    Severity: [Critical/High/Medium/Low]
    ```

12. TEST DELIVERABLES
    - Test plan document (this)
    - Test cases
    - Test scripts (automated)
    - Test data
    - Test execution reports
    - Defect reports
    - Test summary report

13. ENTRY/EXIT CRITERIA
    13.1 Entry Criteria:
    - Requirements finalized
    - Test environment ready
    - Test data available
    - Code deployed to test environment

    13.2 Exit Criteria:
    - All test cases executed
    - 95%+ tests passing
    - All critical/high defects resolved
    - Performance metrics met
    - Security scan passed
    - Stakeholder approval

14. TEST SCHEDULE
    - Test planning: [dates]
    - Test case creation: [dates]
    - Test execution: [dates]
    - Defect fixing: [dates]
    - Regression testing: [dates]
    - UAT: [dates]
    - Go-live: [date]

EXPECTED DELIVERABLES:
1. Complete test plan document
2. Test case spreadsheet
3. Automated test scripts
4. Postman API test collection
5. Performance test scenarios (JMeter)
6. Security test checklist
7. Test execution report template
8. Defect tracking template

Please provide a comprehensive test plan following industry best practices.
```

---

## Security & Performance Prompts

### Prompt 13: Security Audit & Penetration Testing

```
I need a security audit for the LazyFranchisor application.

CONTEXT:
Conduct a comprehensive security assessment covering OWASP Top 10 and industry best practices.

SCOPE:
- Backend API (Rust/Axum)
- Frontend (React)
- Database (PostgreSQL)
- Infrastructure (AWS)
- Third-party integrations

SECURITY ASSESSMENT AREAS:

1. AUTHENTICATION & SESSION MANAGEMENT
   - [ ] Password strength requirements
   - [ ] Password hashing algorithm (should be Argon2)
   - [ ] JWT token security (secret strength, expiration)
   - [ ] Refresh token rotation
   - [ ] Account lockout after failed attempts
   - [ ] Session timeout settings
   - [ ] Multi-factor authentication (if applicable)
   - [ ] OAuth implementation (if applicable)

2. AUTHORIZATION & ACCESS CONTROL
   - [ ] Role-Based Access Control (RBAC) implementation
   - [ ] Principle of least privilege enforced
   - [ ] Permission checks on all endpoints
   - [ ] Resource-level access control
   - [ ] API endpoint authorization
   - [ ] Direct object reference protection
   - [ ] Cross-account access prevention

3. INPUT VALIDATION & SANITIZATION
   Test for:
   - [ ] SQL Injection
   - [ ] NoSQL Injection
   - [ ] Command Injection
   - [ ] LDAP Injection
   - [ ] XML Injection
   - [ ] Cross-Site Scripting (XSS)
   - [ ] Path Traversal
   - [ ] File Upload vulnerabilities
   - [ ] Server-Side Request Forgery (SSRF)

4. DATA PROTECTION
   - [ ] Encryption at rest (AES-256)
   - [ ] Encryption in transit (TLS 1.3)
   - [ ] Sensitive data in logs
   - [ ] PII data handling
   - [ ] Credit card data (PCI DSS if applicable)
   - [ ] Data retention policies
   - [ ] Secure data deletion
   - [ ] Database connection encryption

5. API SECURITY
   - [ ] API authentication
   - [ ] Rate limiting implementation
   - [ ] CORS configuration
   - [ ] API versioning
   - [ ] Input size limits
   - [ ] Request/Response validation
   - [ ] API key management
   - [ ] Webhook security

6. FRONTEND SECURITY
   - [ ] XSS protection
   - [ ] CSRF protection
   - [ ] Content Security Policy (CSP)
   - [ ] Secure cookie settings (HttpOnly, Secure, SameSite)
   - [ ] Dependency vulnerabilities (npm audit)
   - [ ] Subresource Integrity (SRI)
   - [ ] Clickjacking protection (X-Frame-Options)
   - [ ] No sensitive data in client-side code

7. DATABASE SECURITY
   - [ ] Parameterized queries (no string concatenation)
   - [ ] Principle of least privilege for DB users
   - [ ] Database encryption
   - [ ] Audit logging enabled
   - [ ] Regular backups
   - [ ] Backup encryption
   - [ ] Database access restrictions

8. INFRASTRUCTURE SECURITY
   - [ ] Server hardening
   - [ ] Firewall configuration
   - [ ] Intrusion detection system
   - [ ] DDoS protection
   - [ ] Regular security patches
   - [ ] Secure configuration management
   - [ ] Network segmentation
   - [ ] VPC configuration

9. LOGGING & MONITORING
   - [ ] Security events logged
   - [ ] Log integrity protection
   - [ ] Centralized logging
   - [ ] Real-time alerting
   - [ ] Audit trail for sensitive operations
   - [ ] Log retention policy
   - [ ] No sensitive data in logs

10. SECRETS MANAGEMENT
    - [ ] No hardcoded secrets
    - [ ] Environment variables for secrets
    - [ ] Secrets rotation policy
    - [ ] AWS Secrets Manager usage
    - [ ] API key management
    - [ ] Encryption keys stored securely

TEST METHODOLOGY:

1. Automated Scanning:
   - OWASP ZAP
   - Burp Suite
   - npm audit / cargo audit
   - Snyk
   - SonarQube

2. Manual Testing:
   - Authentication bypass attempts
   - Authorization escalation attempts
   - Business logic testing
   - Session management testing
   - Cryptography review

3. Code Review:
   - Security-focused code review
   - Dependency analysis
   - Configuration review

DELIVERABLES:
1. Security assessment report with:
   - Executive summary
   - Vulnerability findings (with CVSS scores)
   - Proof of concept for each issue
   - Remediation recommendations
   - Risk prioritization

2. OWASP Top 10 compliance matrix

3. Penetration testing report

4. Remediation roadmap with timeline

5. Secure coding guidelines (if gaps found)

SEVERITY CLASSIFICATION:
- Critical: Immediate data breach risk (SQL injection, auth bypass)
- High: Significant security impact (XSS, CSRF, sensitive data exposure)
- Medium: Potential security issue (weak crypto, info disclosure)
- Low: Minor security concern (missing headers, verbose errors)

Please provide a comprehensive security assessment with detailed findings and recommendations.
```

---

### Prompt 14: Performance Optimization

```
I need to optimize the performance of [FEATURE/MODULE] in LazyFranchisor.

CONTEXT:
Analyze and improve performance to meet our targets:
- API response time: <500ms (95th percentile)
- Page load time: <2s (First Contentful Paint)
- Database query time: <100ms
- Time to Interactive: <3s

CURRENT PERFORMANCE:
[Provide current metrics if available]

AREAS TO OPTIMIZE:

1. BACKEND PERFORMANCE

   Database Optimization:
   - [ ] Add missing indexes
   - [ ] Optimize slow queries (use EXPLAIN ANALYZE)
   - [ ] Implement query result caching
   - [ ] Use connection pooling
   - [ ] Batch operations where possible
   - [ ] Denormalize for read-heavy operations
   - [ ] Implement database sharding (if needed)

   Application Code:
   - [ ] Profile with flamegraph
   - [ ] Optimize hot paths
   - [ ] Reduce allocations
   - [ ] Use async/await efficiently
   - [ ] Implement request coalescing
   - [ ] Add response compression
   - [ ] Optimize serialization/deserialization

   Caching Strategy:
   - [ ] Implement Redis caching
   - [ ] Cache API responses
   - [ ] Cache database queries
   - [ ] Cache computed values
   - [ ] Set appropriate TTLs
   - [ ] Implement cache warming
   - [ ] Cache invalidation strategy

2. FRONTEND PERFORMANCE

   Loading Optimization:
   - [ ] Code splitting (route-based)
   - [ ] Lazy loading components
   - [ ] Dynamic imports
   - [ ] Tree shaking
   - [ ] Bundle size analysis
   - [ ] Remove unused dependencies
   - [ ] Use production builds

   Rendering Optimization:
   - [ ] Use React.memo for expensive components
   - [ ] useMemo for expensive calculations
   - [ ] useCallback for function props
   - [ ] Virtualize long lists
   - [ ] Debounce/throttle user inputs
   - [ ] Avoid unnecessary re-renders
   - [ ] Optimize images (WebP, lazy loading)

   Asset Optimization:
   - [ ] Minify JavaScript
   - [ ] Minify CSS
   - [ ] Optimize images (compression, format)
   - [ ] Use CDN for static assets
   - [ ] Enable browser caching
   - [ ] Implement service worker
   - [ ] Use HTTP/2

3. DATABASE QUERY OPTIMIZATION

   Slow Query Example:
   ```sql
   EXPLAIN ANALYZE
   SELECT s.id, s.total_amount, s.created_at,
          f.name, COUNT(li.id) AS item_count
   FROM sales_transactions s
   JOIN franchisees f ON s.franchisee_id = f.id
   LEFT JOIN sales_line_items li ON s.id = li.transaction_id
   WHERE s.created_at >= NOW() - INTERVAL '30 days'
     AND f.is_active = true
   GROUP BY s.id, f.name
   ORDER BY s.created_at DESC
   LIMIT 100;
   ```

   Optimization steps:
   1. Analyze current query plan
   2. Identify sequential scans
   3. Add appropriate indexes
   4. Rewrite query if needed
   5. Test with production-like data volume
   6. Verify improvement

4. API RESPONSE TIME OPTIMIZATION

   Current bottlenecks:
   - [List slow endpoints]
   - [Typical response times]

   Optimization strategies:
   - [ ] Pagination for large result sets
   - [ ] Field filtering (allow clients to request specific fields)
   - [ ] Response compression (gzip/brotli)
   - [ ] API response caching
   - [ ] Parallel query execution
   - [ ] Optimize serialization
   - [ ] Connection keep-alive

5. N+1 QUERY PROBLEM

   Example problem:
   ```rust
   // BAD: N+1 queries
   let sales = get_all_sales().await?;
   for sale in sales {
       let user = get_user_by_id(sale.user_id).await?; // N queries
   }
   ```

   Fix:
   ```rust
   // GOOD: Single query with JOIN
   let sales_with_users = sqlx::query_as::<_, SaleWithUser>(
       "SELECT s.*, u.name as user_name
        FROM sales_transactions s
        JOIN users u ON s.user_id = u.id"
   )
   .fetch_all(&pool)
   .await?;
   ```

6. MONITORING & PROFILING

   Backend:
   - [ ] Add request timing middleware
   - [ ] Prometheus metrics
   - [ ] Grafana dashboards
   - [ ] Slow query logging
   - [ ] APM tool (New Relic, DataDog)

   Frontend:
   - [ ] Web Vitals monitoring
   - [ ] Real User Monitoring (RUM)
   - [ ] Lighthouse CI
   - [ ] Bundle analyzer
   - [ ] React DevTools Profiler

PERFORMANCE TESTING:
1. Load Testing (k6):
   ```javascript
   import http from 'k6/http';
   import { check, sleep } from 'k6';

   export let options = {
     stages: [
       { duration: '2m', target: 100 }, // Ramp up
       { duration: '5m', target: 100 }, // Stay at 100 users
       { duration: '2m', target: 0 },   // Ramp down
     ],
     thresholds: {
       http_req_duration: ['p(95)<500'], // 95% of requests must complete below 500ms
     },
   };

   export default function () {
     let res = http.get('http://api.lazyfranchisor.com/api/v1/sales');
     check(res, { 'status is 200': (r) => r.status === 200 });
     sleep(1);
   }
   ```

2. Benchmark Results:
   ```
   Before Optimization:
   - Avg response time: 1200ms
   - p95 response time: 2500ms
   - Requests/sec: 50

   Target:
   - Avg response time: <300ms
   - p95 response time: <500ms
   - Requests/sec: >200
   ```

DELIVERABLES:
1. Performance audit report
2. Flamegraphs and profiling data
3. Query optimization recommendations
4. Before/after benchmark results
5. Optimized code implementation
6. Performance monitoring dashboard
7. Optimization guide/documentation
8. Performance budget for future development

Please provide detailed performance analysis with specific optimizations and measurable results.
```

---

## DevOps & Infrastructure Prompts

### Prompt 15: CI/CD Pipeline Setup

```
I need to set up a complete CI/CD pipeline for LazyFranchisor.

CONTEXT:
Create automated pipelines for building, testing, and deploying to staging and production environments using GitHub Actions.

REQUIREMENTS:

PIPELINE STAGES:
1. Code Quality (on PR)
2. Build (on push)
3. Test (on push)
4. Deploy to Staging (on merge to develop)
5. Deploy to Production (on merge to main, with manual approval)

ENVIRONMENTS:
- Development: Local
- Staging: AWS (dev.lazyfranchisor.com)
- Production: AWS (app.lazyfranchisor.com)

PIPELINE 1: PR Checks (.github/workflows/pr-checks.yml)

```yaml
name: PR Checks

on:
  pull_request:
    branches: [develop, main]

jobs:
  code-quality:
    # Check formatting, linting, security

  unit-tests:
    # Run unit tests

  integration-tests:
    # Run integration tests

  build:
    # Build application
```

Requirements:
- Rust: cargo fmt --check, cargo clippy, cargo test
- Frontend: npm run lint, npm test
- Security: cargo audit, npm audit
- Fail PR if any check fails
- Show coverage report in PR comment
- Fail if coverage drops below 80%

PIPELINE 2: Deploy to Staging (.github/workflows/deploy-staging.yml)

```yaml
name: Deploy to Staging

on:
  push:
    branches: [develop]

jobs:
  deploy:
    # Build Docker images
    # Push to ECR
    # Deploy to ECS/Kubernetes
    # Run smoke tests
    # Notify team
```

Requirements:
- Build Docker images for backend and frontend
- Tag with git commit SHA
- Push to AWS ECR
- Update ECS task definition
- Deploy with rolling update (zero downtime)
- Run smoke tests after deployment
- Rollback automatically if smoke tests fail
- Send Slack notification on success/failure

PIPELINE 3: Deploy to Production (.github/workflows/deploy-production.yml)

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    environment:
      name: production
      url: https://app.lazyfranchisor.com
    # Require manual approval
    # Run full test suite
    # Database migration
    # Blue-green deployment
    # Health checks
    # Notify stakeholders
```

Requirements:
- Require manual approval from CTO
- Run full test suite before deployment
- Run database migrations
- Blue-green deployment strategy
- Health checks before switching traffic
- Automatic rollback on failure
- Comprehensive deployment notifications
- Tag release with version number

DOCKER CONFIGURATION:

Backend Dockerfile:
```dockerfile
# Multi-stage build for Rust
FROM rust:1.70 AS builder
WORKDIR /app
COPY Cargo.toml Cargo.lock ./
COPY src ./src
RUN cargo build --release

FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y ca-certificates
COPY --from=builder /app/target/release/lazyfranchisor /usr/local/bin/
EXPOSE 3000
CMD ["lazyfranchisor"]
```

Frontend Dockerfile:
```dockerfile
# Multi-stage build for React
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```

AWS INFRASTRUCTURE:

```hcl
# Terraform for ECS deployment
resource "aws_ecs_cluster" "main" {
  name = "lazyfranchisor-${var.environment}"
}

resource "aws_ecs_task_definition" "api" {
  family                   = "lazyfranchisor-api"
  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = 512
  memory                   = 1024

  container_definitions = jsonencode([{
    name  = "api"
    image = "${aws_ecr_repository.api.repository_url}:${var.image_tag}"
    portMappings = [{
      containerPort = 3000
      protocol      = "tcp"
    }]
    environment = [
      { name = "DATABASE_URL", value = var.database_url },
      { name = "REDIS_URL", value = var.redis_url }
    ]
    secrets = [
      { name = "JWT_SECRET", valueFrom = aws_secretsmanager_secret.jwt_secret.arn }
    ]
    logConfiguration = {
      logDriver = "awslogs"
      options = {
        "awslogs-group"         = "/ecs/lazyfranchisor-api"
        "awslogs-region"        = var.aws_region
        "awslogs-stream-prefix" = "ecs"
      }
    }
  }])
}
```

MONITORING & ALERTS:
- CloudWatch logs for all services
- Prometheus metrics endpoint
- Grafana dashboards
- PagerDuty integration for production alerts
- Slack notifications for all deployments

ROLLBACK PROCEDURE:
```bash
# Automated rollback script
#!/bin/bash
aws ecs update-service \
  --cluster lazyfranchisor-production \
  --service api \
  --task-definition lazyfranchisor-api:PREVIOUS_REVISION \
  --force-new-deployment
```

DELIVERABLES:
1. Complete GitHub Actions workflows
2. Dockerfiles for all services
3. docker-compose.yml for local development
4. Terraform/CloudFormation for infrastructure
5. Deployment scripts
6. Rollback procedures
7. Monitoring configuration
8. Documentation on deployment process
9. Runbook for common issues

Please implement a robust CI/CD pipeline following DevOps best practices.
```

---

## Feature-Specific Prompts

### Prompt 16: Complete Feature Implementation

```
I need to implement the [FEATURE_NAME] feature for LazyFranchisor from scratch.

FEATURE DESCRIPTION:
[Detailed description of the feature]

REQUIREMENTS:
[List all functional and non-functional requirements]

USER STORIES:
1. As a [role], I want [action] so that [benefit]
2. As a [role], I want [action] so that [benefit]

ACCEPTANCE CRITERIA:
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

TECHNICAL SPECIFICATIONS:

1. DATABASE SCHEMA:
```sql
-- Provide table definitions
```

2. API ENDPOINTS:
```
POST   /api/v1/[resource]
GET    /api/v1/[resource]
GET    /api/v1/[resource]/{id}
PUT    /api/v1/[resource]/{id}
DELETE /api/v1/[resource]/{id}
```

3. FRONTEND PAGES:
- List page (table with pagination, filtering, sorting)
- Detail page (view single record)
- Create/Edit form
- Dashboard widget (if applicable)

4. BUSINESS RULES:
- [Rule 1]
- [Rule 2]
- [Rule 3]

5. INTEGRATION POINTS:
- [External service 1]
- [External service 2]
- [Internal module dependencies]

IMPLEMENTATION PLAN:

Phase 1: Backend (Week 1)
- [ ] Create database migration
- [ ] Create data models
- [ ] Implement service layer
- [ ] Create API handlers
- [ ] Write unit tests
- [ ] Write integration tests
- [ ] Document API (OpenAPI)

Phase 2: Frontend (Week 2)
- [ ] Create TypeScript types
- [ ] Implement API client
- [ ] Create list view component
- [ ] Create detail view component
- [ ] Create form component
- [ ] Implement state management
- [ ] Write component tests

Phase 3: Integration & Testing (Week 3)
- [ ] End-to-end testing
- [ ] Performance testing
- [ ] Security testing
- [ ] Bug fixes
- [ ] Code review
- [ ] Documentation

EXPECTED DELIVERABLES:
1. Complete backend implementation:
   - Migration files
   - Models (models/[feature].rs)
   - Service (services/[feature]_service.rs)
   - Handlers (handlers/[feature]_handler.rs)
   - Unit tests (>80% coverage)
   - Integration tests
   - API documentation

2. Complete frontend implementation:
   - TypeScript types
   - API client (services/[feature]-api.ts)
   - React components
   - Redux/state management
   - Component tests
   - Storybook stories

3. Documentation:
   - Feature documentation
   - API documentation
   - User guide
   - Developer notes

4. Tests:
   - Unit tests (backend & frontend)
   - Integration tests
   - E2E tests
   - Performance tests

5. Deployment:
   - Database migration plan
   - Feature flags (if needed)
   - Rollout strategy
   - Monitoring dashboards

Please provide complete, production-ready implementation following our coding standards and best practices. Include comprehensive error handling, logging, and tests.
```

---

## Additional QA Prompts

### Prompt 17: Accessibility (a11y) Testing

```
I need to audit and improve accessibility for [COMPONENT/PAGE] in LazyFranchisor.

CONTEXT:
Ensure WCAG 2.1 AA compliance for inclusive design.

AUDIT CHECKLIST:

1. KEYBOARD NAVIGATION
   - [ ] All interactive elements accessible via Tab
   - [ ] Logical tab order
   - [ ] Skip links for main content
   - [ ] No keyboard traps
   - [ ] Focus indicators visible
   - [ ] Escape key closes modals/dropdowns

2. SCREEN READER SUPPORT
   - [ ] Semantic HTML (header, nav, main, article, aside, footer)
   - [ ] ARIA labels on all buttons/links
   - [ ] ARIA landmarks
   - [ ] Form labels associated with inputs
   - [ ] Error messages announced
   - [ ] Loading states announced
   - [ ] Dynamic content updates announced (aria-live)

3. VISUAL
   - [ ] Color contrast ≥ 4.5:1 (normal text)
   - [ ] Color contrast ≥ 3:1 (large text)
   - [ ] Information not conveyed by color alone
   - [ ] Text resizable to 200%
   - [ ] No horizontal scrolling at 320px width
   - [ ] Focus indicators visible

4. FORMS
   - [ ] Labels for all form controls
   - [ ] Required fields indicated
   - [ ] Error messages clear and specific
   - [ ] Error messages associated with fields
   - [ ] Autocomplete attributes
   - [ ] Fieldset/legend for groups

5. IMAGES & MEDIA
   - [ ] Alt text for images
   - [ ] Decorative images marked (alt="")
   - [ ] Captions for videos
   - [ ] Transcripts for audio

TESTING TOOLS:
1. Automated:
   - axe DevTools browser extension
   - WAVE
   - Lighthouse accessibility audit
   - pa11y CI

2. Manual:
   - Keyboard-only navigation
   - Screen reader testing (NVDA/JAWS/VoiceOver)
   - Zoom to 200%
   - High contrast mode

FIXES NEEDED:
[For each issue found, provide:]
- Issue description
- WCAG criterion violated
- Severity (A, AA, AAA)
- Recommended fix with code example

Example Fix:
```tsx
// BEFORE (not accessible)
<div onClick={handleClick}>Submit</div>

// AFTER (accessible)
<button
  onClick={handleClick}
  aria-label="Submit form"
  type="submit"
>
  Submit
</button>
```

DELIVERABLES:
1. Accessibility audit report
2. List of issues with severity
3. Code fixes for each issue
4. Updated components
5. Accessibility testing guide
6. WCAG 2.1 compliance statement

Please provide comprehensive accessibility improvements.
```

---

### Prompt 18: Mobile Responsiveness Testing

```
I need to test and optimize [PAGE/COMPONENT] for mobile devices.

DEVICES TO TEST:
- iPhone 13/14 (390x844)
- iPhone SE (375x667)
- Samsung Galaxy S21 (360x800)
- iPad (768x1024)
- iPad Pro (1024x1366)

RESPONSIVE DESIGN REQUIREMENTS:

1. LAYOUT
   - [ ] Single column on mobile (<768px)
   - [ ] Two columns on tablet (768-1024px)
   - [ ] Multi-column on desktop (>1024px)
   - [ ] No horizontal scrolling
   - [ ] Content readable without zooming
   - [ ] Touch targets ≥44x44px

2. NAVIGATION
   - [ ] Hamburger menu on mobile
   - [ ] Bottom navigation (if applicable)
   - [ ] Swipe gestures work
   - [ ] Pull-to-refresh (if applicable)

3. FORMS
   - [ ] Input fields stack vertically
   - [ ] Appropriate input types (tel, email, number)
   - [ ] Large enough tap targets
   - [ ] Zoom not disabled on inputs
   - [ ] Keyboard doesn't hide inputs

4. TABLES
   - [ ] Horizontal scroll for wide tables
   - [ ] Or cards view on mobile
   - [ ] Sticky column headers (if scrollable)

5. IMAGES
   - [ ] Responsive images (srcset)
   - [ ] Lazy loading
   - [ ] Optimized for mobile (smaller file size)

6. PERFORMANCE
   - [ ] First Contentful Paint <2s on 3G
   - [ ] Time to Interactive <5s on 3G
   - [ ] Lighthouse mobile score >90

MEDIA QUERIES:
```css
/* Mobile First */
.container {
  padding: 16px;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 24px;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 32px;
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

TESTING CHECKLIST:
- [ ] Test on real devices
- [ ] Chrome DevTools device emulation
- [ ] Rotate device (portrait/landscape)
- [ ] Touch interactions work
- [ ] Gestures work (swipe, pinch)
- [ ] No layout shifts (CLS < 0.1)
- [ ] Text readable (≥16px)
- [ ] Adequate spacing between elements

DELIVERABLES:
1. Responsive design report
2. Screenshots on various devices
3. CSS fixes
4. Component updates
5. Performance improvements

Please provide comprehensive mobile optimization.
```

---

**Document Control:**
- **Version:** 1.0
- **Last Updated:** October 29, 2025
- **Next Review:** As needed during development
- **Owner:** CTO / Engineering Lead

*End of AI Development Prompts*
