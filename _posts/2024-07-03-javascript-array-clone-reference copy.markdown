---
layout: post
title:  "Javascript Array Clone Reference"
date:   2024-07-03 08:40:00 +0700
categories: programming
permalink: /javascript-array-clone-reference/
---

In TypeScript (and JavaScript in general), `arrays are reference types`. This means that when you assign an array to another variable or pass it into a function, both variables or parameters reference the same memory location.

As a result:

1. `Changes made to the array through one variable will affect the other variable.`
2. If you want to copy an array without modifying the original array, you need to create a clone.

#### Example Demonstrations:
1\. Changes via reference:

```typescript
let arr1 = [1, 2, 3];
let arr2 = arr1; // arr2 references the same memory as arr1
arr2[0] = 100;   // Modifying the first element

console.log(arr1); // [100, 2, 3]
console.log(arr2); // [100, 2, 3]
```

2\. Create a copy to avoid modifying the original:

If you want to copy an array instead of referencing it, you can use the following methods:

a. Using the spread operator:

```typescript
let arr1 = [1, 2, 3];
let arr2 = [...arr1]; // Create a copy of arr1
arr2[0] = 100;

console.log(arr1); // [1, 2, 3]
console.log(arr2); // [100, 2, 3]
```

b. Using `Array.slice()`:

```typescript
let arr1 = [1, 2, 3];
let arr2 = arr1.slice(); // Create a copy of arr1
arr2[0] = 100;

console.log(arr1); // [1, 2, 3]
console.log(arr2); // [100, 2, 3]
```

c. Using `Array.from()`:

```typescript
let arr1 = [1, 2, 3];
let arr2 = Array.from(arr1); // Create a copy of arr1
arr2[0] = 100;

console.log(arr1); // [1, 2, 3]
console.log(arr2); // [100, 2, 3]
```

#### Note:
- If the array is nested (contains other arrays or objects - mảng lồng nhau), the techniques above only create a `shallow copy` (bản sao nông).
- To create a `deep copy` (bản sao sâu) in cases of complex arrays, you might need to use libraries like lodash or use JSON.stringify + JSON.parse.

`Deep Copy` using JSON:

```typescript
let arr1 = [[1, 2], [3, 4]];
let arr2 = JSON.parse(JSON.stringify(arr1));

arr2[0][0] = 100;
console.log(arr1); // [[1, 2], [3, 4]]
console.log(arr2); // [[100, 2], [3, 4]]
```