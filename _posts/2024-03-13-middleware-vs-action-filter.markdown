---
layout: post
title:  "Middleware vs Action Filter in ASP.NET Core"
date:   2024-03-13 08:40:00 +0700
categories: programming
permalink: /middleware-vs-action-filter/
---
Both **ActionFilter** and **Middleware** are powerful mechanisms in ASP.NET Core used to handle cross-cutting concerns, but they operate at different levels of the request pipeline and serve distinct purposes.

### **1. Basic Concept**

| **Aspect**      | **Middleware**                                      | **ActionFilter**                                       |
|-----------------|------------------------------------------------------|-------------------------------------------------------|
| **Definition**  | Handles **incoming requests** and **outgoing responses** at the **application level**. | Handles logic **before** and **after** controller actions at the **controller/action level**. |
| **Pipeline**    | Executes **before** the request reaches routing and MVC. | Executes **after routing** but **before/after** controller actions. |
| **Scope**       | Applies to the **entire application**.               | Applies to specific **controllers** or **actions**.    |

### **2. Execution Flow**

| **Aspect**             | **Middleware**                                      | **ActionFilter**                               |
|------------------------|-----------------------------------------------------|------------------------------------------------|
| **When It Runs**       | Runs **before** routing, or globally throughout the pipeline. | Runs **before and after** controller actions.   |
| **Control Flow**       | Uses `await next()` to continue the pipeline.       | Uses `OnActionExecuting` and `OnActionExecuted` methods. |
| **Execution Context**  | Has access to the **HttpContext** (global request/response). | Has access to the **ActionContext** (controller/action details). |

### **3. Use Cases**

| **Middleware**                                          | **ActionFilter**                                      |
|---------------------------------------------------------|------------------------------------------------------|
| **Global logging** of requests and responses.            | **Logging** at the controller/action level.           |
| **Authentication/Authorization** across the app.        | **Model validation** for specific actions.            |
| **Global exception handling** (Error handling).         | **Input validation** and action-specific security checks. |
| **Response compression, caching, CORS** policies.       | **Custom logic** before/after an action executes.     |
| **Routing, static file serving**, and **session management**. | **Result filtering** or modifying the action result.  |

### **4. Implementation**

#### **Middleware Example**
```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Pre-processing logic
        Console.WriteLine("Middleware: Before next");

        await _next(context); // Continue to the next middleware

        // Post-processing logic
        Console.WriteLine("Middleware: After next");
    }
}

// Register Middleware in Program.cs or Startup.cs
app.UseMiddleware<CustomMiddleware>();
```

#### **ActionFilter Example**
```csharp
public class CustomActionFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        // Logic before the action executes
        Console.WriteLine("ActionFilter: Before action execution");
    }

    public override void OnActionExecuted(ActionExecutedContext context)
    {
        // Logic after the action executes
        Console.WriteLine("ActionFilter: After action execution");
    }
}

// Apply to a specific controller or action
[CustomActionFilter]
public IActionResult MyAction()
{
    return Ok("Hello World");
}
```

### **5. Key Differences**

| **Feature**               | **Middleware**                                       | **ActionFilter**                                     |
|---------------------------|-----------------------------------------------------|-----------------------------------------------------|
| **Level**                 | Application-wide.                                   | Controller or action-specific.                      |
| **Execution Point**       | Runs early in the pipeline, even before routing.     | Runs after routing but before/after actions.        |
| **Context Access**        | Accesses `HttpContext` (global request/response data). | Accesses `ActionContext` (controller/action data). |
| **Flexibility**           | Better for cross-cutting concerns (logging, auth).  | Better for action-specific logic (validation).      |
| **Error Handling**        | Handles global errors (exception middleware).       | Handles action-specific errors.                     |
| **Usage Scope**           | Ideal for app-wide concerns.                        | Ideal for controller/action concerns.              |

### **6. When to Use?**

- **Use Middleware** when:
  - You need to handle **global concerns** like logging, authentication, authorization, error handling, or CORS.
  - You need to **manipulate the request/response pipeline** before it reaches the routing system.

- **Use ActionFilter** when:
  - You need logic tied specifically to **controller actions** (e.g., model validation, custom authorization).
  - You want to **manipulate action inputs or results**.

### **7. Summary**

| **Aspect**               | **Middleware**                                     | **ActionFilter**                                     |
|--------------------------|---------------------------------------------------|----------------------------------------------------|
| **Scope**                | Global, application-wide.                         | Specific to controllers/actions.                    |
| **Execution Timing**     | Before routing and controller execution.          | Before and after controller actions.               |
| **Best for**             | Cross-cutting concerns (auth, logging, errors).   | Action-specific logic (validation, logging).       |
| **Context Access**       | `HttpContext` (global request data).              | `ActionContext` (action-specific data).            |
| **Pipeline Control**     | Uses `await next()` to continue.                  | Uses `OnActionExecuting`/`OnActionExecuted`.       |

In summary, **Middleware** is suited for **global application behavior**, while **ActionFilter** is best for **controller/action-specific logic**.