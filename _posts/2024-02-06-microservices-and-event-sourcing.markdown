---
layout: post
title:  "Microservices and Event Sourcing"
date:   2024-02-06 08:40:00 +0700
categories: programming
permalink: /microservices-and-event-sourcing/
---

**Event Sourcing** is a design pattern where state changes in a system are stored as a sequence of immutable events rather than updating the current state directly. The current state is reconstructed by replaying these events.

## **Key Concepts**
- **Event:** A record of something that happened in the system (e.g., `OrderCreated`, `OrderShipped`).  
- **Event Store:** A database or storage system where events are persisted.  
- **Aggregate Root:** The main entity that ensures the consistency of changes by applying events.  
- **Command:** An action that changes the state (e.g., `CreateOrderCommand`).  
- **Event Handler:** Listens for and reacts to events.

## **Benefits of Event Sourcing**

1. **Auditability:** Full history of state changes is stored.  
2. **Debugging:** Replay events to reproduce bugs.  
3. **Scalability:** Easy to distribute events to other systems.  
4. **Flexibility:** Rebuild state projections for different views.  
5. **Temporal Queries:** Query the state at any point in time.

## **Disadvantages of Event Sourcing**

1. **Complexity:** More components (event store, replay logic).  
2. **Event Versioning:** Evolving event schemas is challenging.  
3. **Data Growth:** Event storage grows indefinitely.  
4. **Read Model Lag:** CQRS projections may lag behind writes.

## **Detailed C# Implementation**

**Scenario**: An e-commerce system where users can create orders.

#### **1. Domain Event**

```csharp
public interface IDomainEvent
{
    DateTime OccurredOn { get; }
}

public class OrderCreatedEvent : IDomainEvent
{
    public Guid OrderId { get; }
    public string ProductName { get; }
    public decimal Price { get; }

    public DateTime OccurredOn { get; } = DateTime.UtcNow;

    public OrderCreatedEvent(Guid orderId, string productName, decimal price)
    {
        OrderId = orderId;
        ProductName = productName;
        Price = price;
    }
}
```

#### **2. Aggregate Root**

```csharp
public abstract class AggregateRoot
{
    private readonly List<IDomainEvent> _domainEvents = new();

    public IReadOnlyCollection<IDomainEvent> DomainEvents => _domainEvents.AsReadOnly();

    protected void AddDomainEvent(IDomainEvent domainEvent)
    {
        _domainEvents.Add(domainEvent);
    }

    public void ClearEvents() => _domainEvents.Clear();
}

public class Order : AggregateRoot
{
    public Guid Id { get; private set; }
    public string ProductName { get; private set; }
    public decimal Price { get; private set; }

    private Order() { }

    public Order(Guid id, string productName, decimal price)
    {
        Id = id;
        ProductName = productName;
        Price = price;

        AddDomainEvent(new OrderCreatedEvent(id, productName, price));
    }
}
```

#### **3. Event Store**

```csharp
public interface IEventStore
{
    Task SaveEventsAsync(Guid aggregateId, IEnumerable<IDomainEvent> events);
    Task<IEnumerable<IDomainEvent>> GetEventsAsync(Guid aggregateId);
}

public class InMemoryEventStore : IEventStore
{
    private readonly Dictionary<Guid, List<IDomainEvent>> _store = new();

    public async Task SaveEventsAsync(Guid aggregateId, IEnumerable<IDomainEvent> events)
    {
        if (!_store.ContainsKey(aggregateId))
            _store[aggregateId] = new List<IDomainEvent>();

        _store[aggregateId].AddRange(events);
        await Task.CompletedTask;
    }

    public async Task<IEnumerable<IDomainEvent>> GetEventsAsync(Guid aggregateId)
    {
        _store.TryGetValue(aggregateId, out var events);
        return await Task.FromResult(events ?? new List<IDomainEvent>());
    }
}
```

#### **4. Command and Handler (CQRS)**

```csharp
public class CreateOrderCommand : IRequest<Guid>
{
    public string ProductName { get; }
    public decimal Price { get; }

    public CreateOrderCommand(string productName, decimal price)
    {
        ProductName = productName;
        Price = price;
    }
}

public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, Guid>
{
    private readonly IEventStore _eventStore;

    public CreateOrderCommandHandler(IEventStore eventStore)
    {
        _eventStore = eventStore;
    }

    public async Task<Guid> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        var order = new Order(Guid.NewGuid(), request.ProductName, request.Price);
        await _eventStore.SaveEventsAsync(order.Id, order.DomainEvents);
        order.ClearEvents();
        return order.Id;
    }
}
```

#### **5. Controller**

```csharp
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
        return Ok(new { OrderId = orderId });
    }
}
```

## **Flow Explanation**

1. **Client** sends a POST request to create an order.  
2. **Controller** sends a `CreateOrderCommand` via **MediatR**.  
3. **Command Handler** creates the `Order` aggregate and generates an `OrderCreatedEvent`.  
4. The **Event Store** persists the event.  
5. The client receives the created `OrderId` as feedback.

## **Advantages in this Implementation**

- **Auditability:** Every order creation is logged as an event.  
- **Flexibility:** Can rebuild order state anytime by replaying events.  
- **Integration Ready:** Can easily publish events to other microservices.

## **Disadvantages in this Implementation**

- **Event Versioning:** Changing the `OrderCreatedEvent` structure requires careful handling.  
- **Storage Overhead:** Events keep growing indefinitely.  
- **Complexity:** More layers and components to manage.