# Invoice & Inventory Management System
### A full-stack purchase tracking platform for managing parties, products, rates, and invoices — built for small businesses and back-office teams.

---

## Overview

Managing purchase invoices manually across multiple suppliers and product lines is error-prone and time-consuming. This project solves that by providing a clean, browser-based interface backed by a secure REST API to create, track, and audit purchase history in real time. It was built to demonstrate end-to-end full-stack development skills — from database schema design through RESTful API development to a dynamic, responsive frontend. What makes it stand out is its combination of JWT-secured endpoints, soft-delete data integrity, server-side pagination with multi-column sorting and search, and a dynamic rate system that always uses the most recent price for a product.

---

## 🚀 Live Demo

🚀 Demo coming soon

---

## Tech Stack

| Category    | Technology                                      |
|-------------|-------------------------------------------------|
| Frontend    | HTML5, CSS3, Vanilla JavaScript, jQuery         |
| UI Framework| Bootstrap 4, Bootstrap Lumen theme              |
| Data Tables | DataTables 1.13, Select2                        |
| Backend     | ASP.NET Core 6 (Web API)                        |
| ORM         | Entity Framework Core 6 (Code-First + SQL scaffold) |
| Database    | Microsoft SQL Server 2022                       |
| Auth        | JWT Bearer Tokens (`Microsoft.AspNetCore.Authentication.JwtBearer`) |
| Mapping     | AutoMapper 12                                   |
| API Docs    | Swagger / Swashbuckle                           |
| Build Tool  | .NET 6 SDK, MSBuild                             |

---

## Key Features

- 🔐 **JWT Authentication** — Secure user registration and login; all API endpoints are protected with Bearer token authorization
- 🏭 **Party (Manufacturer) Management** — Full CRUD for supplier records with duplicate-name prevention and soft-delete support
- 📦 **Product Catalog** — Create and manage products with unique-name enforcement; soft-deleted records are automatically restored on re-add
- 🔗 **Product–Party Assignment** — Many-to-many mapping between manufacturers and products, tracked independently from each entity
- 💲 **Dynamic Rate History** — Time-stamped pricing per product; the API always resolves the latest rate when building invoices
- 🧾 **Invoice / Purchase History** — Multi-line invoices grouped by Invoice ID with automatic grand-total computation (`rate × quantity`)
- 🔍 **Advanced Invoice Search** — Server-side pagination, multi-column sorting (Invoice ID, Party, Date, Total), substring search, date-range filter, and multi-product filter using Select2
- 📋 **Inline Invoice Editing** — Add, edit, or delete individual line items within an existing invoice directly from the purchase history table

---

## Architecture Overview

The system follows a classic **3-tier architecture**:

1. **Presentation Tier** — Static HTML pages served from the filesystem, each accompanied by a dedicated JavaScript module that communicates with the API via jQuery AJAX. Bootstrap 4 provides the responsive layout. JWT tokens are stored in `localStorage` and attached as `Authorization: Bearer` headers on every request.

2. **Application Tier** — An ASP.NET Core 6 Web API organized around RESTful controllers (`/api/manufacturers`, `/api/products`, `/api/mappings`, `/api/rates`, `/api/invoices`, `/api/login`, `/api/register`). AutoMapper converts between EF Core models and lightweight DTOs to keep the API contract decoupled from the database schema. A custom `RestrictNegativeValue` validation attribute enforces business rules at the model layer.

3. **Data Tier** — A SQL Server database accessed through Entity Framework Core. The schema is version-controlled via a `script.sql` export. Soft-delete (`isDeleted` flag) is used across all primary entities so that historical invoice data is never orphaned.

---

## Getting Started

### Prerequisites

- [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)
- [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) (2019 or later recommended)
- A modern browser (Chrome, Edge, Firefox)
- Optional: [Visual Studio 2022](https://visualstudio.microsoft.com/) or VS Code

---

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yashvi-khunt/invoice-inventory-system.git
   cd invoice-inventory-system
   ```

2. **Set up the database**
   - Open SQL Server Management Studio (SSMS) or `sqlcmd`
   - Execute `script.sql` to create the `Evaluation` database and all tables:
     ```bash
     sqlcmd -S . -E -i script.sql
     ```

3. **Configure the backend connection string**

   Open `Backend/EvaluationProject/EvaluationProject/appsettings.json` and update the connection string to match your SQL Server instance:
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Data Source=<YOUR_SERVER>; Database=Evaluation; Integrated Security=SSPI;"
     }
   }
   ```

4. **Restore NuGet packages and build**
   ```bash
   cd Backend/EvaluationProject
   dotnet restore
   dotnet build
   ```

5. **Run the API**
   ```bash
   cd EvaluationProject
   dotnet run
   ```
   The API will start on `https://localhost:7146` by default. Swagger UI is available at `https://localhost:7146/swagger`.

6. **Open the frontend**
   - Navigate to the `Frontend/EvalFrontend/` folder
   - Open `Login.html` directly in your browser, **or** serve it with a static file server:
     ```bash
     # Example using the VS Code Live Server extension, or:
     npx serve Frontend/EvalFrontend
     ```

---

### Environment Variables / Configuration

| Key | Location | Description |
|-----|----------|-------------|
| `ConnectionStrings:DefaultConnection` | `appsettings.json` | ADO.NET connection string to your SQL Server `Evaluation` database |
| JWT Signing Key | `Program.cs` / `LoginController.cs` | Symmetric key used to sign and validate JWT tokens — move to `appsettings.json` or a secrets manager before deploying to production. Built as a demonstration of full-stack .NET architecture. |
| `baseURL` | `Frontend/EvalFrontend/js/*.js` | Base URL of the running API (default: `https://localhost:7146/api`) — update if the API is hosted elsewhere |

---

### How to Run Locally (Summary)

```
1. Start SQL Server
2. Run script.sql to initialize the database
3. Update appsettings.json with your connection string
4. dotnet run  (from Backend/EvaluationProject/EvaluationProject/)
5. Open Frontend/EvalFrontend/Login.html in a browser
6. Register a new account, then log in
```

---

## Project Structure

```
invoice-inventory-system/
├── script.sql                          # Full SQL Server database creation script
│
├── Backend/
│   └── EvaluationProject/
│       ├── EvaluationProject.sln       # Visual Studio solution file
│       └── EvaluationProject/
│           ├── Program.cs              # App entry point: DI, JWT, CORS, middleware setup
│           ├── appsettings.json        # Connection string and logging config
│           ├── EvaluationProject.csproj# NuGet dependencies (EF Core, JWT, AutoMapper, Swagger)
│           ├── Controllers/
│           │   ├── LoginController.cs       # POST /api/login, POST /api/register
│           │   ├── ManufacturerController.cs# CRUD /api/manufacturers
│           │   ├── MappingController.cs     # CRUD /api/mappings (product–party links)
│           │   ├── ProductController.cs     # CRUD /api/products
│           │   ├── PurchaseHistoryController.cs # CRUD + search /api/invoices
│           │   └── RateController.cs        # CRUD /api/rates
│           ├── Models/
│           │   ├── EvaluationContext.cs     # EF Core DbContext with Fluent API config
│           │   ├── Manufacturer.cs          # Party/supplier entity
│           │   ├── Product.cs               # Product entity
│           │   ├── ManufacturerProductMapping.cs # Join table entity
│           │   ├── Rate.cs                  # Dated price record per product
│           │   ├── PurchaseHistory.cs       # Invoice line item entity
│           │   ├── PurchaseHistoryDetail.cs # Supporting detail model
│           │   ├── User.cs                  # Auth user entity
│           │   ├── Role.cs / UserRole.cs    # Role scaffolding models
│           ├── DTOs/
│           │   ├── *DTO.cs                  # Read/response DTOs
│           │   └── *CreationDTO.cs          # Write/request DTOs
│           ├── Helpers/
│           │   └── MappingProfile.cs        # AutoMapper profile (models ↔ DTOs)
│           └── Validations/
│               └── RestrictNegativeValue.cs # Custom data annotation validator
│
└── Frontend/
    └── EvalFrontend/
        ├── Login.html / Signup.html         # Authentication pages
        ├── Index.html                        # Party (manufacturer) list
        ├── AddParty.html                     # Add new party
        ├── Product.html / AddProduct.html    # Product management
        ├── AssignPage.html / AddAssign.html  # Product–party assignment
        ├── Rate.html / AddRate.html          # Rate management
        ├── PurchaseHistory.html              # Invoice list with search/filter
        ├── AddPurchaseHistory.html           # Create new invoice
        ├── EditPurchaseHistory.html          # Edit existing invoice
        ├── ViewPurchaseHistory.html          # Read-only invoice detail view
        ├── css/
        │   ├── Site.css                      # Custom styles
        │   └── bootstrap-lumen.css           # Lumen Bootstrap theme
        └── js/
            ├── common.js      # Shared: auth guard, nav highlight, sign-out
            ├── login.js       # Login & registration AJAX logic
            ├── party.js       # Manufacturer CRUD table logic
            ├── product.js     # Product CRUD table logic
            ├── assign.js      # Mapping CRUD table logic
            ├── rate.js        # Rate CRUD table logic
            ├── invoice.js     # Invoice list: pagination, sort, search, edit modal
            ├── view.js        # Invoice detail view
            ├── edit.js        # Invoice edit page logic
            └── script.js      # Add-invoice form logic
```

---

## 📸 Screenshots

📸 Screenshots coming soon

---

## What I Learned

- 🛠️ **Designing a layered REST API from scratch** — I practiced separating concerns cleanly across Controllers, DTOs, Models, and AutoMapper profiles, which taught me how to keep API contracts stable even when the underlying database schema changes.
- 🔐 **Implementing stateless JWT authentication** — Wiring up `JwtBearer` middleware in ASP.NET Core and consuming the token in a vanilla-JS frontend deepened my understanding of how token-based auth flows work end-to-end without a framework like React or Angular doing the heavy lifting.
- 📊 **Building server-side pagination and dynamic filtering** — Writing composable `IQueryable` chains for pagination, multi-column sorting, substring search, date ranges, and multi-value product filters in a single endpoint was a practical exercise in LINQ composition and query optimization.
- 🔄 **Managing soft deletes with referential integrity** — Using an `isDeleted` flag instead of hard deletes across related entities (products, mappings, rates, invoices) required careful query filtering throughout the codebase and reinforced the importance of data integrity in transactional systems.

---

## Author

**Yashvi Khunt**  
MS Computer Science (Cybersecurity) — Stevens Institute of Technology  
🔗 [github.com/yashvi-khunt](https://github.com/yashvi-khunt)  
💼 [linkedin.com/in/yashvi-khunt](https://linkedin.com/in/yashvi-khunt)
