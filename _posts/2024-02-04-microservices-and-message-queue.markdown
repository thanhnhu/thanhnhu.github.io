---
layout: post
title:  "Microservices and Message Queue"
date:   2024-02-04 08:40:00 +0700
categories: programming
permalink: /microservices-and-message-queue/
---

A **Message Queue (MQ)** is a communication method used in microservices where services exchange data asynchronously through messages. A `producer` (sender) puts messages into a queue, and `consumers` (receivers) process those messages.

**Popular MQ Systems:** RabbitMQ, Apache Kafka, Azure Service Bus, Amazon SQS

### Advantages of Using Message Queue in Microservices

1. **Decoupling** - Services communicate without direct dependencies.  
2. **Scalability** - Consumers can scale horizontally.  
3. **Fault Tolerance** - Messages persist during service downtime.  
4. **Asynchronous Processing** - Improves response time for users.  
5. **Load Balancing** - Distributes load among consumers.

### Disadvantages

1. **Complexity** - Increases system complexity.  
2. **Latency** - Not suitable for real-time needs.  
3. **Message Loss** - Requires proper handling.  
4. **Error Handling** - Needs dead-letter queues.  
5. **Ordering Issues** - Maintaining order can be hard.

### Microservices with Message Queue in C# - Create Order Example

#### 1. Order API Controller (Producer)

```csharp
using MediatR;
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

namespace OrderService.Controllers
{
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
            var result = await _mediator.Send(command);
            return Ok(result);
        }
    }
}
```

#### 2. Command Handler (Publish to RabbitMQ)

```csharp
using MediatR;
using Newtonsoft.Json;
using RabbitMQ.Client;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace OrderService.Application.Handlers
{
    public class CreateOrderHandler : IRequestHandler<CreateOrderCommand, string>
    {
        public Task<string> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
        {
            var factory = new ConnectionFactory() { HostName = "localhost" };
            using var connection = factory.CreateConnection();
            using var channel = connection.CreateModel();

            channel.QueueDeclare(queue: "order-queue", durable: false, exclusive: false, autoDelete: false, arguments: null);

            var message = JsonConvert.SerializeObject(request);
            var body = Encoding.UTF8.GetBytes(message);

            channel.BasicPublish(exchange: "", routingKey: "order-queue", basicProperties: null, body: body);

            return Task.FromResult("Order is being processed.");
        }
    }
}
```

#### 3. Order Consumer (Listener Service)

```csharp
using Newtonsoft.Json;
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System;
using System.Text;

namespace OrderService.Consumer
{
    public class OrderConsumer
    {
        public void Start()
        {
            var factory = new ConnectionFactory() { HostName = "localhost" };
            using var connection = factory.CreateConnection();
            using var channel = connection.CreateModel();

            channel.QueueDeclare(queue: "order-queue", durable: false, exclusive: false, autoDelete: false, arguments: null);

            var consumer = new EventingBasicConsumer(channel);
            consumer.Received += (model, ea) =>
            {
                var body = ea.Body.ToArray();
                var message = Encoding.UTF8.GetString(body);
                var order = JsonConvert.DeserializeObject<CreateOrderCommand>(message);

                Console.WriteLine($"Received Order: ProductId={order.ProductId}, Quantity={order.Quantity}, Price={order.Price}");
            };

            channel.BasicConsume(queue: "order-queue", autoAck: true, consumer: consumer);

            Console.WriteLine("Listening for orders...");
            Console.ReadLine();
        }
    }
}
```

#### 4. Request Flow

1. **API Call:**  
   `POST /api/orders` with body:

   ```json
   {
     "productId": "P001",
     "quantity": 2,
     "price": 99.99
   }
   ```

2. **Queue Publishing:**  
   `CreateOrderHandler` publishes to **RabbitMQ** (`order-queue`).

3. **Order Processing:**  
   `OrderConsumer` listens and processes orders.

4. **API Response:**  
   `"Order is being processed."`

### Summary

**Advantages Applied:**

- Decoupling: API and processing are independent.
- Scalability: Multiple consumers can process orders.
- Asynchronous: Faster API response.

**Disadvantages Mitigated:**

- Error Handling: Can be enhanced with dead-letter queues.
- Complexity: Managed with RabbitMQ and MediatR.