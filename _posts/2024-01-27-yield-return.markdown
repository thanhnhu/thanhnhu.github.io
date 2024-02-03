---
layout: post
title:  "What is yield return"
date:   2024-01-27 08:40:00 +0700
categories: programming
permalink: /yield-return/
---
Can understand `yield return` as `lazy` versus `normal return` as `eager`.
In an iterator block, `normal return` returns the entire resultset at once. While `yield return` returns instance of `IEnumerable` (implemented by Array/List), and then returns one by one when we access the value (the index position will be noted for the next access). Iterators can be used to lazily generate a sequence of objects. So the code in the method only executed once we need the value (and it's one by one)

`Benefits`: save memory by no need to iterate all the collection, ex: with .First() with predicate only execute 1 time, there is no need to loop all the collection.