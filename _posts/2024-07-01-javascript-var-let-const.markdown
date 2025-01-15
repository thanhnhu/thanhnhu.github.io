---
layout: post
title:  "Javascript var let const"
date:   2024-07-01 08:40:00 +0700
categories: programming
permalink: /javascript-var-let-const/
---

### Comparison of `var`, `let`, and `const` in JavaScript

In JavaScript, `var`, `let`, and `const` are keywords used to declare variables, but they have important differences related to scope, redeclaration, and mutability of values. Below is a detailed comparison:

#### 1. Scope
- **`var`:**  
  - Has **function scope**: a `var` variable is only effective within the function it was declared.  
  - Does not have **block scope**: if declared inside a block (`{}`), the variable can still be accessed outside of that block.  
  - Example:
    ```javascript
    if (true) {
      var x = 10;
    }
    console.log(x); // 10
    ```

- **`let` and `const`:**  
  - Have **block scope**: the variable is only effective within the block (`{}`) it was declared.  
  - Example:
    ```javascript
    if (true) {
      let y = 20;
    }
    console.log(y); // Error: y is not defined
    ```

#### 2. Redeclaration (Khả năng tái khai báo)

- **`var`:** Can be redeclared within the same scope without causing an error.
```javascript
var z = 5;
var z = 10; // No error
console.log(z); // 10
```

- `let` and `const`: Cannot be redeclared within the same scope.
```javascript
let a = 5;
let a = 10; // Error: Identifier 'a' has already been declared
```

#### 3. Reassignment

- `var` and `let`: Can be reassigned after declaration.

```javascript
var x = 5;
x = 10; // Valid

let y = 15;
y = 20; // Valid
```

- `const`: Cannot be reassigned after declaration.

```javascript
const z = 30;
z = 40; // Error: Assignment to constant variable
```

Note: for objects or arrays, the contents can change, but the variable itself cannot be reassigned.

```javascript
const obj = { name: 'Alice' };
obj.name = 'Bob'; // Valid
obj = { age: 25 }; // Error
```

#### 4. Hoisting (Cơ chế đưa lên đầu)

- `var`: Is "hoisted" (moved to the top of the scope) but not initialized. The variable's value will be undefined if accessed before being assigned.

```javascript
console.log(x); // undefined
var x = 10;
```

- `let` and `const`: Are also "hoisted", but cannot be accessed before initialization (Temporal Dead Zone).

```javascript
console.log(y); // Error: Cannot access 'y' before initialization
let y = 10;
```

#### 5. When to Use?
- `var`: Should be avoided as it can lead to errors due to loose scope and hoisting behavior.
- `let`: Use when you need to declare a variable that may change its value.
- `const`: Use when declaring a variable whose value should not change, especially for constants, objects, or arrays.

### Summary

| Attribute         | `var`                 | `let`                    | `const`                  |
| ----------------- | --------------------- | ------------------------ | ------------------------ |
| **Scope**         | Function              | Block                    | Block                    |
| **Redeclaration** | Allowed               | Not Allowed              | Not Allowed              |
| **Reassignment**  | Allowed               | Allowed                  | Not Allowed              |
| **Hoisting**      | Yes (undefined value) | Yes (Temporal Dead Zone) | Yes (Temporal Dead Zone) |

Using `let` and `const` will make your code safer and easier to maintain.