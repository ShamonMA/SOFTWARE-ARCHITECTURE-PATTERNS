# .NET Architectural Patterns Synthesis

A comprehensive guide to architectural patterns for .NET applications, ranging from simple monolithic structures to complex distributed systems. This repository serves as a reference for selecting the right architecture based on project scale, team size, and business requirements.

## 1. Core Architectural Patterns

* **Clean Architecture (Onion Architecture):** The "gold standard" for enterprise applications. It relies on the Dependency Rule, where source code dependencies point only inwards, keeping the Domain independent of infrastructure.

 
```text
Solution.Root/
├── src/
│   ├── Solution.Domain/          # Core Domain: Entities, Enums, Exceptions, Interfaces
│   ├── Solution.Application/     # Business Logic: DTOs, Mappers, CQRS Handlers, Behaviors
│   ├── Solution.Infrastructure/  # External Concerns: Persistence, Identity, External Services
│   └── Solution.API/             # Presentation Layer: Controllers, Middlewares, Program.cs
└── tests/
    ├── Solution.UnitTests/       # Isolated unit tests for core logic
    └── Solution.IntegrationTests/# Integration tests for infrastructure and API endpoints
```
 
  
* **Vertical Slice Architecture:** Organizes code by features (e.g., "AddProduct") rather than technical concerns. It offers high cohesion and reduces friction by keeping all feature-specific logic localized.

```text
Solution.Root/
├── src/
│   ├── Solution.API/
│   │   ├── Features/
│   │   │   ├── Orders/
│   │   │   │   ├── CreateOrder/
│   │   │   │   │   ├── CreateOrderEndpoint.cs
│   │   │   │   │   ├── CreateOrderCommand.cs
│   │   │   │   │   ├── CreateOrderValidator.cs
│   │   │   │   │   └── CreateOrderHandler.cs
│   │   │   │   └── GetOrderById/
│   │   │   └── Products/
│   │   ├── Common/                # Shared behaviors/base classes
│   │   └── Program.cs
└── tests/
```

* **Microservices:** Ideal for large-scale systems requiring independent deployment. Leverages ASP.NET Core with Docker/Kubernetes, often using Dapr, gRPC, and MassTransit.
* **CQRS:** Separates read and write operations. Excellent for high-performance needs and complex audit trails, allowing for specialized storage technologies per operation type.
* **Minimal APIs:** The modern approach for lightweight, scalable APIs. It removes MVC boilerplate and pairs perfectly with Vertical Slice architecture.

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

---

### Understanding the Architecture
To better understand how these systems are structured, review these visual representations:
