---
layout: post
title:  "Hexagonal, Onion, and Clean Architectures"
date:   2024-02-10 08:40:00 +0700
categories: programming
permalink: /hexagonal-onion-and-clean-architectures/
---

## 1. **Hexagonal Architecture (Ports and Adapters)**

#### **Concept**  
- Proposed by **Alistair Cockburn**.  
- Focuses on `decoupling the core business logic` (domain) from external systems (UI, databases, message queues) using **Ports** and **Adapters**.

#### **Structure**  
- **Core (Domain Model):** Contains business logic.  
- **Ports:** Interfaces that define input/output operations.  
- **Adapters:** Implement ports to interact with external systems.  

#### **Key Characteristics**  
- **Inbound Adapters:** Handle user inputs (HTTP controllers, CLI, etc.).  
- **Outbound Adapters:** Handle outputs (DB, MQ, APIs).  
- **Easily testable:** Adapters can be replaced without affecting core logic.  

#### **Use Case**  
- Applications that need flexibility with multiple I/O interfaces (web, CLI, MQ).

## 2. **Onion Architecture**

#### **Concept**  
- Introduced by **Jeffrey Palermo**.  
- Emphasizes `dependency inversion`, where the core business logic is independent of external concerns.

#### **Structure**  
- **Core (Domain Layer):** Entities and business rules.  
- **Domain Services:** Business logic that doesn't fit in entities.  
- **Application Layer:** Use cases and service interfaces.  
- **Infrastructure Layer:** Implements interfaces (DB, external APIs).  
- **Presentation Layer:** UI or API controllers.  

#### **Key Characteristics**  
- **Dependencies point inward** (from outer layers to inner layers).  
- **Strong focus on Domain logic.**  
- **Highly testable** due to dependency inversion.  

#### **Use Case**  
- Domain-Driven Design (DDD) projects with complex business rules.

## 3. **Clean Architecture**

#### **Concept**  
- Created by **Robert C. Martin (Uncle Bob)**.  
- Combines principles from Hexagonal and Onion architectures.  
- Prioritizes business rules and enforces clear separation between layers.

#### **Structure**  
- **Entities (Core):** Enterprise-wide business rules.  
- **Use Cases (Application):** Specific business rules.  
- **Interface Adapters:** Converts data between use cases and external interfaces.  
- **Frameworks & Drivers:** External systems (DB, UI, MQ).  

#### **Key Characteristics**  
- **Dependency Rule:** Inner layers must not depend on outer layers.  
- **Testable and maintainable.**  
- **Frameworks are easily replaceable.**  

#### **Use Case**  
- Enterprise-level applications requiring scalability and maintainability.

## **Comparison Table**

| **Feature**               | **Hexagonal Architecture**           | **Onion Architecture**             | **Clean Architecture**           |
|---------------------------|-------------------------------------|-----------------------------------|---------------------------------|
| **Focus**                 | Decoupling I/O via Ports & Adapters | Dependency Inversion & Domain Core | Clear Separation & Business Rules |
| **Layers**                | Core, Ports, Adapters               | Domain, Application, Infrastructure | Entities, Use Cases, Adapters, Frameworks |
| **Dependency Direction**  | Toward Core                        | Toward Domain Core                 | Toward Business Rules           |
| **Testability**           | High                               | High                               | High                           |
| **External Systems**      | Managed via Adapters               | Managed in Infrastructure Layer    | Managed in Frameworks Layer     |
| **Best for**              | Systems with multiple I/O interfaces | DDD-based complex business logic  | Large, scalable applications    |

## **Which Architecture to Choose?**

- **Hexagonal Architecture:** Best when the application interacts with various I/O systems (REST API, CLI, MQ).  
- **Onion Architecture:** Suitable for applications with complex domains requiring pure business logic.  
- **Clean Architecture:** Ideal when scalability, testability, and maintainability are top priorities.

> In practice, these architectures are not mutually exclusive. Many systems blend concepts from all three to fit specific requirements.