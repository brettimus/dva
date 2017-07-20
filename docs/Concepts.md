# Concepts

[以中文版查看此文](./Concepts_zh-CN.md)

## Data Flow

dva borrows its ideas on [data flow](http://redux.js.org/docs/basics/DataFlow.html) from Redux.

<img src="https://zos.alipayobjects.com/rmsportal/PPrerEAKbIoDZYr.png" width="807" />

## Models

### State

```ts
type State = any
```

The state tree of your models is held inside the [store](http://redux.js.org/docs/basics/Store.html). Usually, the state tree is a javascript object; though, it can be of any type. It should be treated like it is immutable—you should never modify the state tree directly!

In dva, you can access your app's store with the `._store` property. You can access the state tree by calling `._store.getState()`.

```javascript
const app = dva();
// ...
console.log(app._store); // Logs your application state tree
console.log(app._store.getState()); // Logs the application state tree
```

### Action

```ts
type AsyncAction = any
```

Just like [Redux's actions](http://redux.js.org/docs/basics/Actions.html), an action in dva is a plain object that represents a proposed change to the application's state. In fact, actions are *the only way* to ensure that changes to data in the state tree propagated to the user interface. 

Any data you want to add to your store, whether it's from UI events, http requests, or other sources (e.g., WebSockets), needs to be dispatched inside an action and handled by a reducer.

Note that actions must have a `type` field, and may also contain additional data that reducers use to update the state tree. The convention in these docs is to put additional data inside a property called `payload`. 

> :cyclone: *If you're confused by the terms `dispatch` and `reducer`, read the following sections!*

```javascript
const optionalData = {};
# The `dispatch` function (described below) is how we tell the state tree that we want it to change
dispatch({
  type: 'add',
  payload: optionalData,
});
```

### dispatch function

```ts
type dispatch = (a: Action) => Action
```

The dispatch function accepts an action or a representation of an asynchronous action. It helps communicate to reducers (described below) that the state tree should be changed. 

So, 

The dispatch function is a function for triggering action, action is the only way to change state, but it just describes an action. while dispatch can be regarded as a way to trigger this action, and Reducer is to describe how to change state.

```javascript
const optionalData = {};
dispatch({
  type: 'user/add', // if dispatched outside of a model,
                    // the `type` field needs to include the model's namespace
  payload: optionalData,
});
```

### Reducer

`type Reducer<S, A> = (state: S, action: A) => S`

[Just like in Redux](http://redux.js.org/docs/basics/Reducers.html), a reducer (also called a reducing function) is a function that accepts the current state of the application and an action, then returns *a new copy* of the state of the application.

In dva, reducers *immutably* change a model's state. **Importantly**, each reducer must

  1. Be a [pure function](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/ch3.md)
  2. Treat its state argument as [immutable data](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/ch3.md#reasonable).

```js
// some-model.js
export default {
// ...
  reducers: {
    add(state, action) {
      return {
        ...state,
        count: action.payload.count,
      }
    }
  }
  // ...
}
```

#### Background

The term "reducer" comes from functional programming, where it is defined as a function that takes an accumulation and a value and returns a new accumulation. Reducers are typically are used to "reduce" a collection of values down to a single value. For example,

```javascript
// a collection of objects with different properties
const collection = [{ x: 1 }, { y: 2 }, { z: 3 }]
// a reducer function that talk all the proprties of objects in a collection,
// and combines them into a new object
const reducer = (accumulation, value) => Object.assign(accumulation, value)
const singleValue = collection.reduce(reducer, {} /* this is the initial value for `accumulation` */)
console.log(singleValue)
// => { x: 1, y: 2, z: 3 }
```

### Effect

dva uses [redux-saga](http://yelouafi.github.io/redux-saga/) to control asynchronous (or any potentially side-effecting) data flow, like network calls to an api. It is recommended that you read the [redux-saga documentation](https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html) to understand how to use effects. After that, read the [dva api docs on effects](https://github.com/dvajs/dva/blob/master/docs/API.md#effects).


#### Background

"Effect" is another term that comes from functional programming. (Learn more about effects in the [Mostly adequate guide to Functional Programming](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/ch3.md#side-effects-may-include).)

Side-effects make a function impure, meaning the same input may not result in the same output. Thus, side-effects make functions difficult to test. In dva applications, the most common type of side effect is an asynchronous operation.


### Subscription

Subscriptions are a way to set a model's state from a source that could emit data at any point of time. Essentially, they *listen* for data. (This concept comes from [Elm](https://guide.elm-lang.org/architecture/effects/time.html).)

These data sources may include, but are not limited to:

  * keyboard input
  * history router change
  * the current time
  * a websocket connection
  * geolocation change
  * etc...


```javascript
import key from 'keymaster';
//...
app.model({
  namespace: 'count',
  subscriptions: {
    keyEvent(dispatch) {
      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) });
    },
  }
});
```

## Router

Here, "router" means "frontend router." Because dva is designed with single page applications in mind, there is extra code required on the frontend to control changes to the URL and overall routing logic. Through the History API provided by the browser, we can change the url of the application without refreshing the page and, perhaps most importantly, without breaking the back button.

dva provides a `router` function that accepts a stateless router component. Under the hood, it uses [react-router version 2](https://github.com/ReactTraining/react-router/tree/v2.8.1).

```jsx
import { Router, Route } from 'dva/router';
app.router(({history}) => (
  <Router history={history}>
    <Route path="/" component={HomePage} />
  </Router>
);
```

### Route Components

dva makes it easy to use the [presentational and container components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) pattern common amongst React applications.

It's helpful to think of most of the app's container components as route components (that is, fed to a `Route` component. These route components are placed inside the `routes/` directory and passed to `Route`s in the main `Router` (see above). Accordinginly, the majority of components in the `components/` directory should be presentational, though this is not a strict rule.


## References
- [redux docs](http://redux.js.org/docs/Glossary.html)
- [Mostly adequate guide to FP](https://github.com/MostlyAdequate/mostly-adequate-guide)
- [choo docs](https://github.com/yoshuawuyts/choo)
- [elm](http://elm-lang.org/blog/farewell-to-frp)
