---
layout: post
title:  "Angular Observable vs Subject"
date:   2024-07-05 08:40:00 +0700
categories: programming
permalink: /angular-observable-subject/
---

In Angular, both `Observable` and `Subject` are part of the `Reactive Programming` paradigm, leveraging the `RxJS` library. However, they serve different purposes and have different behaviors.

### Observable
- Definition: An `Observable` represents a stream of data that can be observed over time. It can emit values asynchronously.
- Creation: It is created using `Observable.create()` or various helper methods such as `of()`, `from()`, or `interval()`.
- Purpose: Observables are used for unidirectional data flows where consumers subscribe to the stream to receive updates.
- Behavior: When you subscribe to an Observable, it starts emitting values. An Observable can emit multiple types of values, including numbers, strings, or objects, depending on the logic.

Example:

```typescript
import { Observable } from 'rxjs';

const observable = new Observable(subscriber => {
  subscriber.next('Hello');
  subscriber.next('World');
  subscriber.complete();
});

observable.subscribe(value => {
  console.log(value);
});
```

Output:

```typescript
Hello
World
```

### Subject
- Definition: A `Subject` is a special type of `Observable` that allows values to be both `multicasted` to multiple subscribers and `multidirectional` (it can also act as both an observer and an observable).
- Creation: It is created using new Subject().
- Purpose: A Subject is useful when you want to share a single data stream across multiple subscribers. It is like a combination of an `Observable` and an `Observer`.
- Behavior: A Subject can emit values that can be received by multiple subscribers. Unlike an Observable, a Subject can also push values from the outside.
  
Example:

```typescript
import { Subject } from 'rxjs';

const subject = new Subject();

subject.subscribe(value => {
  console.log('Subscriber 1:', value);
});

subject.subscribe(value => {
  console.log('Subscriber 2:', value);
});

subject.next('Hello');
subject.next('World');
```

Output:

```yaml
Subscriber 1: Hello
Subscriber 2: Hello
Subscriber 1: World
Subscriber 2: World
```

#### Key Differences:
1\. Unicast vs Multicast:

- Observable: Each subscriber gets its own independent execution of the stream (unicast).
- Subject: A Subject multicasts the values to all its subscribers, sharing the same stream.
  
2\. Creation:

- Observable: A new Observable starts execution when subscribed to.
- Subject: A Subject can be subscribed to by multiple observers, and values can be manually pushed by using .next().
  
3\. Side Effects:

- Observable: Observables are purely functional and do not have side effects unless explicitly specified.
- Subject: Since a Subject can act as both an observer and observable, you can directly push data into it, leading to side effects (e.g., when emitting values through .next()).
  
4\. Use Case:

- Observable: Ideal for one-way data streams where the producer emits data and consumers subscribe to it.
- Subject: Useful for scenarios where you need a "central dispatcher" or for managing shared state across components. It's commonly used in services for communication between components in Angular.

#### Example Use Case of Subject:
A common use case of a Subject in Angular is for handling shared state, such as a service that sends events to multiple components.

```typescript
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class MessageService {
  private messageSource = new Subject<string>();
  message$ = this.messageSource.asObservable();

  sendMessage(message: string) {
    this.messageSource.next(message);
  }
}
```

In a component, you would subscribe to message$ and react to the message changes:

```typescript
import { Component, OnInit } from '@angular/core';
import { MessageService } from './message.service';

@Component({
  selector: 'app-message',
  templateUrl: './message.component.html',
})
export class MessageComponent implements OnInit {
  message: string;

  constructor(private messageService: MessageService) {}

  ngOnInit() {
    this.messageService.message$.subscribe(msg => {
      this.message = msg;
    });
  }
}
```

This way, any component that subscribes to the `message$` observable will get updates whenever `sendMessage()` is called.

### Pipe

In Angular, the `pipe()` method is used with `Observables` to compose operations that transform or handle data in a declarative way. The pipe() method allows you to chain RxJS operators, which are functions that operate on Observables to perform actions like filtering, mapping, and subscribing to asynchronous data streams.

#### Syntax of pipe():

```typescript
observable.pipe(
  operator1(),
  operator2(),
  ...
)
```

Each operator inside the pipe() is applied sequentially to the observable stream. The pipe() method returns a new observable, and it does not modify the original observable.

#### Common RxJS Operators Used with pipe():
Here are some of the commonly used operators when working with pipe():

1\. `map()`: Transforms the data emitted by the observable by applying a function to each emitted value.

Example:

```typescript
import { map } from 'rxjs/operators';

observable.pipe(
  map(value => value * 2)
).subscribe(console.log);
```

2\. `filter()`: Filters out values based on a condition.

Example:

```typescript
import { filter } from 'rxjs/operators';

observable.pipe(
  filter(value => value > 10)
).subscribe(console.log);
```

3\. `tap()`: Allows you to perform side effects (like logging or debugging) without modifying the emitted values.

Example:

```typescript
import { tap } from 'rxjs/operators';

observable.pipe(
  tap(value => console.log('Received value:', value))
).subscribe();
```

4\. `take()`: Completes the observable after emitting a certain number of values.

Example:

```typescript
import { take } from 'rxjs/operators';

observable.pipe(
  take(3)
).subscribe(console.log);
```

5\. `switchMap()`: Transforms the value emitted by the source observable into a new observable and unsubscribes from the previous one.

Example:

```typescript
import { switchMap } from 'rxjs/operators';

observable.pipe(
  switchMap(value => getNewObservable(value))
).subscribe(console.log);
```

6\. `catchError()`: Handles errors in the observable stream by allowing you to return a fallback value or handle the error.

Example:

```typescript
import { catchError } from 'rxjs/operators';
import { of } from 'rxjs';

observable.pipe(
  catchError(error => of('Error occurred!'))
).subscribe(console.log);
```

7\. `debounceTime()`: Delays the values emitted by the observable by a specified amount of time, useful for handling events like typing in a search box.

Example:

```typescript
import { debounceTime } from 'rxjs/operators';

observable.pipe(
  debounceTime(300)
).subscribe(console.log);
```

8\. `distinctUntilChanged()`: Only emits values if the current value is different from the last emitted value.

Example:

```typescript
import { distinctUntilChanged } from 'rxjs/operators';

observable.pipe(
  distinctUntilChanged()
).subscribe(console.log);
```

#### Example Using pipe():
Here is a more comprehensive example that chains multiple operators with the pipe() method:

```typescript
import { Observable } from 'rxjs';
import { map, filter, debounceTime, distinctUntilChanged } from 'rxjs/operators';

const observable = new Observable<string>((observer) => {
  observer.next('Apple');
  observer.next('Banana');
  observer.next('apple');
  observer.next('Banana');
  observer.next('Orange');
});

observable.pipe(
  debounceTime(300),
  map((value) => value.toLowerCase()), // Transform to lowercase
  filter((value) => value.startsWith('b')), // Filter values starting with 'b'
  distinctUntilChanged() // Avoid consecutive duplicate values
).subscribe(console.log);
```

Output:

```typescript
banana
```

In this example:

- debounceTime(300) waits for 300ms of inactivity before processing the values.
- map((value) => value.toLowerCase()) transforms each string to lowercase.
- filter((value) => value.startsWith('b')) only passes values starting with "b".
- distinctUntilChanged() filters out consecutive duplicate values (e.g., "Banana" is not emitted twice).

Or we can use `debounceTime` and `switchMap` to delay the search when user input.

#### Why Use `pipe()`?

The pipe() method is powerful because it provides a clean and functional approach to dealing with complex logic in an observable chain. Each operator has a single responsibility and can be composed together to create more advanced behaviors, making the code cleaner and more maintainable.

The use of pipe() also improves code readability, as it allows developers to process a stream of data step-by-step in a declarative style rather than using nested callback functions or imperative approaches.