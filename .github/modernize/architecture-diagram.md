# Architecture Diagram

eShopOnWeb is a multi-tier e-commerce application built with Clean Architecture principles, featuring an ASP.NET Core MVC storefront, a Blazor WebAssembly admin portal, and a RESTful API layer, all backed by SQL Server via Entity Framework Core.

## Application Architecture

```mermaid
graph TB
    subgraph Clients["Clients"]
        Browser["Web Browser"]
        AdminUser["Admin User"]
        APIConsumer["API Consumer"]
    end

    subgraph WebApp["Web Application - ASP.NET Core MVC"]
        MVC["MVC Controllers and Razor Views"]
        BlazorServer["Blazor Server Host"]
        HealthChecks["Health Checks"]
        MemoryCache["In-Memory Cache"]
        CookieAuth["Cookie Authentication"]
        MediatR["MediatR Pipeline"]
    end

    subgraph BlazorAdminApp["Blazor Admin - WebAssembly"]
        BlazorPages["Admin Pages and Components"]
        BlazorAuthState["Custom Auth State Provider"]
        LocalStorage["Blazored LocalStorage"]
        HttpService["HTTP Service Client"]
    end

    subgraph PublicAPIApp["Public API - Minimal APIs"]
        Endpoints["API Endpoints - Ardalis ApiEndpoints"]
        Swagger["Swagger and OpenAPI"]
        JWTAuth["JWT Bearer Authentication"]
        CORS["CORS Policy"]
        ExceptionMiddleware["Exception Middleware"]
        AutoMapper["AutoMapper"]
    end

    subgraph CoreLayer["Application Core - Domain Layer"]
        Entities["Domain Entities and Aggregates: Basket, Order, Catalog"]
        Services["Domain Services: BasketService, OrderService"]
        Interfaces["Repository and Service Interfaces"]
        Specifications["Ardalis Specification Pattern"]
        GuardClauses["Ardalis Guard Clauses"]
    end

    subgraph SharedLayer["Blazor Shared"]
        DTOs["Shared DTOs and Models"]
        Validation["FluentValidation Rules"]
    end

    subgraph InfraLayer["Infrastructure - Data Access Layer"]
        EfRepo["EF Core Generic Repository"]
        CatalogCtx["CatalogContext - DbContext"]
        IdentityCtx["AppIdentityDbContext"]
        TokenService["Token Claims Service - JWT"]
        EfConfig["Entity Configurations and Migrations"]
    end

    subgraph DataStores["Data Storage"]
        CatalogDB[("SQL Server - CatalogDb: Products, Orders, Baskets")]
        IdentityDB[("SQL Server - IdentityDb: Users, Roles, Claims")]
    end

    subgraph AzureServices["Azure Services"]
        KeyVault["Azure Key Vault"]
        AzureIdentity["Azure Identity"]
    end

    Browser -->|"HTTP Requests"| MVC
    Browser -->|"SignalR WebSocket"| BlazorServer
    AdminUser -->|"WASM Download"| BlazorAdminApp
    APIConsumer -->|"REST API Calls"| Endpoints

    BlazorServer --> BlazorAdminApp
    MVC --> MediatR
    MVC --> CookieAuth
    MVC --> MemoryCache
    MediatR --> CoreLayer

    HttpService -->|"REST with JWT Token"| Endpoints
    BlazorPages --> HttpService
    BlazorPages --> BlazorAuthState
    BlazorAuthState --> LocalStorage

    Endpoints --> JWTAuth
    Endpoints --> AutoMapper
    Endpoints --> Swagger
    Endpoints --> ExceptionMiddleware
    Endpoints --> CORS
    Endpoints --> CoreLayer

    Services --> Interfaces
    Services --> Specifications
    Services --> GuardClauses
    Entities --> Specifications

    CoreLayer --> SharedLayer

    Interfaces -->|"Implemented by"| EfRepo
    EfRepo --> CatalogCtx
    EfRepo --> EfConfig
    TokenService --> IdentityCtx

    CatalogCtx -->|"Entity Framework Core"| CatalogDB
    IdentityCtx -->|"ASP.NET Identity"| IdentityDB

    WebApp -->|"Configuration Secrets"| KeyVault
    WebApp --> AzureIdentity
```
