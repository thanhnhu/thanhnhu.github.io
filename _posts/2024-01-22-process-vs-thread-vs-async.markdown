---
layout: post
title:  "process vs thread vs async/await"
date:   2024-01-22 08:40:00 +0700
categories: programming
permalink: /process-vs-thread-vs-async/
---
A program (application like MS Word) when running, it's loaded into memory and become a `Process`. `Thread` is simplest execution unit of process, process can have multiple Thread, ex: MS Word is a process, there have a thread to check gramma, thread to input user typing.

#### Threading vs Multithreading

`Single threading`: when tasks need to be `executed sequentially`. With the long task, which could potentially block the main thread, resulting in the freeze user interface

`Multithreading`: many tasks/functions can run at the same time. Use multithreading when your application has tasks that can `run concurrently`, independently, and without needing to be sequentially organized. This is particularly useful when performing I/O-bound work or expensive computations while keeping the user interface responsive.

`Task` a simple execution unit of thread.

#### async/await
A method go with async, it must have a `await` keyword in that method, when it first enter the await, it return the control to its calling, this make the method not blocking the main thread, so it makes the method asynchronus (blocking means freeze UI if windows form app or maybe hang the IIS server-application pool if web app, then slow down the server performance).

```cs
await method1();		var task1 = method1();
method2();  			method2();
    				await task1;
```

`async void` means the method call not waiting for the completion.