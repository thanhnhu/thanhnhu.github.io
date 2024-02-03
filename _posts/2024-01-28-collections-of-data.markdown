---
layout: post
title:  "Collections of data"
date:   2024-01-28 08:40:00 +0700
categories: programming
permalink: /collections-of-data/
---
In C#, there are several interfaces available for collections of data that can be iterated over or queried. These interfaces include `IEnumerable`, `IEnumerator`, `IQueryable`, `ICollection`, and `IList`. Each of these interfaces has its own unique characteristics and is used in different scenarios depending on the requirements of your application.

#### IEnumerable and IEnumerator

The `IEnumerable` interface is the most basic of the collection interfaces. It provides a single method, GetEnumerator(), which returns an `IEnumerator` that can be used to iterate over a collection of objects. The `IEnumerator` interface provides a way to iterate over a collection of objects one at a time. It defines two methods: MoveNext() and Reset(). MoveNext() moves the cursor to the next element in the collection and returns a boolean indicating whether there are any more elements to be read. Reset() resets the cursor to the beginning of the collection.

`IEnumerable` is suitable for collections that only need to be iterated over once. It is a read-only interface, which means that it only provides methods for reading the data, not modifying it. On the other hand, IEnumerator provides a way to iterate over a collection of objects one at a time, and it is used to retrieve data from a collection in a sequential order.

#### IQueryable

The `IQueryable` interface is used to represent a query that can be executed against a data source. It provides a way to build complex queries by composing expressions and then executing them against a data source. IQueryable is commonly used in LINQ-to-SQL and Entity Framework applications.

IQueryable is a powerful interface that allows for deferred execution of queries. This means that the query is not actually executed until the results are needed. IQueryable provides several methods, such as Where(), Select(), and OrderBy(), that can be used to build complex queries.

#### ICollection and IList

The `ICollection` interface provides a way to add, remove, and count items in a collection. It is suitable for collections that need to be modified. The `IList` interface extends ICollection and provides additional methods for manipulating items in a collection. It allows items to be inserted at a specific index, retrieved by index, and replaced at a specific index.

ICollection and IList are suitable for collections that need to be modified. They provide methods for adding, removing, and manipulating items in a collection. The main difference between ICollection and IList is that IList allows items to be inserted at a specific index and retrieved by index.

#### IEnumerable vs IQueryable

`IEnumerable` operations are executed in memory. All data is retrieved from the collection, and subsequent operations are performed in memory.

`IQueryable` operations are typically translated into a query language (e.g., SQL for databases) and executed on the data source. This allows for server-side processing and optimization.

<!-- ![image info](../images/1702143812675.jpg) -->