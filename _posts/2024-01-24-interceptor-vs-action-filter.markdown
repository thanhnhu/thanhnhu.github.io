---
layout: post
title:  "Interceptor vs Action Filter"
date:   2024-01-24 08:40:00 +0700
categories: programming
permalink: /interceptor-vs-action-filter/
---
`Interceptor` is a design pattern that allows code to be executed or data to be manipulated before or after a particular operation (an action method is invoked). For example, in web development, interceptors can be used to modify HTTP requests or responses (request/response pipeline). Allowing developers to use interceptors to manage concerns like logging, security, caching, validation, and transaction handling in a modular and reusable way.

`Action Filter` is an implementation of `Interceptor` in web development, an action filter is a feature provided by many web frameworks that allows developers to attach custom behavior to specific actions or controllers. Action filters are commonly used to perform tasks such as logging, authentication, authorization, input validation, and caching.

Here are some commonly used action filters:

1. `Authorization Filters`: Check if the current user has the necessary permissions to access a particular resource.
2. `Authentication Filters`: Verify the identity of a user, typically before authorization checks.
3. `Action Result Filters`: Modify the result of an action (e.g., add headers, modify the content, or redirect).
4. `Exception Filters`: Handle exceptions that occur during the execution of an action.
5. `Action Filters`: Execute code before and after the execution of an action method.

`Delegating Handler` is a type of message handler that can be used to process HTTP requests and responses. `Delegating Handlers` are part of the request/response pipeline and provide a way to perform custom processing at an earlier stage than controllers or action filters.