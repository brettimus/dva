# API

[以中文版查看此文](./API_zh-CN.md)

## Exported Modules
### dva

Default export. A factory function for creating dva apps.

### dva/mobile

`dva/mobile` is `dva` without the router, which should be used in multiple-page or react-native apps.

### dva/router

Exports the api of [react-router@2.x](https://github.com/ReactTraining/react-router/tree/v2.8.1), and also exports [react-router-redux](https://github.com/reactjs/react-router-redux) with the `routerRedux` key.

E.g.,

```js
import { Router, Route, routerRedux } from 'dva/router';
```

### dva/fetch

Exports the api of [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch), which can be used for async network requests. This is included solely for convenience. (That is, you can choose to use whatever request library you want.)

### dva/saga

Exports the api of [redux-saga](https://github.com/yelouafi/redux-saga).

***

## The dva API
### `app = dva(opts)`

Create an app and return dva instance. (Note: dva supports multiple instances.)

`opts` includes:

* `history`: Specify the history for router (default `hashHistory`).
* `initialState`: Specify the initial state (default `{}`). Its priority is higher than model state

E.g., to use `browserHistory`:

```js
import { browserHistory } from 'dva/router';
const app = dva({
  history: browserHistory,
});
```

Additionally, for convenience, `opts` also accepts [hooks](#appusehooks):

```js
const app = dva({
  history,
  initialState,
  onError,
  onAction,
  onStateChange,
  onReducer,
  onEffect,
  onHmr,
  extraReducers,
  extraEnhancers,
});
```

### `app.use(hooks)`

Specify hooks or register a plugin.

E.g., to register the [dva-loading](https://github.com/dvajs/dva-loading) plugin:

```js
import createLoading from 'dva-loading';
// ...
app.use(createLoading(opts));
```

**Possible `hooks` include the following** :

#### `onError(fn, dispatch)`

Triggered when an `effect` throws an unhandled error or a `subscription` passes an error to its `done` callback. Useful for capturing and handling unhandled errors from effects, though note that for subscriptions, errors must be explicitly passed as an argument to `done`. E.g.,

```js
app.model({
  subscriptions: {
    setup({ dispatch }, done) {
      done(e);
    },
  },
});
```

If the app were to use the component library [antd](http://ant.design/), a simple global error handler for uncaught exceptions might look like this:

```js
import { message } from 'antd';
const app = dva({
  onError(e) {
    const duration = 3;
    message.error(e.message, duration);
  },
});
```

#### `onAction(fn | fn[])`

Triggered when an action is dispatched. Useful for registering additional redux middleware.

E.g., to log actions with [redux-logger](https://github.com/evgenyrodionov/redux-logger):

```js
import { createLogger } from 'redux-logger';
const app = dva({
  onAction: createLogger(opts),
});
```

#### `onStateChange(fn)`

Triggered when `state` changes. Useful for syncing `state` to localStorage, server, etc.

#### `onReducer(fn)`

Wrap execution of reducers

E.g. to implement redo/undo using [redux-undo](https://github.com/omnidan/redux-undo) :

```js
import undoable from 'redux-undo';
const app = dva({
  onReducer: reducer => {
    return (state, action) => {
      const undoOpts = {};
      const newState = undoable(reducer, undoOpts)(state, action);
      // restore `routing` because dva syncs routing data via `react-router-redux`
      return { ...newState, routing: newState.present.routing };
    },
  },
});
```

View [examples/count-undo](https://github.com/dvajs/dva/blob/master/examples/count-undo/index.js) for more details.

#### `onEffect(fn)`

Wrap execution of effects

E.g. [dva-loading](https://github.com/dvajs/dva-loading) has implemented auto loading states with this hook.

#### `onHmr(fn)`

Related to HMR (Hot Module Replacement), currently used in [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr).

#### `extraReducers`

Adds extra reducers to the redux store.

E.g., the library [redux-form](https://github.com/erikras/redux-form) needs an extra `form` reducer:

```js
import { reducer as formReducer } from 'redux-form'
const app = dva({
  extraReducers: {
    form: formReducer,
  },
});
```

#### `extraEnhancers`

Adds an extra [StoreEnhancer](https://github.com/reactjs/redux/blob/master/docs/Glossary.md#store-enhancer)s.

E.g., to use dva with [redux-persist](https://github.com/rt2zz/redux-persist):

```js
import { persistStore, autoRehydrate } from 'redux-persist';
const app = dva({
  extraEnhancers: [autoRehydrate()],
});
persistStore(app._store);
```

### `app.model(model)`

Registers a model, see [#Model](#model) for more info on models.

### `app.unmodel(namespace)`

Unregisters a model, see [#Model](#model) for more info on models.

### `app.router(({ history, app }) => <RouterConfig history={history} />)`

Registers a router configuration.

E.g.,

```jsx
import { Router, Route } from 'dva/router';
app.router(({ history }) => {
  return (
    <Router history={history}>
      <Route path="/" component={App} />
    <Router>
  );
});
```

It is recommended to use a separate file to configure the router. Then, it is possible to do hmr with [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr). E.g.,

```js
app.router(require('./router'));
```

If the app doesn't require a router, like a react-native or multiple-page application, pass in a function that simply returns the root JSX Element. E.g.,

```jsx
app.router(() => <App />);
```

### `app.start(selector?)`

Starts the application, mounting to the DOM element that first matches `selector`. 

```js
app.start('#root');
```

`selector` is optional, however, and if there is no `selector`, `app.start` will return a higher-order component (HOC), i.e., a function which returns JSX element.

This is useful, for example, to implement internationalization with react-intl:

```jsx
import { IntlProvider } from 'react-intl';
//...
const App = app.start();
ReactDOM.render(<IntlProvider><App /></IntlProvider>, htmlElement);
```

***

## Model
model is the most important concept to grasp in dva.

E.g.

```js
app.model({
  namespace: 'todo',
	state: [],
  reducers: {
    add(state, { payload: todo }) {
      // Save data to state
      return [...state, todo];
    },
  },
  effects: {
    *save({ payload: todo }, { put, call }) {
      // Call saveTodoToServer, then trigger `add` action to save data
      yield call(saveTodoToServer, todo);
      yield put({ type: 'add', payload: todo });
    },
  },
  subscriptions: {
    setup({ history, dispatch }) {
      // Subscribe history(url) change, trigger `load` action if pathname is `/`
      return history.listen(({ pathname }) => {
        if (pathname === '/') {
          dispatch({ type: 'load' });
        }
      });
    },
  },
});
```

model includes 5 properties:

### namespace

model's namespace.

### state

models's initial state, it's priority is lower then `opts.initialState` in `dva()`.

e.g.

```
const app = dva({
  initialState: { count: 1 },
});
app.model({
  namespace: 'count',
  state: 0,
});
```

Then, state.count is 1 after `app.start()`.

### reducers

Store reducers in key/value Object. reducer is the only place to modify `state`. Triggered by `action`.

`(state, action) => newState` or `[(state, action) => newState, enhancer]`

View https://github.com/dvajs/dva/blob/master/test/reducers-test.js for details.

### effects

Store effects in key/value Object. Used for do async operations and biz logic, don't modify `state` directly. Triggered by `action`, could trigger new `action`, communicate with server, select data from global `state` and so on.

`*(action, effects) => void` or `[*(action, effects) => void, { type }]`。

type includes:

* `takeEvery`
* `takeLatest`
* `throttle`
* `watcher`

View https://github.com/dvajs/dva/blob/master/test/effects-test.js for details.

### subscriptions

Store subscriptions in key/value Object. Subscription is used for subscribing data source, then trigger action by need. It's executed when `app.start()`.

`({ dispatch, history }, done) => unlistenFunction`

Notice: if we want to unregister a model with `app.unmodel()`, it's subscriptions must return unsubscribe method.
