---
layout: post
title:  "What is Object-Oriented Programming (OOP)"
date:   2024-01-15 08:40:00 +0700
sticky: true
categories: programming
permalink: /oop-programming/
---
`Object-Oriented Programming (OOP)` is a programming paradigm that organizes code into objects, which are instances of classes. It focuses on representing real-world entities as objects that have properties (attributes) and behaviors (methods). OOP aims to provide a structured and modular approach to software development.

The key concepts of OOP include:

- `Classes and Objects`: A class is a blueprint or template that defines the properties and behaviors of objects. An object is an instance of a class, which can hold data (attributes) and perform actions (methods).

- `Encapsulation`: Encapsulation is the mechanism of bundling data (attributes) and methods that operate on that data within a single unit (object or class). It provides data hiding and abstraction, ensuring that the internal state of an object is accessible only through defined methods.

- `Inheritance`: Inheritance allows classes to inherit properties and behaviors from other classes. It establishes a parent-child relationship between classes, where the child class (subclass) inherits the characteristics of the parent class (superclass). This promotes code reuse and supports the "is-a" relationship.

- `Polymorphism`: Polymorphism allows objects of different classes to be treated as objects of a common superclass. It enables the use of a single interface to represent different types of objects. Polymorphism is achieved through method overriding (providing a different implementation in a subclass) and method overloading (providing multiple methods with the same name but different parameters).

- `Abstraction`: Abstraction focuses on providing a simplified and generalized view of objects and their interactions. It involves hiding complex implementation details and exposing only relevant information and functionality. Abstract classes and interfaces are used to define common behaviors and establish contracts for classes to follow.

OOP promotes modularity, reusability, and maintainability by organizing code into reusable objects and establishing relationships between them. It allows for better code organization, separation of concerns, and easier collaboration among developers. OOP is widely used in various programming languages such as Java, C++, Python, and C#.

<style scoped>
table {
  font-size: 14px;
}
</style>

#### Difference between an abstract class and an Interface?

|Abstract Class                                               |Interface                                |
|---|---|
|A class can extend only one Abstract class                   |A class can implement several interfaces |
|The member of an abstract class can be private and protected |An Interface can only have public members|
|Abstract classes should have subclasses                      |Interfaces must have Implementations by classes|
|Any class can extend an abstract class                       |Only an Interface can extend another Interface|
|Methods in an abstract class can be abstract as well as concrete|All methods in an Interface should be abstract|
|There can be a constructor for Abstract class                |Interface does not have constructor|
|An abstract class can Implement methods                      |Interfaces can not contain body of any of its method|

#### Difference between Method Overloading and Method Overriding?

|Method Overloading                                           |Method Overriding  |
|---|---|
|Method Overloading lets you have 2 methods with same name and different signature|Method Overriding lets you have 2 methods with same name and same signature|
|Overloading is called as compile time polymorphism or early binding              |Overriding is called as run time polymorphism or late binding or dynamic polymorphism|
|Overloading can be achieved:                                 |Overriding can be achieved:|
|-By changing the number of parameters used.                  |-Creating the method in a derived class with same name, same parameters and same return type as in base class is called as method overriding|
|-By changing the order of parameters.                        ||
|-By using different data types for the parameters.           ||
|Method overloading can be overloaded in same class or in the child class.|Method overriding is only possible in derived class not within the same class where the method is declared|