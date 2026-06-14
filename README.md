# .NET Architectural Patterns Synthesis

A comprehensive guide to architectural patterns for .NET applications, ranging from simple monolithic structures to complex distributed systems. This repository serves as a reference for selecting the right architecture based on project scale, team size, and business requirements.

## 1. Core Architectural Patterns

* **Clean Architecture (Onion Architecture):** The "gold standard" for enterprise applications. It relies on the Dependency Rule, where source code dependencies point only inwards, keeping the Domain independent of infrastructure.
![Clean_Architecture](Clean_Architecture.png)
 
```text
src/
├── Core/                       # The innermost circle (Domain logic)
│   ├── Domain/                 # Enterprise Business Rules (Entities, Value Objects, Enums)
│   │   ├── Entities/
│   │   ├── ValueObjects/
│   │   └── Interfaces/         # Repository/Service interfaces that Domain needs
│   └── Application/            # Application Business Rules
│       ├── UseCases/           # Implementation of business processes
│       ├── DTOs/               # Data Transfer Objects
│       └── Interfaces/         # Interfaces for external dependencies (e.g., IEmailService)
├── Infrastructure/             # Implementation of Interfaces defined in Core
│   ├── Persistence/            # Database contexts, migrations, repositories
│   ├── Services/               # Third-party API implementations, Email, Logging
│   └── External/               # Cache providers, message queues
└── Presentation/               # The outermost circle (UI)
    ├── API/                    # Controllers, Middlewares, Filters
    ├── Client/                 # Frontend (if monolithic)
    └── Configuration/          # DI Registrations, App settings
```
 
  
* **Vertical Slice Architecture:** Organizes code by features (e.g., "AddProduct") rather than technical concerns. It offers high cohesion and reduces friction by keeping all feature-specific logic localized.
![Vertical_Slice_Architecture](Vertical_Slice_Architecture.png)

```text
src/
├── Features/
│   ├── Orders/
│   │   ├── CreateOrder/
│   │   │   ├── CreateOrderEndpoint.cs    # The API definition (Controller/Minimal API)
│   │   │   ├── CreateOrderCommand.cs     # The Request model
│   │   │   ├── CreateOrderHandler.cs     # The Business Logic
│   │   │   ├── CreateOrderValidator.cs   # Validation logic
│   │   │   └── OrderCreatedEvent.cs      # Domain event (if needed)
│   │   ├── GetOrderById/
│   │   │   ├── GetOrderEndpoint.cs
│   │   │   ├── GetOrderQuery.cs
│   │   │   └── GetOrderHandler.cs
│   ├── Users/
│   │   ├── RegisterUser/
│   │   └── LoginUser/
├── Shared/                               # Cross-cutting concerns (common across slices)
│   ├── Database/
│   ├── Infrastructure/
│   └── Logging/
└── Program.cs                            # Entry point / DI Registration
```

* **Minimal APIs:** The modern approach for lightweight, scalable APIs. It removes MVC boilerplate and pairs perfectly with Vertical Slice architecture.
![Minimal_APIs_Architecture](Minimal_APIs_Architecture.png)
```text 
src/
├── Endpoints/                  # Groups of endpoints (feature-based or resource-based)
│   ├── UserEndpoints.cs        # Maps routes and handlers for Users
│   ├── OrderEndpoints.cs       # Maps routes and handlers for Orders
│   └── IEndpointDefinition.cs  # Interface to standardize endpoint registration
├── Models/                     # DTOs, Requests, and Responses
├── Services/                   # Business logic / Orchestration
├── Data/                       # Database Context and Repositories
├── Program.cs                  # Entry point, DI container, and middleware
└── appsettings.json
```
* **Hexagonal Architecture (Ports and Adapters):** Similar to Clean Architecture, this focuses on isolating the application core from external frameworks and tools using interfaces (Ports) and implementations (Adapters).
![Hexagonal Architecture](Hexagonal_Architecture.png)
```text 
MySolution.sln
├── src/
│   ├── MyProject.Core/             # Pure Business Logic (Class Library)
│   │   ├── Domain/                 # Entities, Domain Events
│   │   │   └── IUserRepository.cs  # Driven Port (Interface)
│   │   └── Application/            # Use Cases, DTOs
│   │       └── UserService.cs      # Driving Port (Orchestrates logic)
│   │
│   ├── MyProject.Infrastructure/   # Implementation of Driven Ports
│   │   ├── Persistence/
│   │   │   └── SqlUserRepository.cs # Implements IUserRepository
│   │   └── ExternalServices/
│   │
│   └── MyProject.Api/              # Driving Adapter (Web API)
│       ├── Controllers/
│       │   └── UserController.cs   # Calls UserService
│       └── Program.cs              # Dependency Injection Container
│
└── tests/
    ├── MyProject.Core.Tests/       # Unit tests for Domain/Application
    └── MyProject.Infrastructure.Tests/ # Integration tests for DB/External APIs
```

* **CQRS:** Separates read and write operations. Excellent for high-performance needs and complex audit trails, allowing for specialized storage technologies per operation type.
![CQRS](CQRS_Architecture.png)
```text 
MyProject.Api/
├── Features/
│   ├── Orders/
│   │   ├── CreateOrder/
│   │   │   ├── CreateOrderCommand.cs         # Request object
│   │   │   └── CreateOrderCommandHandler.cs  # Business Logic
│   │   ├── GetOrderById/
│   │   │   ├── GetOrderByIdQuery.cs          # Request object
│   │   │   └── GetOrderByIdQueryHandler.cs   # Read logic (Dapper/EF)
│   │   └── OrdersController.cs               # Minimal API or Controller
│   │
│   └── Users/
│       └── ...
├── Infrastructure/                           # EF Core, Dapper, Email clients
├── Domain/                                   # Entities, Value Objects
└── Program.cs                                # MediatR and DI Registration
```
  
* **Microservices:** Ideal for large-scale systems requiring independent deployment. Leverages ASP.NET Core with Docker/Kubernetes, often using Dapr, gRPC, and MassTransit.
![Micro-Services-Architecture](Micro-Services-Architecture.png)
---

## 2. Recommendation Matrix

| Project Scale | Recommended Pattern |
| :--- | :--- |
| **Simple / Small** | Monolithic Layered Architecture |
| **Enterprise / Complex** | Clean Architecture |
| **Feature-Rich / Team** | Vertical Slice Architecture |
| **Massive / Distributed** | Microservices with CQRS |

---

## 3. Scaling Enterprise Applications

Modern enterprise development often combines **Domain-Driven Design (DDD)** Bounded Contexts with **Clean Architecture**.

### The Combined Approach
By layering Clean Architecture *inside* each DDD Bounded Context, you create a robust, modular structure:
* **Bounded Context Layer:** Defines the high-level domain boundary.
* **Clean Architecture Inner Layers:** Each module contains its own:
    * **Domain:** Entities and business rules.
    * **Application:** Use cases, commands, and queries.
    * **Infrastructure:** Database contexts and repository implementations.
```text
Solution.Root/
├── src/
│   ├── BoundedContexts/
│   │   ├── Billing/
│   │   │   ├── Billing.Domain/
│   │   │   ├── Billing.Application/
│   │   │   └── Billing.Infrastructure/
│   │   └── Inventory/
│   │       ├── Inventory.Domain/
│   │       ├── Inventory.Application/
│   │       └── Inventory.Infrastructure/
│   └── SharedKernel/              # Common Domain logic shared across contexts
├── Solution.API/                  # Entry point coordinating across contexts
└── tests/
```

---

## 4. Architectural Selection Matrix

| Project Scenario / Constraints | Primary Challenge | Recommended Architecture | Key Trade-off / Consequence |
| :--- | :--- | :--- | :--- |
| **Small-scale CRUD app** | Over-engineering, boilerplate | **Minimal APIs / Monolith** | Fast development, but risks technical debt. |
| **Enterprise / Core Domain** | Business logic coupling | **Clean Architecture** | High testability, but higher initial ceremony. |
| **Feature-rich / Team-based** | High maintenance/merging | **Vertical Slice Architecture** | Extreme feature autonomy, but risks minor duplication. |
| **Massive / Distributed** | Deployment bottlenecks | **Microservices with CQRS** | Independent scaling, but high operational complexity. |


