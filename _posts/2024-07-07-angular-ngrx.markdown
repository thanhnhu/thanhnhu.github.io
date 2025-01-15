---
layout: post
title:  "Angular ngRX"
date:   2024-07-07 08:40:00 +0700
categories: programming
permalink: /angular-ngrx/
---

`NgRx` is a reactive state management library for Angular applications, based on the principles of `Redux`. It leverages the power of RxJS (Reactive Extensions for JavaScript) to manage the state of the application in a predictable manner. NgRx provides a set of tools for handling state, side effects, and routing in Angular applications, and it works seamlessly with Angular’s dependency injection system.

#### Core Concepts of NgRx

1. **Store**: The central repository for the state in an Angular application. It holds the data and allows components to interact with that state in a predictable manner.

2. **Actions**: Actions are events or intentions that describe a change in the application state. They are dispatched to the store to initiate state changes.

3. **Reducers**: Reducers are functions that take the current state and an action and return a new state. They define how the state changes in response to an action.

4. **Selectors**: Selectors are used to query the state in the store. They are functions that allow you to retrieve a slice of the state.

5. **Effects**: Effects are used to interact with external systems (like APIs) and handle side effects. They listen for dispatched actions and perform asynchronous operations, then dispatch new actions to modify the store based on the results.

#### Key Features

- **Immutable State**: The state in NgRx is immutable, meaning once it’s set, it cannot be directly modified. Any change results in the creation of a new state object.
  
- **Single Source of Truth**: The store serves as the single source of truth for the application state, making it easier to manage, debug, and maintain.

- **Reactive Programming**: NgRx uses RxJS, making it easier to manage complex state transitions and side effects in a reactive way.

- **DevTools Support**: NgRx has built-in support for Redux DevTools, allowing you to inspect the state and actions in your application.

#### Basic Example

```typescript
// Define an action
export const loadItems = createAction('[Item List] Load Items');

// Define a reducer
export const itemReducer = createReducer(
  initialState,
  on(loadItems, state => ({ ...state, loading: true }))
);

// Define a selector
export const selectItems = createSelector(
  (state: AppState) => state.items,
  items => items
);

// Create an effect to interact with an API
@Injectable()
export class ItemEffects {
  loadItems$ = createEffect(() =>
    this.actions$.pipe(
      ofType(loadItems),
      mergeMap(() =>
        this.itemService.getItems().pipe(
          map(items => loadItemsSuccess({ items })),
          catchError(() => of(loadItemsFailure()))
        )
      )
    )
  );
}
```

#### Advantages of NgRx
- Predictable State: Because the state is stored in a central place and can only be modified through actions and reducers, it leads to more predictable behavior.

- Separation of Concerns: NgRx encourages keeping the side effects, business logic, and UI components separated, making the application easier to scale.

- Testing: NgRx makes it easier to test your components and services because of the clear separation of concerns and its reliance on observable streams.

#### Conclusion
NgRx is a powerful state management solution for Angular applications. It simplifies handling complex state changes, side effects, and interactions with external services. By leveraging RxJS and the Redux pattern, NgRx helps create scalable and maintainable applications.