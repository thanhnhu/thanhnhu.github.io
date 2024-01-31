---
layout: post
title:  "What is SOLID Principles"
date:   2024-01-17 08:40:00 +0700
categories: programming
permalink: /solid-principles/
---
The `SOLID` principles are a set of design principles that aim to guide software developers in writing code that is maintainable, flexible, and easy to understand. These principles were introduced by Robert C. Martin (also known as Uncle Bob) and have become widely accepted as best practices in object-oriented programming.

The acronym SOLID stands for:

- `Single Responsibility Principle (SRP)`: A class should have only one reason to change. It means that a class or module should have a single responsibility or purpose. This principle promotes high cohesion and reduces the impact of changes in one area on other unrelated areas of the system.

- `Open/Closed Principle (OCP)`: Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification. This principle encourages designing code that is easily extensible without requiring changes to existing, working code.

- `Liskov Substitution Principle (LSP)`: Subtypes must be substitutable for their base types. This principle ensures that objects of a superclass can be replaced with objects of its subclasses without affecting the correctness of the program. In other words, subclasses should be able to be used interchangeably with their base class without causing any unexpected behavior.

- `Interface Segregation Principle (ISP)`: Clients should not be forced to depend on interfaces they do not use. It suggests that interfaces should be specific to the needs of the clients that use them, rather than having large interfaces with a lot of methods. This principle promotes loose coupling and allows for better maintainability and flexibility.

- `Dependency Inversion Principle (DIP)`: High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions. This principle encourages decoupling between modules by relying on abstractions (interfaces or abstract classes) instead of concrete implementations.

By following these principles, developers can create code that is more modular, testable, and easier to maintain. They provide a guideline for writing software that is more flexible and resistant to changes, promoting good software design and architecture.