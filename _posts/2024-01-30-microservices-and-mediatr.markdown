---
layout: post
title:  "Microservices and MediatR"
date:   2024-01-30 08:40:00 +0700
categories: programming
permalink: /microservices-and-mediatr/
---

### What is MediatR in Microservices?

`MediatR` is a simple, lightweight library that facilitates the **Mediator design pattern** in C#. In the context of Microservices, `MediatR acts as an in-memory message bus` that allows decoupled communication between components such as request handlers and command handlers. Instead of components communicating directly with each other, they communicate through MediatR, which routes the message (request) to the appropriate handler.

### Why Use MediatR in Microservices?

1. **Decoupling**: MediatR helps to decouple components by ensuring that one service doesn't directly depend on another, which makes the system more modular and easier to maintain.
2. **Separation of Concerns**: It promotes the separation of business logic from the infrastructure or UI layer, which is beneficial for complex business applications.
3. **Clearer Code Structure**: It creates a more organized code structure by separating different parts of the application into clear, discrete pieces of functionality.
4. **Simplified Communication**: It allows easy communication within microservices, particularly when dealing with CQRS (Command Query Responsibility Segregation) and Event Sourcing, by making it easier to handle commands, queries, and events.

### MediatR vs General Use

#### General Use

In traditional systems without MediatR, components like services or controllers would directly communicate with each other. This could result in tight coupling, making it harder to maintain and extend the system.

```csharp
public class OrderService
{
    public OrderService() { }

    public void CreateOrder(Order order)
    {
        // Some logic to create the order
    }
}

public class OrderController : ControllerBase
{
    private readonly OrderService _orderService;

    public OrderController(OrderService orderService)
    {
        _orderService = orderService;
    }

    [HttpPost("create-order")]
    public IActionResult CreateOrder([FromBody] Order order)
    {
        _orderService.CreateOrder(order);
        return Ok("Order created successfully.");
    }
}
```

#### With MediatR

Using MediatR, communication is handled through commands, queries, or events. This separates concerns and creates a more extensible system.

```csharp
public class CreateOrderCommand : IRequest
{
    public Order Order { get; set; }
}

public class CreateOrderHandler : IRequestHandler<CreateOrderCommand>
{
    public CreateOrderHandler() { }

    public async Task<Unit> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // Order creation logic

        return Unit.Value;
    }
}

public class OrderController : ControllerBase
{
    private readonly IMediator _mediator;

    public OrderController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost("create-order")]
    public async Task<IActionResult> CreateOrder([FromBody] Order order)
    {
        // Create the command and send it using MediatR
        var command = new CreateOrderCommand { Order = order };
        await _mediator.Send(command);
        return Ok("Order created successfully.");
    }
}
```

Here, MediatR separates the creation of the order. The order creation is now decoupled.

### Extend New Features Easily

#### Without MediatR (General Use)

When implementing a new feature, like sending an email when an order is created, you'd likely need to modify the order creation code directly. Here's how you'd implement it without MediatR:

```csharp
public class OrderService
{
    private readonly IEmailService _emailService;
    private readonly OrderRepository _orderRepository;

    public OrderService(IEmailService emailService, OrderRepository orderRepository)
    {
        _emailService = emailService;
        _orderRepository = orderRepository;
    }

    public void CreateOrder(Order order)
    {
        _orderRepository.SaveOrder(order);
        _emailService.SendEmail($"Order {order.Id} created successfully.");
    }
}
```

In this case, adding features like sending a different email or processing additional tasks would require modifying the CreateOrder method directly.

#### With MediatR (Extension is Easier)

With MediatR, to add an email feature (or any other feature), you can extend the system by creating new handlers or event handlers, without touching the core logic. Here’s how you'd implement it with MediatR:

```csharp
public class CreateOrderCommand : IRequest
{
    public Order Order { get; set; }
}

public class CreateOrderHandler : IRequestHandler<CreateOrderCommand>
{
    private readonly IMediator _mediator;
    private readonly IOrderRepository _orderRepository;

    public CreateOrderHandler(IMediator mediator, IOrderRepository orderRepository)
    {
        _mediator = mediator;
        _orderRepository = orderRepository;
    }

    public async Task<Unit> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // Save the order
        await _orderRepository.SaveOrderAsync(request.Order);

        // Publish event
        await _mediator.Publish(new OrderCreatedEvent { Order = request.Order });

        return Unit.Value;
    }
}

public class OrderCreatedEvent : INotification
{
    public Order Order { get; set; }
}

public class SendEmailOnOrderCreated : INotificationHandler<OrderCreatedEvent>
{
    private readonly IEmailService _emailService;

    public SendEmailOnOrderCreated(IEmailService emailService)
    {
        _emailService = emailService;
    }

    public async Task Handle(OrderCreatedEvent notification, CancellationToken cancellationToken)
    {
        // Send email as a response to the order created event
        await _emailService.SendEmailAsync($"Order {notification.Order.Id} has been created successfully.");
    }
}
```

In this case, the email logic is handled by an `INotificationHandler`, and the order creation logic is decoupled. This makes adding new features, such as sending additional emails, logging, or other notifications, as simple as creating new handlers that listen to the relevant events.

### Comparison of Extending Features

| **Aspect**                | **Without MediatR**               | **With MediatR**                       |
|---------------------------|-----------------------------------|----------------------------------------|
| **Coupling**               | High, as email sending logic is directly tied to order creation logic. | Low, as email sending logic is separated into its own handler, and order creation only publishes an event. |
| **Maintainability**        | Low, requires changes to the core order creation method for each new feature. | High, you can add new features (like sending an email) by simply adding new handlers without modifying the core logic. |
| **Extensibility**          | Moderate, adding new features requires changes to the original method. | Very high, new features are added by creating new handlers for different events. |
| **Code Clarity**           | Less clear, as everything is bundled together. | Clearer, as responsibilities are separated into specific handlers. |

#### Disadvantages:
- Overhead for Small Applications: For simple CRUD operations or small applications, MediatR introduces unnecessary complexity.
- Performance Concerns: Since MediatR uses an in-memory mediator, if the system has a large number of requests and handlers, performance may degrade.
- Learning Curve: Developers unfamiliar with the mediator pattern may find it challenging to grasp the concept and apply it effectively.

### Conclusion
In general use, adding a new feature like sending an email requires modifying the service method directly, which can lead to tight coupling and harder-to-maintain code. However, with MediatR, you can add features more easily by simply adding new handlers. This makes MediatR a highly extensible and maintainable approach, especially in the context of microservices, where decoupling and scalability are crucial.