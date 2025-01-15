---
layout: post
title:  "Microservices and Aggregate Root"
date:   2024-02-02 08:40:00 +0700
categories: programming
permalink: /microservices-and-aggregate-root/
---

### What is Aggregate Root?

**Aggregate Root** is a central concept in **Domain-Driven Design (DDD)**. It acts as the entry point to a group of related entities, ensuring consistency and enforcing business rules.

#### Key Characteristics:
1. **Consistency Boundary** – Ensures data integrity across related entities.  
2. **Encapsulation** – Hides internal entities, exposing only the root.  
3. **Transactional Boundary** – Handles transactions as a unit.  
4. **Domain Integrity** – Enforces domain logic within the aggregate.

### Why Aggregate Root Is Better

| **Aspect**        | **Without Aggregate Root**                   | **With Aggregate Root**        |
| ----------------- | -------------------------------------------- | ------------------------------ |
| **Consistency**   | Inconsistent due to direct entity access.    | Enforced by root-only access.  |
| **Encapsulation** | Entities exposed, leading to invalid states. | Internal entities are hidden.  |
| **Complexity**    | Complex and unstructured.                    | Simplifies relationships.      |
| **Transactional** | Manual transaction management.               | Naturally handled in the root. |

### Code Comparison

#### Without Aggregate Root

```csharp
public class Order
{
    public Guid Id { get; set; }
    public List<OrderItem> Items { get; set; } = new List<OrderItem>();
    public decimal TotalAmount { get; set; }

    public void AddItem(OrderItem item)
    {
        Items.Add(item);
        TotalAmount += item.Price * item.Quantity;
    }
}

public class OrderItem
{
    public Guid Id { get; set; }
    public string ProductName { get; set; }
    public decimal Price { get; set; }
    public int Quantity { get; set; }
}
```

Issues:

- Direct access to Items.
- No validation on data.

#### With Aggregate Root

```csharp
public class Order
{
    public Guid Id { get; private set; }
    private List<OrderItem> _items = new List<OrderItem>();
    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();
    public decimal TotalAmount { get; private set; }

    public void AddItem(string productName, decimal price, int quantity)
    {
        if (price <= 0 || quantity <= 0)
            throw new ArgumentException("Invalid price or quantity.");

        var item = new OrderItem(productName, price, quantity);
        _items.Add(item);
        TotalAmount += item.Price * item.Quantity;
    }
}

public class OrderItem
{
    public Guid Id { get; private set; }
    public string ProductName { get; private set; }
    public decimal Price { get; private set; }
    public int Quantity { get; private set; }

    public OrderItem(string productName, decimal price, int quantity)
    {
        Id = Guid.NewGuid();
        ProductName = productName;
        Price = price;
        Quantity = quantity;
    }
}
```

#### Advantages:
- Controlled access to entity data.
- Validation ensures data integrity.
- Transaction-safe operations.

#### Disadvantages:
- Complexity in Modeling: Defining the right aggregates and the boundaries between aggregates can be complex, especially for large systems.
- Overhead in Handling: Aggregates can introduce overhead, especially when you have to load a large amount of data into memory or deal with large aggregates, which might not be required in every scenario.
- Potential for Bloating: If not carefully managed, aggregates can become bloated with too many responsibilities and excessive logic, violating the Single Responsibility Principle.