---
layout: post
title:  "How to prevent class from being inherited?"
date:   2024-01-29 08:40:00 +0700
categories: programming
permalink: /prevent-class-inherit/
---
In many programming languages, you can prevent a class from being inherited by using the `sealed` (C#) or `final` (Java) modifier. This modifier restricts the inheritance of a class, ensuring that no other class can extend or inherit from it. Here's an example in both C# and Java:

```cs
sealed class SealedClass
{
  // Class members and methods
}
```

```java
final class FinalClass {
  // Class members and methods
}
```