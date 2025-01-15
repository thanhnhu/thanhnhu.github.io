---
layout: post
title:  "REST API vs WebAPI vs MVC"
date:   2024-03-28 08:40:00 +0700
categories: programming
permalink: /restapi-vs-webapi-vs-mvc/
---
`REST API (Representational State Transfer Application Programming Interface)` is an architectural style for designing networked applications. It is a set of constraints or principles that define how resources should be represented and addressed in a distributed and stateless manner. REST is not a specific technology but rather a set of architectural principles commonly applied to web services.

Key characteristics and principles of REST APIs:

`Statelessness`: Each request from a client to a server must contain all the information needed to understand and fulfill the request. The server should not store any information about the client's state between requests. Each request is independent.

`Client-Server Architecture`: The architecture is divided into a client and a server, each with distinct responsibilities. The client is responsible for the user interface and user experience, while the server is responsible for processing requests and managing resources.

`Uniform Interface`: RESTful APIs have a uniform and consistent interface, which simplifies and enhances the overall architecture. The uniform interface is typically composed of four constraints:

- `Resource-Based`: Resources are identified and manipulated using URIs (Uniform Resource Identifiers). Each resource should have a unique URI.
- `Representation`: Resources can have multiple representations, such as XML, JSON, or HTML. Clients interact with these representations rather than directly with the resources.
- `Stateless Communication`: Each request from a client contains all the information needed for the server to fulfill the request. The server does not store any client state between requests.
- `Stateless Server`: The server does not store any state about the client between requests. Any required state is sent by the client with each request.

`Resource-Based`: Resources are the key abstraction in REST, and they can represent entities, concepts, or services. Each resource should be uniquely identifiable using a URI.

`HTTP Methods (CRUD Operations)`:

- GET: Retrieve a representation of a resource.
- POST: Create a new resource.
- PUT/PATCH: Update an existing resource.
- DELETE: Remove a resource.

`Hypermedia (HATEOAS - Hypermedia As The Engine Of Application State)`: The representation of a resource contains hypermedia links that guide the client on the available actions it can perform.

RESTful APIs are widely used in web development and are the foundation for building scalable and interoperable web services. They are often employed for communication between web servers and clients, as well as in microservices architectures. The data exchanged is typically in JSON or XML format.

#### Difference between RestAPI and WebAPI?
REST API refers specifically to APIs that adhere to the principles of Representational State Transfer, emphasizing stateless communication, resource-based interaction, and a uniform interface. Web API is a more general term that can refer to any API accessible over the web, regardless of its architectural style or underlying technologies. A RESTful API is a subset of Web APIs that follows the REST principles.

So Web API use SOAP-based protocol while REST API use HTTP methods. REST API return json/xml while Web API return any data type.

#### Difference between MVC and WebAPI?
MVC is focused on building web applications that serve HTML views, while Web API is designed for building APIs that provide data and services in various formats. It's common to see both MVC and Web API used together in modern web applications where MVC handles UI-related concerns, and Web API provides services for client-side interactions or other applications.

So MVC return View, while Web API return data.