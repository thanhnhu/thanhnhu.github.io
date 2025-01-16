---
layout: post
title:  "Microservices and CQRS"
date:   2024-01-28 08:40:00 +0700
categories: programming
permalink: /microservices-and-cqrs/
---

**Microservices Architecture** is a design approach where an application is composed of small, loosely coupled, and independently deployable services. Each service is focused on a specific business capability and can be developed, deployed, and scaled independently.

#### Key Characteristics of Microservices:
1. **Single Responsibility:** Each service handles a specific business function (e.g., Order Service, Payment Service).  
2. **Decentralized Data Management:** Each service manages its own database, ensuring data isolation.  
3. **Independent Deployment:** Services can be deployed and updated independently.  
4. **Scalability:** Individual services can be scaled based on demand.  
5. **Technology Diversity:** Different services can use different technologies and programming languages.

## CQRS (Command Query Responsibility Segregation)

**CQRS** separates read (query) and write (command) operations into different models.  
- **Command**: Handles operations that change the state (Create, Update, Delete).  
- **Query**: Handles data retrieval without modifying the state.

#### Why Use CQRS?
1. **Separation of Concerns:** Simplifies complex business logic by separating reads and writes.  
2. **Performance Optimization:** Queries and commands can be optimized differently (e.g., caching for reads).  
3. **Scalability:** Commands and queries can be scaled independently.  
4. **Flexibility:** Easier to apply Event Sourcing, logging, and auditing.

## Why Microservices + CQRS Is Better Than Monolithic Architecture

| **Aspect**            | **Monolithic Architecture**                    | **Microservices + CQRS**                              |
|----------------------|-------------------------------------------------|-------------------------------------------------------|
| **Deployment**       | All components are deployed together.          | Services are deployed independently.                   |
| **Scalability**      | Entire application must scale.                 | Individual services scale as needed.                   |
| **Maintainability**  | Complex and tightly coupled code.              | Easier to maintain with modular services.              |
| **Data Management**  | Single database for the entire app.            | Decentralized databases per service.                   |
| **Performance**      | Read/write operations share the same model.   | Reads and writes are optimized separately.             |
| **Technology Stack** | Limited to one stack.                         | Services can use different technologies.               |

#### Disadvantages:
- Complexity: CQRS can introduce complexity, especially when managing the two separate models and their synchronization.
- Increased Development Time: The overhead of maintaining two separate models, handling synchronization, and dealing with data consistency across the system can increase development time.
- Eventual Consistency: With separate command and query models, achieving eventual consistency may require additional mechanisms, such as events, which can lead to latency.
- Overhead for Simple Systems: For simple applications, implementing CQRS may be overkill as it adds unnecessary complexity without providing significant benefits.

## Detailed C# Implementation (Microservices + CQRS)

Let's implement a basic **Order Service** using **CQRS** in a microservices setup.

#### 1. Command: Create Order

```csharp
// Commands/CreateOrderCommand.cs
using MediatR;

public record CreateOrderCommand(string ProductName, int Quantity, decimal Price) : IRequest<Guid>;
```

#### 2. Command Handler: Handle Order Creation

```csharp
// Handlers/CreateOrderCommandHandler.cs
using MediatR;

public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, Guid>
{
    private readonly OrderDbContext _context;

    public CreateOrderCommandHandler(OrderDbContext context)
    {
        _context = context;
    }

    public async Task<Guid> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        var order = new Order
        {
            Id = Guid.NewGuid(),
            ProductName = request.ProductName,
            Quantity = request.Quantity,
            Price = request.Price,
            CreatedAt = DateTime.UtcNow
        };

        _context.Orders.Add(order);
        await _context.SaveChangesAsync(cancellationToken);

        return order.Id;
    }
}
```

#### 3. Query: Get Order By ID

```csharp
// Queries/GetOrderByIdQuery.cs
using MediatR;

public record GetOrderByIdQuery(Guid OrderId) : IRequest<Order>;
```

#### 4. Query Handler: Fetch Order

```csharp
// Handlers/GetOrderByIdQueryHandler.cs
using MediatR;
using Microsoft.EntityFrameworkCore;

public class GetOrderByIdQueryHandler : IRequestHandler<GetOrderByIdQuery, Order>
{
    private readonly OrderDbContext _context;

    public GetOrderByIdQueryHandler(OrderDbContext context)
    {
        _context = context;
    }

    public async Task<Order> Handle(GetOrderByIdQuery request, CancellationToken cancellationToken)
    {
        return await _context.Orders.FirstOrDefaultAsync(o => o.Id == request.OrderId, cancellationToken);
    }
}
```

#### 5. API Controller

```csharp
// Controllers/OrdersController.cs
using MediatR;
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;

    public OrdersController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    public async Task<IActionResult> CreateOrder([FromBody] CreateOrderCommand command)
    {
        var orderId = await _mediator.Send(command);
        return Ok(orderId);
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetOrder(Guid id)
    {
        var order = await _mediator.Send(new GetOrderByIdQuery(id));
        return order == null ? NotFound() : Ok(order);
    }
}
```