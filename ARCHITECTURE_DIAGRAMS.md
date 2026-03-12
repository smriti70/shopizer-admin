# Shopizer Admin - Architecture Diagrams & Component Interactions

## Table of Contents
1. [Application Flow Diagrams](#application-flow-diagrams)
2. [Module Dependency Graph](#module-dependency-graph)
3. [Component Interaction Patterns](#component-interaction-patterns)
4. [Data Flow Architecture](#data-flow-architecture)
5. [State Management](#state-management)

---

## Application Flow Diagrams

### 1. Application Bootstrap Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Application Startup                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  main.ts                                                        │
│  • platformBrowserDynamic()                                     │
│  • bootstrapModule(AppModule)                                   │
└──────────────────────────────┬──────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  AppModule                                                      │
│  ├── BrowserModule                                              │
│  ├── BrowserAnimationsModule                                    │
│  ├── HttpClientModule                                           │
│  ├── ThemeModule.forRoot()                                      │
│  ├── CoreModule.forRoot()                                       │
│  ├── TranslateModule.forRoot()                                  │
│  ├── ToastrModule.forRoot()                                     │
│  └── AppRoutingModule                                           │
└──────────────────────────────┬──────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  HTTP Interceptors Registration                                │
│  ├── AuthInterceptor (JWT injection)                            │
│  └── GlobalHttpInterceptor (Error handling)                     │
└──────────────────────────────┬──────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  AppComponent                                                   │
│  • Initialize theme                                             │
│  • Load translations                                            │
│  • Check authentication                                         │
└──────────────────────────────┬──────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  Router Navigation                                              │
│  • If authenticated → /pages (PagesModule)                      │
│  • If not authenticated → /auth (AuthModule)                    │
└─────────────────────────────────────────────────────────────────┘
```

### 2. User Authentication Flow

```
┌──────────────┐
│  User Opens  │
│  Application │
└──────┬───────┘
       │
       ↓
┌──────────────────────┐
│  Check Token in      │
│  LocalStorage        │
└──────┬───────────────┘
       │
       ├─── Token Exists ───┐
       │                    │
       │                    ↓
       │            ┌───────────────────┐
       │            │  AuthGuard        │
       │            │  canActivate()    │
       │            │  returns true     │
       │            └────────┬──────────┘
       │                     │
       │                     ↓
       │            ┌───────────────────┐
       │            │  Navigate to      │
       │            │  /pages/home      │
       │            └───────────────────┘
       │
       └─── No Token ───┐
                        │
                        ↓
               ┌────────────────────┐
               │  Redirect to       │
               │  /auth/login       │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  User enters       │
               │  credentials       │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  AuthService       │
               │  .login()          │
               │  POST /auth/login  │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  Backend validates │
               │  Returns JWT +     │
               │  User data         │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  TokenService      │
               │  .saveToken()      │
               │  .saveUser()       │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  Parse roles       │
               │  Store in          │
               │  localStorage      │
               └────────┬───────────┘
                        │
                        ↓
               ┌────────────────────┐
               │  Navigate to       │
               │  /pages/home       │
               └────────────────────┘
```

### 3. Product Management Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Product Management Workflow                  │
└─────────────────────────────────────────────────────────────────┘

┌──────────────┐
│ Products     │
│ List Page    │
└──────┬───────┘
       │
       ├──── View ────┐
       │              │
       │              ↓
       │     ┌────────────────────┐
       │     │ Product Details    │
       │     │ • Basic Info       │
       │     │ • Descriptions     │
       │     │ • Categories       │
       │     │ • Attributes       │
       │     │ • Properties       │
       │     │ • Images           │
       │     │ • Pricing          │
       │     │ • Inventory        │
       │     └────────────────────┘
       │
       ├──── Create ────┐
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Product Creation   │
       │       │ Wizard             │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Step 1: Basic Info │
       │       │ • Name             │
       │       │ • SKU              │
       │       │ • Type             │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Step 2: Details    │
       │       │ • Description      │
       │       │ • Categories       │
       │       │ • Brand            │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Step 3: Images     │
       │       │ • Upload images    │
       │       │ • Set default      │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Step 4: Pricing    │
       │       │ • Base price       │
       │       │ • Discounts        │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ Step 5: Inventory  │
       │       │ • Quantity         │
       │       │ • Variants         │
       │       └────────┬───────────┘
       │                │
       │                ↓
       │       ┌────────────────────┐
       │       │ ProductService     │
       │       │ .createProduct()   │
       │       │ POST /products     │
       │       └────────────────────┘
       │
       └──── Edit ────┐
                      │
                      ↓
             ┌────────────────────┐
             │ Load existing      │
             │ product data       │
             │ Update fields      │
             │ PUT /products/:id  │
             └────────────────────┘
```

---

## Module Dependency Graph

```
┌─────────────────────────────────────────────────────────────────┐
│                        AppModule                                │
│                     (Root Module)                               │
└────────────┬────────────────────────────────────────────────────┘
             │
             ├──────────────────────────────────────────┐
             │                                          │
             ↓                                          ↓
┌────────────────────────┐              ┌──────────────────────────┐
│    CoreModule          │              │    ThemeModule           │
│    (Singleton)         │              │    (Shared UI)           │
│                        │              │                          │
│  • Services            │              │  • Components            │
│  • Guards              │              │    - Header              │
│  • Interceptors        │              │    - Footer              │
│  • Utils               │              │    - Image Browser       │
│                        │              │  • Layouts               │
└────────────────────────┘              │  • Pipes                 │
                                        │  • Directives            │
                                        └──────────────────────────┘
             │
             ↓
┌─────────────────────────────────────────────────────────────────┐
│                      PagesModule                                │
│                   (Lazy Loaded)                                 │
└────────────┬────────────────────────────────────────────────────┘
             │
             ├──────────┬──────────┬──────────┬──────────┬─────────┐
             │          │          │          │          │         │
             ↓          ↓          ↓          ↓          ↓         ↓
┌──────────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ AuthModule   │ │HomeModule│ │Catalogue │ │ Orders   │ │Customers │
│              │ │          │ │ Module   │ │ Module   │ │ Module   │
│ • Login      │ │Dashboard │ │          │ │          │ │          │
│ • Register   │ │          │ │Products  │ │Order List│ │Customer  │
│ • Reset Pwd  │ │          │ │Categories│ │Details   │ │List      │
└──────────────┘ └──────────┘ │Options   │ │Invoice   │ └──────────┘
                               │Brands    │ └──────────┘
                               └──────────┘
             │          │          │          │          │
             ↓          ↓          ↓          ↓          ↓
┌──────────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Content      │ │Shipping  │ │ Payment  │ │   Tax    │ │  Store   │
│ Module       │ │ Module   │ │ Module   │ │Management│ │Management│
│              │ │          │ │          │ │          │ │          │
│ • Pages      │ │Methods   │ │Methods   │ │Tax Class │ │Store     │
│ • Boxes      │ │Packages  │ │Config    │ │Tax Rate  │ │Details   │
│ • Images     │ │Origin    │ └──────────┘ └──────────┘ │Branding  │
└──────────────┘ └──────────┘                            └──────────┘
             │
             ↓
┌─────────────────────────────────────────────────────────────────┐
│                      SharedModule                               │
│                   (Shared Resources)                            │
│                                                                 │
│  • Components (Reusable)                                        │
│  • Services (Feature-specific)                                  │
│  • Models (Data structures)                                     │
│  • Validators                                                   │
│  • Pipes                                                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component Interaction Patterns

### 1. Smart Component / Dumb Component Pattern

```
┌─────────────────────────────────────────────────────────────────┐
│                    Smart Component                              │
│                  (Container Component)                          │
│                                                                 │
│  • Manages state                                                │
│  • Calls services                                               │
│  • Handles business logic                                       │
│  • Subscribes to observables                                    │
│                                                                 │
│  Example: ProductsListComponent                                 │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  ngOnInit() {                                            │  │
│  │    this.productService.getProducts()                     │  │
│  │      .subscribe(products => {                            │  │
│  │        this.products = products;                         │  │
│  │      });                                                 │  │
│  │  }                                                       │  │
│  │                                                          │  │
│  │  onDelete(id) {                                          │  │
│  │    this.productService.delete(id)                        │  │
│  │      .subscribe(() => this.loadProducts());              │  │
│  │  }                                                       │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ @Input() products
                             │ @Output() delete
                             │
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                    Dumb Component                               │
│                (Presentational Component)                       │
│                                                                 │
│  • Receives data via @Input()                                   │
│  • Emits events via @Output()                                   │
│  • No service dependencies                                      │
│  • Pure presentation logic                                      │
│                                                                 │
│  Example: ProductCardComponent                                  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  @Input() product: Product;                              │  │
│  │  @Output() delete = new EventEmitter<number>();          │  │
│  │                                                          │  │
│  │  onDeleteClick() {                                       │  │
│  │    this.delete.emit(this.product.id);                    │  │
│  │  }                                                       │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### 2. Service-Component Interaction

```
┌─────────────────────────────────────────────────────────────────┐
│                        Component                                │
│                                                                 │
│  constructor(private productService: ProductService) {}         │
│                                                                 │
│  loadProducts() {                                               │
│    this.loading = true;                                         │
│    this.productService.getProducts(this.storeId)                │
│      .pipe(                                                     │
│        finalize(() => this.loading = false)                     │
│      )                                                          │
│      .subscribe(                                                │
│        data => this.products = data,                            │
│        error => this.handleError(error)                         │
│      );                                                         │
│  }                                                              │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ Method call
                             │
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                        Service                                  │
│                                                                 │
│  getProducts(storeId: string): Observable<Product[]> {          │
│    const url = `${this.apiUrl}/products`;                       │
│    const params = { store: storeId };                           │
│    return this.http.get<Product[]>(url, { params })             │
│      .pipe(                                                     │
│        map(response => this.transformData(response)),           │
│        catchError(this.handleError)                             │
│      );                                                         │
│  }                                                              │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ HTTP Request
                             │
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                    HTTP Interceptor                             │
│                                                                 │
│  intercept(req, next) {                                         │
│    // Add JWT token                                             │
│    const authReq = req.clone({                                  │
│      headers: req.headers.set('Authorization', `Bearer ${token}`)│
│    });                                                          │
│    return next.handle(authReq);                                 │
│  }                                                              │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ HTTP Request with token
                             │
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                      Backend API                                │
│                                                                 │
│  GET /api/products?store=STORE001                               │
│  Authorization: Bearer eyJhbGc...                               │
└─────────────────────────────────────────────────────────────────┘
```

---

## Data Flow Architecture

### 1. CRUD Operations Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    CREATE Operation                             │
└─────────────────────────────────────────────────────────────────┘

Component Form
    │
    │ User fills form
    │ Clicks "Save"
    │
    ↓
FormGroup.value
    │
    │ Validate
    │
    ↓
Service.create(data)
    │
    │ POST /api/resource
    │
    ↓
Backend API
    │
    │ Validate & Save
    │
    ↓
Response (201 Created)
    │
    │ Return created entity
    │
    ↓
Service transforms response
    │
    │ Observable<Entity>
    │
    ↓
Component.subscribe()
    │
    │ Show success message
    │ Navigate to list
    │
    ↓
Updated UI

┌─────────────────────────────────────────────────────────────────┐
│                    READ Operation                               │
└─────────────────────────────────────────────────────────────────┘

Component.ngOnInit()
    │
    │ Load data
    │
    ↓
Service.getAll()
    │
    │ GET /api/resource
    │
    ↓
Backend API
    │
    │ Query database
    │
    ↓
Response (200 OK)
    │
    │ Return entity list
    │
    ↓
Service transforms response
    │
    │ Observable<Entity[]>
    │
    ↓
Component.subscribe()
    │
    │ Assign to component property
    │
    ↓
Template renders data

┌─────────────────────────────────────────────────────────────────┐
│                    UPDATE Operation                             │
└─────────────────────────────────────────────────────────────────┘

Component loads entity
    │
    │ Populate form
    │
    ↓
User modifies form
    │
    │ Clicks "Update"
    │
    ↓
Service.update(id, data)
    │
    │ PUT /api/resource/:id
    │
    ↓
Backend API
    │
    │ Validate & Update
    │
    ↓
Response (200 OK)
    │
    │ Return updated entity
    │
    ↓
Component.subscribe()
    │
    │ Show success message
    │ Refresh data
    │
    ↓
Updated UI

┌─────────────────────────────────────────────────────────────────┐
│                    DELETE Operation                             │
└─────────────────────────────────────────────────────────────────┘

Component
    │
    │ User clicks "Delete"
    │
    ↓
Confirmation Dialog
    │
    │ User confirms
    │
    ↓
Service.delete(id)
    │
    │ DELETE /api/resource/:id
    │
    ↓
Backend API
    │
    │ Delete from database
    │
    ↓
Response (204 No Content)
    │
    ↓
Component.subscribe()
    │
    │ Show success message
    │ Remove from list
    │
    ↓
Updated UI
```

### 2. Form Handling Pattern

```
┌─────────────────────────────────────────────────────────────────┐
│                  Reactive Forms Pattern                         │
└─────────────────────────────────────────────────────────────────┘

Component Class
│
├── FormGroup Definition
│   │
│   ├── FormBuilder.group({
│   │     name: ['', [Validators.required, Validators.minLength(3)]],
│   │     email: ['', [Validators.required, Validators.email]],
│   │     price: [0, [Validators.required, Validators.min(0)]],
│   │     category: [null, Validators.required]
│   │   })
│   │
│   └── Custom Validators
│       • equalValidator
│       • uniqueValidator
│       • asyncValidator
│
├── Form Submission
│   │
│   ├── onSubmit() {
│   │     if (this.form.valid) {
│   │       const data = this.form.value;
│   │       this.service.save(data).subscribe(...);
│   │     }
│   │   }
│   │
│   └── Error Handling
│       • Display validation errors
│       • Show API errors
│
└── Template Binding
    │
    ├── <form [formGroup]="form" (ngSubmit)="onSubmit()">
    │     <input formControlName="name">
    │     <div *ngIf="form.get('name').errors">
    │       <span *ngIf="form.get('name').errors.required">
    │         Name is required
    │       </span>
    │     </div>
    │   </form>
    │
    └── Real-time Validation
        • valueChanges observable
        • statusChanges observable
```

---

## State Management

### LocalStorage State

```
┌─────────────────────────────────────────────────────────────────┐
│                  LocalStorage Structure                         │
└─────────────────────────────────────────────────────────────────┘

localStorage
├── token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
├── user: {
│     id: 123,
│     username: "admin@shopizer.com",
│     firstName: "Admin",
│     lastName: "User",
│     store: "STORE001"
│   }
├── roles: {
│     isSuperadmin: false,
│     isAdmin: true,
│     isAdminRetail: false,
│     isAdminCatalogue: true,
│     isAdminStore: false,
│     isAdminOrder: true,
│     isAdminContent: false,
│     isCustomer: false,
│     canAccessToOrder: true
│   }
├── currentStore: "STORE001"
├── language: "en"
└── theme: "default"
```

### Service State Management

```
┌─────────────────────────────────────────────────────────────────┐
│              BehaviorSubject Pattern                            │
└─────────────────────────────────────────────────────────────────┘

@Injectable()
export class StateService {
  
  private currentStoreSubject = new BehaviorSubject<Store>(null);
  public currentStore$ = this.currentStoreSubject.asObservable();
  
  private productsSubject = new BehaviorSubject<Product[]>([]);
  public products$ = this.productsSubject.asObservable();
  
  setCurrentStore(store: Store) {
    this.currentStoreSubject.next(store);
    localStorage.setItem('currentStore', store.code);
  }
  
  getCurrentStore(): Store {
    return this.currentStoreSubject.value;
  }
  
  updateProducts(products: Product[]) {
    this.productsSubject.next(products);
  }
}

// Component usage
constructor(private stateService: StateService) {
  this.stateService.currentStore$.subscribe(store => {
    this.store = store;
    this.loadProducts();
  });
}
```

---

## Error Handling Strategy

```
┌─────────────────────────────────────────────────────────────────┐
│                  Error Handling Flow                            │
└─────────────────────────────────────────────────────────────────┘

HTTP Error
    │
    ↓
GlobalHttpInterceptor
    │
    ├── 401 Unauthorized
    │   │
    │   ├── Clear token
    │   ├── Redirect to login
    │   └── Show "Session expired" message
    │
    ├── 403 Forbidden
    │   │
    │   ├── Show "Access denied" message
    │   └── Redirect to home
    │
    ├── 404 Not Found
    │   │
    │   └── Show "Resource not found" message
    │
    ├── 500 Server Error
    │   │
    │   └── Show "Server error" message
    │
    └── Network Error
        │
        └── Show "Connection error" message
    │
    ↓
ToastrService
    │
    └── Display notification
        • Error toast (red)
        • Warning toast (yellow)
        • Info toast (blue)
        • Success toast (green)
```

---

## Performance Optimization Strategies

### 1. Lazy Loading Modules

```
AppRoutingModule
│
├── /auth → AuthModule (Lazy)
├── /pages → PagesModule (Lazy)
│   │
│   ├── /catalogue → CatalogueModule (Lazy)
│   ├── /orders → OrdersModule (Lazy)
│   ├── /customers → CustomersModule (Lazy)
│   └── ...
│
└── Benefits:
    • Smaller initial bundle
    • Faster initial load
    • Load on demand
```

### 2. Change Detection Strategy

```typescript
@Component({
  selector: 'app-product-card',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `...`
})
export class ProductCardComponent {
  @Input() product: Product;
  
  // Only re-renders when:
  // 1. Input reference changes
  // 2. Event triggered
  // 3. Manual markForCheck()
}
```

### 3. TrackBy Functions

```typescript
<div *ngFor="let product of products; trackBy: trackByProductId">
  {{ product.name }}
</div>

trackByProductId(index: number, product: Product): number {
  return product.id;
}

// Benefits:
// • Prevents unnecessary DOM re-renders
// • Improves list performance
// • Maintains component state
```

---

**Document Version**: 1.0  
**Last Updated**: March 2026  
**Maintained By**: Shopizer Team
