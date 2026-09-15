# PRD: Stock and Sales Management Application

**Status:** Draft v0.2
**Owner:** Smart and Boss
**Members:**
- 67011075 — Akarawin Chumniprasertkun
- 68011958 — Teetut Iamsoonthorn
**Last updated:** 2026-09-15


prd.md — What the system should do

PRD explains what the project is, why we are building it, and what the system needs to do.

Our project is a Stock + Sales Management System.

Staff can record sales.
Stock is automatically deducted when a sale is made.
Stock staff can update and add stock.
The system has login and role-based permissions.
The CEO can access all information.
The Dashboard shows company sales and salesperson performance using graphs.
The system keeps an Audit Log showing who did what and when.
Data can be exported as CSV.
The system supports Thai and English.

---

## 1. What — Summary

Stock and Sales Management is an internal web application for managing
inventory and sales information.

The system allows staff to update stock quantities, record sales,
automatically deduct sold quantities from stock, and track sales performance.

The system also provides a dashboard for the CEO/Manager to view an overall
summary of company performance, inventory status, and individual salesperson
performance.

- **Stack:** Next.js 14 (App Router) + TypeScript + Tailwind CSS
- **Data storage:** SQLite via `better-sqlite3`, single local database file
- **Deployment (v1):** Local only — runs on a machine on-site

---

## 2. Why — Problem & Motivation

Currently, stock and sales information may be managed using paper documents
or spreadsheets. This can make it difficult to:

- Track current stock accurately
- Know which products are running low
- Keep sales information organized
- Review salesperson performance
- See an overall picture of company sales
- Determine who changed or entered information
- Make purchasing and restocking decisions quickly

**Goal:** provide a simple system for staff to record stock and sales
information while giving the CEO/Manager a clear overview of inventory and
sales performance.

---

## 3. Who — Users / Personas

### CEO / Manager (Admin)

The CEO/Manager has access to all information and system management functions.

Key needs:

- View all stock information
- View all sales information
- View company-wide dashboard
- View salesperson performance
- Manage users
- Manage SKUs
- Correct stock or sales information when necessary
- View complete audit history
- Export reports and data

### Staff / Operator

Operators have specific responsibilities within the company.

Each user can only access functions and information relevant to their assigned
responsibilities.

Examples of responsibilities may include:

- Salesperson
- Stock/warehouse staff
- Other operational staff

A user must log in before accessing the system.

Users cannot access another user's private or restricted information unless
they have sufficient permissions.

---

## 4. Authentication & Permission Model

### 4.1 Authentication

- All users must log in using username/password.
- There is no public/self-registration.
- User accounts are created and managed by an Admin.
- Passwords must be stored securely as password hashes.

### 4.2 Permissions

The system has two main access levels:

- **Admin:** CEO/Manager with full system access.
- **Operator:** staff member with access based on their assigned responsibility.

Operators should only see and modify information they are authorized to access.

The system must perform permission checks at both route and action level.

---

## 5. Core Features

### 5.1 SKU Management

The system allows authorized users to manage products/SKUs.

Each SKU contains:

- SKU code
- Name
- Description
- Type
- Unit
- Low-stock threshold
- Stale-check threshold
- Current quantity
- Automatically generated QR code
- Active/inactive status

Authorized users can:

- Create SKU
- Edit SKU
- Deactivate SKU
- Search SKU
- View SKU details
- Scan QR code to quickly identify a SKU

Operators are allowed to create new SKUs according to the group's permission
decision, while unauthorized users cannot perform SKU management actions.

---

### 5.2 Stock Update

Staff can update the current quantity of a SKU.

A stock update records:

- SKU
- Previous quantity
- New quantity
- User who performed the action
- Date/time
- Action type

The system must validate that stock quantity cannot become negative.

---

### 5.3 Add Stock / Restock

Staff can record newly received stock separately from a normal stock count.

Example:

Current stock = 50

Add stock = 20

New stock = 70

The system records:

- SKU
- Quantity added
- Previous quantity
- New quantity
- User
- Date/time
- Action type

---

### 5.4 Sales Recording

The system must support recording a complete sales transaction.

One sale can contain multiple products/items.

A sales transaction may contain:

#### Sale information

- Document/invoice number
- Customer ID
- Customer name
- Salesperson
- Total amount
- Payment information/status
- System-generated date/time

#### Sale items

Each item contains:

- SKU/product code
- Product description
- Quantity
- Unit
- Unit price
- Item total

The system calculates the item total and overall sale total.

Users do not need to manually enter the sale date.
The system records the date/time automatically.

---

### 5.5 Automatic Stock Deduction

When a sale is successfully recorded, the system automatically deducts the
sold quantity from the corresponding SKU stock.

Example:

Current stock = 100

Sold = 15

New stock = 85

The system must ensure that a sale cannot cause stock quantity to become
negative.

Stock deduction and sale recording should be performed as a single database
transaction so that the system does not record a sale without updating stock,
or update stock without recording the sale.

---

### 5.6 Dashboard

The dashboard provides a simple visual overview of company performance.

### CEO/Manager Dashboard

The CEO can view:

- Total number of SKUs
- Current stock status
- Low-stock items
- Stale stock items
- Total sales
- Sales performance by salesperson
- Sales trends over time
- Other important company-level summaries

The dashboard should use graphs and charts where appropriate so that important
information can be understood quickly.

Examples:

- Total sales by month
- Sales by salesperson
- Sales trend over time
- Low-stock summary
- Top-selling products

### Time Filters

The CEO should be able to select a time period such as:

- Today
- Last 7 days
- This month
- Last month
- Custom date range

---

### 5.7 Salesperson Performance

Salesperson performance is primarily measured by sales value.

The system should allow the CEO to compare salespeople based on:

- Total sales amount
- Sales amount during a selected time period
- Sales trend over time

The dashboard should present this information using easy-to-read graphs.

---

### 5.8 Export

The system supports exporting data for external reporting.

Supported format:

- CSV

The system should also support exporting a visual report containing important
dashboard information and graphs when technically feasible.

The exported report should be suitable for management review.

---

### 5.9 Audit Log

Every important stock and sales-changing action must be recorded.

The audit log records:

- User
- Action
- Affected data
- Previous value
- New value
- Date/time

Examples of actions:

- Create SKU
- Edit SKU
- Deactivate SKU
- Update stock
- Add stock
- Create sale
- Modify sale
- Correct stock
- Correct sales information
- User management actions

Audit history must not be silently deleted when information is corrected.

This allows the CEO/Manager to review who performed an action and what was
changed.

The purpose is to improve accountability and help detect unauthorized or
suspicious changes.

---

## 6. User Flow

### 6.1 CEO/Manager

1. CEO logs in.
2. CEO views the dashboard.
3. CEO selects a time period.
4. System displays company-wide sales and inventory information.
5. CEO can view salesperson performance.
6. CEO can manage users and SKUs.
7. CEO can review audit history.
8. CEO can export data/reports.

### 6.2 Salesperson

1. Salesperson logs in.
2. Salesperson accesses functions allowed for their responsibility.
3. Salesperson creates a sales transaction.
4. Salesperson selects the customer.
5. Salesperson adds one or more products.
6. System calculates the sale total.
7. System records the sale.
8. System automatically deducts sold quantities from stock.

### 6.3 Stock Staff

1. Staff logs in.
2. Staff searches for or scans a SKU QR code.
3. Staff views the current stock.
4. Staff performs a weekly stock update or records new stock.
5. System validates the quantity.
6. System saves the change.
7. System records the action in the audit log.

---

## 7. Functional Requirements

| ID | Requirement |
| FR1 | Users must log in with username/password before accessing protected pages. |
| FR2 | System must support Admin and Operator access levels. |
| FR3 | Admin can manage users and system data. |
| FR4 | Operators can only access functions permitted by their assigned responsibility. |
| FR5 | Each SKU must contain name, type, unit, thresholds, current quantity, and QR code. |
| FR6 | Authorized users can create, edit, and deactivate SKUs. |
| FR7 | Authorized staff can update SKU quantities. |
| FR8 | Authorized staff can add stock without overwriting the current quantity. |
| FR9 | Stock quantity must never become negative. |
| FR10 | Every stock-changing action must record the user and timestamp. |
| FR11 | System must support sales transactions containing multiple sale items. |
| FR12 | Each sale item must contain SKU, quantity, unit price, and item total. |
| FR13 | System must calculate the total value of a sale. |
| FR14 | System must automatically deduct sold quantities from stock. |
| FR15 | Sale recording and stock deduction must be performed safely as one database transaction. |
| FR16 | System must record salesperson information for each sale. |
| FR17 | Dashboard must show company-level sales information. |
| FR18 | Dashboard must show salesperson sales performance. |
| FR19 | Dashboard must support time-period filtering. |
| FR20 | Dashboard must show low-stock and stale-stock warnings. |
| FR21 | System must support QR-code-based SKU identification. |
| FR22 | System must support configurable SKU Types and Units. |
| FR23 | Admin must be able to review audit history. |
| FR24 | Corrected data must retain an audit history of previous values and changes. |
| FR25 | System must support CSV export. |
| FR26 | System should support exporting management reports containing dashboard graphs when feasible. |
| FR27 | System must support Thai and English UI. |
| FR28 | Users must be able to switch between Thai and English using a language-switching control. |

---

## 8. Non-Functional Requirements

| ID | Requirement |
| NFR1 | **Usability:** UI must be simple enough for staff with low technology familiarity. |
| NFR2 | **Performance:** Dashboard and SKU lists should load in under 2 seconds with up to approximately 1,000 SKUs on local hardware. |
| NFR3 | **Reliability:** SQLite must safely handle concurrent writes using database transactions. |
| NFR4 | **Data integrity:** Stock quantity must never become negative. |
| NFR5 | **Portability:** Application and database must run locally on a single machine without an external server. |
| NFR6 | **Auditability:** Important data-changing actions must be traceable to a user and timestamp. |
| NFR7 | **Accessibility:** Text, numbers, warnings, and dashboard graphs must be clearly readable on common desktop/tablet displays. |
| NFR8 | **Security:** Passwords must not be stored as plain text. |
| NFR9 | **Localization:** The system must support Thai and English and allow users to switch languages. |

---

## 9. Out of Scope (v1)

- Cloud hosting
- External/cloud database
- Native mobile application
- Public self-service registration
- Email notifications
- SMS/OTP authentication
- OAuth/third-party authentication
- Multi-location/multi-warehouse support
- Complex approval workflows
- Integration with external accounting systems
- Online payment gateway integration

---

## 10. Success Criteria / Metrics

| Metric | Target |
|---|---|
| System availability | Application runs successfully on the local machine. |
| Stock update usability | An Operator can complete a stock update in under 2 minutes without assistance. |
| Sales recording usability | A salesperson can complete a normal multi-item sales transaction quickly and accurately. |
| Stock accuracy | Stock is automatically updated correctly after every successful sale. |
| Warning accuracy | Low-stock and stale-stock warnings correctly reflect current data. |
| Auditability | 100% of stock and sales-changing actions have a user and timestamp recorded. |
| Dashboard usability | CEO can understand key company sales and inventory information at a glance. |
| Performance | Dashboard remains responsive with approximately 1,000 SKUs. |
| Export | Users with permission can successfully export CSV data. |
| Localization | Users can switch between Thai and English without restarting the application. |

---

## 11. Tech Constraints

### Must use

- Next.js 14 (App Router)
- TypeScript
- Tailwind CSS
- SQLite via `better-sqlite3`
- Single local `.db` file
- QR code generation library such as `qrcode`

### Must not use / avoid in v1

- External/cloud database
- Cloud hosting
- Third-party authentication providers
- Email/SMS notification services
- External payment gateway

---

## 12. Suggested Data Model
	User (id, name, username, password_hash, access_level[admin|operator], responsibility, created_at, is_active)
	SKU (id, code, name, description, type, unit, low_stock_threshold, stale_days_threshold, current_quantity, qr_code, created_at, is_active)
	Customer (id, customer_code, name, created_at)
	Sale (id, document_number, customer_id, salesperson_id, total_amount, payment_status, created_at)
	SaleItem (id, sale_id, sku_id, quantity, unit_price, total_amount)
	StockEvent (id, sku_id, user_id, type[update|add|sale|correction], quantity_before, quantity_after, created_at)
	AuditLog (id, user_id, action, entity_type, entity_id, old_value, new_value, created_at)

=======================================================================================================================================================================


# agents.md — Rules for AI Coding Agents

This file defines the rules for AI agents and contributors working in this
repository.

Related docs: `prd.md`, `architecture.md`, `schema.md`,
`implementation-plan.md`, `progress.md`.

agents.md — Rules for developing the system

Agents defines the rules that developers or AI coding agents must follow when working on the project.

For example:

Use Next.js, TypeScript, Tailwind CSS, and SQLite.
Stock must never become negative.
Sales must automatically deduct stock.
Sale recording and stock deduction must happen safely together.
Important changes must be recorded in the Audit Log.
User permissions must always be checked.
Do not change the technology or database without approval.
Code must be tested and the project must build successfully.

In short: agents.md = "What rules must we follow when writing the code?"

---

## 1. Standing Conventions

- Use Next.js 14 with App Router.
- Use TypeScript with strict mode.
- Use Tailwind CSS for styling.
- Use `better-sqlite3` with a single local SQLite database.
- Use functional React components.
- Use Server Components by default.
- Use `"use client"` only when necessary.
- Use Server Actions or Route Handlers for data mutations.
- Store dates and timestamps in UTC ISO-8601 format.
- Use kebab-case for files and snake_case for database tables/columns.
- Every stock-changing database operation must use a transaction.
- The application must support Thai and English.

---

## 2. Forbidden Actions

The agent must not:

- Add cloud hosting or an external database.
- Replace SQLite or `better-sqlite3`.
- Add third-party authentication.
- Add email, SMS, or push notifications.
- Allow stock quantity to become negative.
- Delete or overwrite audit history.
- Change requirements in `prd.md` without approval.
- Add database tables or fields without updating `schema.md`.
- Ignore user permission checks.

---

## 3. Stock and Sales Rules

- Stock quantity must never be negative.
- Stock updates must record the previous and new quantity.
- Adding stock increases the current quantity.
- Recording a sale automatically decreases stock.
- Sale and stock deduction must be handled in one database transaction.
- Important stock and sales changes must be recorded in the audit log.
- Audit logs must record the user, action, previous value, new value, and time.

---

## 4. Roles and Permissions

- There are two main access levels: Admin and Operator.
- Admin represents the CEO/Manager and can access all system information.
- Operators can only access functions related to their assigned responsibility.
- Permission checks must be performed on the server.

---

## 5. Definition of Done

A task is complete when:

1. The application builds successfully.
2. Relevant functionality has been tested.
3. The implementation matches `prd.md`.
4. The feature works through the actual UI.
5. Database changes are reflected in `schema.md`.
6. `progress.md` is updated.
7. No new errors are introduced.

---

## 6. Session Rules

At the start of a session:

- Read `progress.md`.
- Check the current milestone in `implementation-plan.md`.
- Review relevant requirements in `prd.md`.

At the end of a session:

- Update `progress.md`.
- Record completed work and remaining work.
- Leave the repository in a working state.

=======================================================================================================================================================================



# architecture.md — Stock and Sales Management Application

Companion to `prd.md` and `agents.md`. This file describes how the system is structured.

architecture.md — How the system is built

Architecture explains how the internal parts of the system are structured and how they communicate with each other.

The overall flow is:

User → Browser → Next.js → Business Logic → SQLite

For example, when making a sale:

Salesperson → Enter Sale → Next.js validates data → Save Sale → Deduct Stock → Save Audit Log

It also explains:

What technologies are used.
Where different files and folders are located.
How login works.
How stock updates work.
How Sales connects with Stock.
How the Dashboard gets its data.
How permissions work.
How QR codes work.
How Thai/English language switching works.

In short: architecture.md = "How is the system built and how do its parts work together?"

---

1. High-Level Overview

The application is a single local web application using Next.js and a local SQLite database.

Browser (UI)
      |
      | Server Actions / Route Handlers
      v
Next.js Server
      |
      | Business Logic / Authentication / Permissions
      v
better-sqlite3
      |
      v
Local SQLite Database


2. Technology
Layer and Technology
Framework--------Next.js 14 App Router
Language---------TypeScript
Styling	Tailwind- CSS
Database---------SQLite + better-sqlite3
Authentication---Local username/password + bcrypt
Validation-------Zod
QR Code----------qrcode
Testing----------Vitest + Testing Library


3. Folder Structure

├── agents.md
├── architecture.md
├── schema.md
├── implementation-plan.md
├── progress.md
├── prd.md
├── data/
│   └── stock.db
├── db/
│   ├── client.ts
│   ├── migrations/
│   └── queries/
├── lib/
│   ├── auth.ts
│   ├── permissions.ts
│   ├── qr.ts
│   └── validation/
├── app/
│   ├── layout.tsx
│   ├── login/
│   ├── dashboard/
│   ├── skus/
│   ├── sales/
│   ├── customers/
│   └── admin/
├── components/
│   ├── ui/
│   ├── sku/
│   ├── sales/
│   └── dashboard/
└── tests/
    ├── unit/
    └── components/


4. Main Data Flow

Login
1. User enters username and password.
2. Server verifies the credentials.
3. Server creates a secure session.
4. Protected routes check authentication and permissions.

Stock Update
1. Operator selects or scans a SKU.
2. Operator enters the current quantity.
3. Server validates the quantity.
4. Database transaction updates the SKU.
5. A stock event and audit log are created.

Add Stock
1. Operator selects a SKU.
2. Operator enters the quantity received.
3. Server calculates the new quantity.
4. Database transaction updates the SKU.
5. Stock event and audit log are created.

Record Sale
1. Salesperson creates a sale.
2. Customer and salesperson information are selected.
3. One or more products are added.
4. Server calculates the totals.
5. System checks available stock.
6. Sale and sale items are saved.
7. Stock is automatically deducted.
8. Stock events and audit logs are created.

Sale recording and stock deduction must happen in the same database transaction.


5. Dashboard Data Flow

The dashboard retrieves information from the database and displays it as summaries and graphs.

The CEO/Manager can view:

- Total sales
- Sales trends
- Salesperson performance
- Low-stock products
- Stale-stock products
- Current inventory

The dashboard supports time-period filters such as:

- Today
- Last 7 days
- This month
- Last month
- Custom date range

Operators only see dashboard information allowed by their assigned responsibility.


6. Authentication & Permissions

The system has two main access levels:

Admin: CEO/Manager with access to all system information and management functions.

Operator: Staff member with access based on their assigned responsibility.

Permissions are checked on the server.

Examples of Operator responsibilities include:

- Salesperson
- Stock staff

Users must not access information or actions outside their assigned permissions.


7. Database Rules

SQLite is the only database.

Database writes must use transactions when multiple related records are changed.

Stock quantity must never become negative.

Sales and stock deduction must be atomic.

Important changes must be recorded in the audit log.

Database structure must match `schema.md`.


8. QR Code

Each SKU has a unique QR code.

The QR code identifies the SKU and allows staff to quickly open the SKU page for stock-related operations.

QR codes are generated by the server using the `qrcode` package.


9. Language Support

The application supports Thai and English.

Users can switch the application language using a language-switching control.


10. Architecture Questions

Should the QR code contain the SKU ID or a local URL?

Will the application be accessed only from the main machine or from other devices on the same local network?
