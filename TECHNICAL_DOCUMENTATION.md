# Shopizer Admin - Technical Documentation

## 📋 Table of Contents
1. [Overview](#overview)
2. [Technology Stack](#technology-stack)
3. [Application Architecture](#application-architecture)
4. [Module Structure](#module-structure)
5. [Core Features](#core-features)
6. [Authentication & Security](#authentication--security)
7. [API Integration](#api-integration)
8. [Deployment](#deployment)

---

## Overview

**Shopizer Admin** is an Angular-based e-commerce administration panel for managing online stores. It provides comprehensive tools for managing products, orders, customers, content, shipping, payments, and store configurations.

### Key Characteristics
- **Framework**: Angular 11.2.14
- **UI Library**: Nebular 6.2.0 + Bootstrap 4.3.1
- **Node Version**: v12.22.7
- **Build Tool**: Angular CLI 11.2.17
- **Mode Support**: STANDARD, MARKETPLACE, B2B

---

## Technology Stack

### Frontend Framework
```
┌─────────────────────────────────────────┐
│         Angular 11.2.14                 │
│  ┌───────────────────────────────────┐  │
│  │   Nebular Theme Framework         │  │
│  │   (UI Components & Layout)        │  │
│  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────┐  │
│  │   Bootstrap 4.3.1                 │  │
│  │   (Grid & Utilities)              │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

### Core Dependencies

| Category | Library | Version | Purpose |
|----------|---------|---------|---------|
| **UI Framework** | @nebular/theme | 6.2.0 | Admin UI components |
| **UI Components** | @ng-bootstrap/ng-bootstrap | 6.1.0 | Bootstrap components |
| **Icons** | eva-icons | 1.1.3 | Icon library |
| **Forms** | @angular/forms | 11.2.14 | Reactive forms |
| **HTTP** | @angular/common/http | 11.2.14 | API communication |
| **Routing** | @angular/router | 11.2.14 | Navigation |
| **i18n** | @ngx-translate/core | 12.1.2 | Internationalization |
| **Charts** | @swimlane/ngx-charts | 13.0.2 | Data visualization |
| **Tables** | ng2-smart-table | 1.5.0 | Data tables |
| **File Upload** | ngx-awesome-uploader | 10.0.4 | File management |
| **Rich Text** | ngx-summernote | 0.7.7 | WYSIWYG editor |
| **Notifications** | ngx-toastr | 12.1.0 | Toast messages |
| **Date** | date-fns | 2.25.0 | Date manipulation |

---

## Application Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Browser (Client)                         │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │              Shopizer Admin (Angular App)                 │ │
│  │                                                           │ │
│  │  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │ │
│  │  │   @theme    │  │   @core      │  │    pages       │  │ │
│  │  │  (Layout &  │  │  (Services & │  │  (Features)    │  │ │
│  │  │ Components) │  │   Guards)    │  │                │  │ │
│  │  └─────────────┘  └──────────────┘  └────────────────┘  │ │
│  │                                                           │ │
│  │  ┌───────────────────────────────────────────────────┐   │ │
│  │  │         HTTP Interceptors                         │   │ │
│  │  │  • AuthInterceptor (JWT Token)                    │   │ │
│  │  │  • GlobalHttpInterceptor (Error Handling)         │   │ │
│  │  └───────────────────────────────────────────────────┘   │ │
│  └───────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              ↕ HTTP/REST
┌─────────────────────────────────────────────────────────────────┐
│                    Backend API Services                         │
│                                                                 │
│  ┌──────────────────────┐      ┌──────────────────────┐        │
│  │  Shopizer API        │      │  Shipping API        │        │
│  │  localhost:8080/api  │      │  localhost:9090/...  │        │
│  └──────────────────────┘      └──────────────────────┘        │
└─────────────────────────────────────────────────────────────────┘
```

### Folder Structure

```
shopizer-admin/
├── src/
│   ├── app/
│   │   ├── @core/              # Core services, guards, utilities
│   │   │   ├── data/           # Data models & interfaces
│   │   │   ├── mock/           # Mock data services
│   │   │   └── utils/          # Utility services
│   │   │
│   │   ├── @theme/             # UI theme & layout
│   │   │   ├── components/     # Shared UI components
│   │   │   │   ├── header/
│   │   │   │   ├── footer/
│   │   │   │   ├── image-browser/
│   │   │   │   └── tiny-mce/
│   │   │   ├── layouts/        # Page layouts
│   │   │   └── styles/         # Global styles
│   │   │
│   │   ├── pages/              # Feature modules
│   │   │   ├── auth/           # Authentication
│   │   │   ├── home/           # Dashboard
│   │   │   ├── catalogue/      # Product management
│   │   │   ├── orders/         # Order management
│   │   │   ├── customers/      # Customer management
│   │   │   ├── content/        # CMS
│   │   │   ├── shipping/       # Shipping config
│   │   │   ├── payment/        # Payment methods
│   │   │   ├── tax-management/ # Tax configuration
│   │   │   ├── store-management/ # Store settings
│   │   │   ├── user-management/  # User admin
│   │   │   └── shared/         # Shared resources
│   │   │       ├── components/
│   │   │       ├── services/
│   │   │       ├── guards/
│   │   │       ├── interceptors/
│   │   │       ├── models/
│   │   │       └── validation/
│   │   │
│   │   ├── app.module.ts
│   │   └── app-routing.module.ts
│   │
│   ├── assets/
│   │   ├── i18n/              # Translation files
│   │   ├── img/               # Images
│   │   └── env.js             # Runtime config
│   │
│   └── environments/          # Environment configs
│
├── docker/                    # Docker configurations
├── conf/                      # Nginx configs
└── package.json
```

---

## Module Structure

### Core Modules (@core)

```
@core/
├── core.module.ts          # Core module with providers
├── data/                   # Abstract data interfaces
│   ├── users.ts
│   ├── orders-chart.ts
│   └── ...
├── mock/                   # Mock implementations
│   ├── users.service.ts
│   └── ...
└── utils/
    ├── layout.service.ts   # Layout state management
    └── state.service.ts    # Application state
```

**Purpose**: Singleton services, guards, and utilities used across the application.

### Theme Module (@theme)

```
@theme/
├── theme.module.ts
├── components/
│   ├── header/             # Top navigation bar
│   ├── footer/             # Footer component
│   ├── image-browser/      # Image gallery browser
│   ├── tiny-mce/           # Rich text editor
│   ├── error/              # Error page
│   └── search-input/       # Global search
├── layouts/
│   ├── one-column/
│   ├── two-columns/
│   └── three-columns/
└── styles/
    ├── themes.scss
    └── ...
```

**Purpose**: Provides consistent UI/UX, layout components, and theming.

### Pages Module (Feature Modules)

Each feature module follows a similar structure:

```
feature-module/
├── feature.module.ts
├── feature-routing.module.ts
├── feature.component.ts
├── services/               # Feature-specific services
├── models/                 # Data models
└── [sub-features]/         # Sub-feature components
```

---

## Core Features

### 1. Authentication Module

```
┌─────────────────────────────────────────┐
│        Authentication Flow              │
├─────────────────────────────────────────┤
│                                         │
│  Login → TokenService → LocalStorage    │
│    ↓                                    │
│  AuthInterceptor adds JWT to requests   │
│    ↓                                    │
│  AuthGuard protects routes              │
│    ↓                                    │
│  Logout → Clear token → Redirect        │
│                                         │
└─────────────────────────────────────────┘
```

**Components**:
- Login
- Register
- Forgot Password
- Reset Password

**Services**:
- `AuthService`: Authentication operations
- `TokenService`: JWT token management
- `SecurityService`: Role-based access control

**Guards**:
- `AuthGuard`: Route protection

### 2. User Management

```
User Management
├── My Profile
├── Create User (Admin only)
└── User List (Admin only)
```

**Features**:
- User CRUD operations
- Profile management
- Password change
- Role assignment

### 3. Store Management

```
Store Management
├── Store Details
│   ├── Basic Information
│   ├── Branding
│   └── Landing Page
├── Stores List (Admin)
└── Create Store (Superadmin/Admin Retail)
```

**Modes**:
- **STANDARD**: Single/Multi-store
- **MARKETPLACE**: Multi-vendor marketplace
- **B2B**: Business-to-business

### 4. Catalogue Management

```
Catalogue Management
├── Categories
│   ├── Categories List
│   ├── Create Category
│   └── Categories Hierarchy
├── Products
│   ├── Products List
│   ├── Create Product
│   ├── Product Details
│   ├── Inventory Management
│   ├── Product Images
│   ├── Product Attributes
│   ├── Product Properties
│   ├── Pricing
│   └── Product Ordering
├── Options
│   ├── Options List
│   ├── Option Values
│   ├── Option Sets
│   └── Variations
├── Brands
│   ├── Brands List
│   └── Create Brand
├── Product Groups
└── Product Types
```

**Key Features**:
- Hierarchical category management
- Product variants and options
- Inventory tracking
- Multi-image support
- Bulk operations
- Product ordering/sorting

### 5. Order Management

```
Order Management
├── Orders List
├── Order Details
│   ├── Customer Information
│   ├── Order Items
│   ├── Shipping Details
│   ├── Payment Information
│   └── Order Status
├── Order History
├── Order Invoice
└── Order Transactions
```

**Capabilities**:
- Order tracking
- Status management
- Invoice generation
- Transaction history

### 6. Customer Management

```
Customer Management
├── Customer List
├── Customer Details
└── Set Credentials
```

### 7. Content Management (CMS)

```
Content Management
├── Pages
│   ├── Create/Edit Pages
│   └── Page List
├── Content Boxes
│   ├── Create/Edit Boxes
│   └── Box List
└── Images
    ├── Upload Images
    └── Image Gallery
```

**Features**:
- WYSIWYG editor (Summernote)
- Image management
- Content boxes for homepage
- SEO-friendly pages

### 8. Shipping Management

```
Shipping Management
├── Expedition Configuration
├── Shipping Methods
│   ├── Configure Methods
│   └── Method List
├── Origin Address
└── Packaging
    ├── Package Types
    └── Package Dimensions
```

### 9. Payment Management

```
Payment Management
└── Payment Methods
    ├── Configure Payment Gateways
    └── Method List
```

### 10. Tax Management

```
Tax Management
├── Tax Classes
│   ├── Create Tax Class
│   └── Tax Class List
└── Tax Rates
    ├── Create Tax Rate
    └── Tax Rate List
```

---

## Authentication & Security

### Security Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Security Layers                       │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  1. Route Guards (AuthGuard)                            │
│     ↓                                                    │
│  2. Role-Based Access Control (RBAC)                    │
│     ↓                                                    │
│  3. JWT Token Authentication                            │
│     ↓                                                    │
│  4. HTTP Interceptors                                   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### User Roles

| Role | Code | Permissions |
|------|------|-------------|
| **Superadmin** | `isSuperadmin` | Full system access |
| **Admin** | `isAdmin` | Store administration |
| **Admin Retail** | `isAdminRetail` | Retail operations |
| **Admin Catalogue** | `isAdminCatalogue` | Product management |
| **Admin Store** | `isAdminStore` | Store settings |
| **Admin Order** | `isAdminOrder` | Order management |
| **Admin Content** | `isAdminContent` | Content management |
| **Customer** | `isCustomer` | Customer portal |

### Authentication Flow

```
┌─────────┐
│  Login  │
└────┬────┘
     │
     ↓
┌─────────────────────┐
│  AuthService.login  │
│  POST /auth/login   │
└────┬────────────────┘
     │
     ↓
┌──────────────────────┐
│  Receive JWT Token   │
│  + User Info         │
└────┬─────────────────┘
     │
     ↓
┌──────────────────────┐
│  TokenService.save   │
│  localStorage.set    │
└────┬─────────────────┘
     │
     ↓
┌──────────────────────┐
│  Navigate to /pages  │
└──────────────────────┘
```

### HTTP Interceptors

#### 1. AuthInterceptor
```typescript
// Adds JWT token to all HTTP requests
intercept(request, next) {
  const token = tokenService.getToken();
  if (token) {
    request = request.clone({
      headers: request.headers.set('Authorization', 'Bearer ' + token)
    });
  }
  return next.handle(request);
}
```

#### 2. GlobalHttpInterceptor
```typescript
// Handles global HTTP errors
intercept(request, next) {
  return next.handle(request).pipe(
    catchError((error: HttpErrorResponse) => {
      // Handle 401, 403, 500, etc.
      // Show toast notifications
      // Redirect to login if unauthorized
    })
  );
}
```

---

## API Integration

### API Configuration

```typescript
// environment.ts
export const environment = {
  production: false,
  mode: 'STANDARD', // MARKETPLACE | B2B | STANDARD
  apiUrl: 'http://localhost:8080/api',
  shippingApi: 'http://localhost:9090/shipping/api/v1',
  client: {
    language: {
      default: 'en',
      array: ['fr', 'en']
    }
  }
};
```

### Service Architecture

```
┌─────────────────────────────────────────┐
│         Service Layer Pattern           │
├─────────────────────────────────────────┤
│                                         │
│  CrudService (Base)                     │
│       ↑                                 │
│       │ extends                         │
│       │                                 │
│  ┌────┴────────────────────────┐       │
│  │                             │       │
│  ProductService  CategoryService       │
│  OrderService    CustomerService       │
│  ...                                    │
│                                         │
└─────────────────────────────────────────┘
```

### Key Services

| Service | Purpose | Endpoints |
|---------|---------|-----------|
| **ProductService** | Product CRUD | `/products/*` |
| **CategoryService** | Category management | `/categories/*` |
| **OrderService** | Order operations | `/orders/*` |
| **CustomerService** | Customer management | `/customers/*` |
| **StoreService** | Store configuration | `/stores/*` |
| **UserService** | User management | `/users/*` |
| **ConfigService** | App configuration | `/config/*` |

### API Request Flow

```
Component
    ↓
Service Method
    ↓
HTTP Client
    ↓
AuthInterceptor (add JWT)
    ↓
Backend API
    ↓
Response
    ↓
GlobalHttpInterceptor (error handling)
    ↓
Service (transform data)
    ↓
Component (update UI)
```

---

## Deployment

### Development

```bash
# Install dependencies
npm install --legacy-peer-deps

# Start dev server
ng serve -o

# Access at
http://localhost:4200
```

### Production Build

```bash
# Build for production
ng build --prod

# Output directory
dist/shopizer-admin/
```

### Docker Deployment

```bash
# Build Docker image
docker build -t shopizer-admin .

# Run container
docker run \
  -e "APP_BASE_URL=http://localhost:9090/api" \
  -it --rm -p 4200:80 \
  shopizerecomm/shopizer-admin
```

### Environment Configuration

Runtime configuration via `assets/env.js`:

```javascript
(function (window) {
  window.__env = window.__env || {};
  window.__env.apiUrl = 'http://localhost:8080/api';
  window.__env.shippingApi = 'http://localhost:9090/shipping/api/v1';
}(this));
```

---

## Internationalization (i18n)

### Translation System

```
assets/i18n/
├── en.json    # English
└── fr.json    # French
```

### Usage

```typescript
// In component
constructor(private translate: TranslateService) {
  translate.setDefaultLang('en');
  translate.use('en');
}

// In template
{{ 'COMPONENTS.HOME' | translate }}
```

### Menu Translation

All menu items use translation keys:
```typescript
{
  title: 'COMPONENTS.USER_MANAGEMENT',
  key: 'COMPONENTS.USER_MANAGEMENT',
  icon: 'person',
  children: [...]
}
```

---

## Default Credentials

```
Username: admin@shopizer.com
Password: password
```

---

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

---

## Performance Optimizations

1. **Lazy Loading**: Feature modules loaded on demand
2. **AOT Compilation**: Ahead-of-time compilation in production
3. **Tree Shaking**: Unused code elimination
4. **Code Splitting**: Separate bundles per module
5. **Image Optimization**: Lazy loading images
6. **Caching**: HTTP caching strategies

---

## Testing

```bash
# Unit tests
npm run test

# E2E tests
npm run e2e

# Test coverage
npm run test:coverage
```

---

## License

MIT License - See LICENSE file for details

---

**Last Updated**: March 2026
**Version**: Angular 11.2.14
**Maintained By**: Shopizer Team
