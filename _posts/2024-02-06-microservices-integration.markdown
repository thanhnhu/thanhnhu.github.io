---
layout: post
title:  "Microservices Integration"
date:   2024-02-06 08:40:00 +0700
categories: programming
permalink: /microservices-integration/
---

## Microservices integration example using CQRS, MediatR, Message Queue, Aggregate Root

### Steps Overview:

1. API Controller: Accepts HTTP requests and forwards them to MediatR.
2. Command Handler: Uses MediatR to process commands (e.g., Create Order).
3. Message Queue: Creates a message that will be consumed by a message handler.
4. Consumer: Handles the message and processes the order.
5. Aggregate Root: Responsible for maintaining the business rules and order state.
6. Response from Message Bus: The command handler listens for messages and responds back with the result.

#### 1. API Controller (Create Order):

```csharp
[Route("api/orders")]
[ApiController]
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
        var result = await _mediator.Send(command);
        return Ok(result);
    }
}
```

#### 2. Command & Command Handler:

```csharp
public class CreateOrderCommand : IRequest<OrderResponse>
{
    public string ProductName { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }
}

public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, OrderResponse>
{
    private readonly IMessageBus _messageBus;

    public CreateOrderCommandHandler(IMessageBus messageBus)
    {
        _messageBus = messageBus;
    }

    public async Task<OrderResponse> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // Create Order message to be sent to the message queue
        var orderMessage = new OrderCreatedMessage
        {
            ProductName = request.ProductName,
            Quantity = request.Quantity,
            Price = request.Price
        };

        // Send the order message to the message queue
        await _messageBus.PublishAsync(orderMessage);

        // Wait for the response (or acknowledgment) from the message bus
        var response = await _messageBus.ReceiveResponseAsync<OrderCreatedResponse>();
        
        return response.OrderResponse;
    }
}
```

#### 3. Message Queue (OrderCreatedMessage):

```csharp
public class OrderCreatedMessage
{
    public string ProductName { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }
}
```

This message will be published to the message queue for processing by the consumer.

#### 4. Consumer (Handles Order Creation):

```csharp
public class OrderCreatedConsumer : IConsumer<OrderCreatedMessage>
{
    private readonly IOrderService _orderService;

    public OrderCreatedConsumer(IOrderService orderService)
    {
        _orderService = orderService;
    }

    public async Task Consume(ConsumeContext<OrderCreatedMessage> context)
    {
        var orderMessage = context.Message;

        // Use Aggregate Root to process the order
        var order = new OrderAggregate(orderMessage.ProductName, orderMessage.Quantity, orderMessage.Price);
        await _orderService.ProcessOrderAsync(order);

        // Send response back to the message bus
        var response = new OrderCreatedResponse
        {
            OrderResponse = new OrderResponse
            {
                ProductName = orderMessage.ProductName,
                Quantity = orderMessage.Quantity,
                Price = orderMessage.Price,
                Status = "Created"
            }
        };

        await context.RespondAsync(response);
    }
}
```

The consumer listens for `OrderCreatedMessage` from the message bus and processes the order using the `OrderAggregate`.

#### 5. Aggregate Root (Order):

```csharp
public class OrderAggregate
{
    public string ProductName { get; private set; }
    public int Quantity { get; private set; }
    public decimal Price { get; private set; }

    public OrderAggregate(string productName, int quantity, decimal price)
    {
        ProductName = productName;
        Quantity = quantity;
        Price = price;
    }

    public void CreateOrder()
    {
        // Business rules for creating an order
        if (Quantity <= 0)
        {
            throw new InvalidOperationException("Quantity must be greater than zero.");
        }

        // Process the order here
    }
}
```

The `OrderAggregate` class is the Aggregate Root, encapsulating the business logic for the order. It ensures that the order is valid and ready to be processed.

#### 6. Order Response (Message Bus Response):

```csharp
public class OrderCreatedResponse
{
    public OrderResponse OrderResponse { get; set; }
}

public class OrderResponse
{
    public string ProductName { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }
    public string Status { get; set; }
}
```

The OrderCreatedResponse is sent back to the command handler after the order has been processed successfully by the consumer.

#### 7. Message Bus Interface:

```csharp
public interface IMessageBus
{
    Task PublishAsync<T>(T message);
    Task<TResponse> ReceiveResponseAsync<TResponse>();
}
```

The `IMessageBus` interface represents the abstraction for the message bus that will handle publishing and receiving messages.

#### Integration in Startup.cs:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMediatR(typeof(CreateOrderCommandHandler));
    // Message Bus setup (using MassTransit or other libraries)
    services.AddMassTransit(x => x.AddConsumer<OrderCreatedConsumer>());
    services.AddMassTransitHostedService();
    services.AddScoped<IOrderService, OrderService>();
    services.AddScoped<IMessageBus, MassTransitMessageBus>();
}
```

### Conclusion

The approach described offers significant benefits for complex, scalable, and maintainable systems by combining CQRS, MediatR, Event-Driven Architecture, and Aggregate Roots. However, the complexity and overhead involved in the implementation of each component should be carefully evaluated based on the specific needs of your application. For simpler systems, a more straightforward approach may be more appropriate, while larger and more distributed systems will benefit from these patterns.