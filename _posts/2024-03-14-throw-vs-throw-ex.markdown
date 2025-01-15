---
layout: post
title:  "throw vs throw ex"
date:   2024-03-14 08:40:00 +0700
categories: programming
permalink: /throw-vs-throw-ex/
---
`Throw` preserves (bảo quản, lưu giữ) the stack trace. So let's say Method2 calls Method1, Method1 throws Error1, it's caught by Method2 and Method2 says `throw` then Method1 Error1 + Method2 Error2 will be available in the stack trace.

`Throw ex` does not preserve the stack trace. So all errors of Method1 will be wiped out and only Method2 errors will sent to the client (skip the errors from the previous stack trace).