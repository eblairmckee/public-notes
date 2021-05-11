# what is Redux?

Redux is a lightweight state management library that can be used in any javascript application. It's framework agnostic, to the point that you can even use a single redux configuration to manage state between multiple frameworks... ie: if you're converting an app from Angular -> React. Yeah, it's that cool.

But with all the awesomeness that is redux, is also comes with a bit of a learning curve. You'll need to invest some time to get a hang of the syntax and patterns. But once you learn it, you'll want to use it in every project, no matter how big or small.

According to Merriam Webster, "redux" means "brought back" or revived. Like a zombie...

Chew on that for a moment. Personally, I'm not a fan. In that sense, you're saying you're digging up past state, who knows how long ago it died (was deprecated or phase out), and you're resurfacing all its decomposed goodness.

Rather, let's think of redux as "preserved past state", where Redux makes copies of your reality (state) and preserves it in what are essentially time capsules. At any point in the future, you can dig up a time capsule and make that your new present, while still preserving the future and past states.

Pretty cool, right? That means you have easy access to functionality like undo, redo, or something even as complicated as version control.

## why you'll love Redux

But that's not all...you also get:

- single source of truth
  - all your state is held in one central location that exists independently of your components
- one-way data flow
  - state only flows from the store to consuming components. components cannot mutate state directly.
- no prop drilling (vs. hooks and `setState`)
- state is immutable (thanks to the [immer](https://github.com/immerjs/immer) library)
- rehydrate stateful components with minimal rerendering
  - decide what components you want to be consumers. makes separation of presentational and stateful components a breeze.
- dev tools include time travel

## some context

Where does Redux fit into the bigger picture?

In JavaScript Land, there isn't a native state management system. You need a library for it, and there are many options. Most of which use something akin to Redux where there is a single source of truth and state is passed down to consuming components... like MobX or overmind.

Since I'm a React dev, I'm going to focus on the state management options over in ReactLand --most of which come prebaked with the React library.

### basic af

`setState()` your most basic, [object oriented approach](https://reactjs.org/docs/state-and-lifecycle.html) to state management in React.

#### disadvantages:

- mutable

### context API

React ships with a higher order component (HOC) called a `<Provider>` that wraps your application and injects state using the [Context API](https://reactjs.org/docs/context.html).

```javascript
<Provider store={store}>
  <App />
</Provider>
```

#### benefits:

- no prop drilling
- declare once in a `<Provider>`
- pass state to all consuming components as props

### the FP option

Last year, we got a gift from the mighty React gods... a state management method that was purely functional -- `useState()` [hooks](https://reactjs.org/docs/hooks-intro.html)!

#### benefits:

- immutable
- fast
- isolated to the component where its declared
  - unless you pass state setters and state as props into other components

#### hooks + baby redux

Hooks paved the way for a simplified version of redux that combines the principles of redux with hooks into a [single hook](https://reactjs.org/docs/hooks-reference.html#usereducer) called `useReducer()`.

Say you don't want to prop drill with hooks, but you don't want to deal with the overhead of redux... you can configure a single `useReducer` hook and pass down the dispatch method to child components. That means you don't have to pass down each of your callbacks individually or pieces of state since they're all packaged in the dispatch method 🤯

Yep, it's 80% of redux without a fuck ton of work.

Under the hood, `useReducer` takes a reducer which contains all the actions (like a redux slice) and initial state. When you dispatch an action, it will pass that action payload and state into the reducer and output new state, rehydrating all components that are accepting dispatch as a prop.

## how do I know which state manager is right for me?

- need to share state across multiple views/routes?
- sick of prop drilling?
- tired of lifting state to the App level?

... if you answered yes to any of these questions... you should be using Redux.

### when you shouldn't

Redux is not the "end all be all". Don't use Redux:

- when you don't need it. save yourself the overhead.
- forms

If you're using forms in a Redux app, set state locally using hooks or the OG `setState()`, and dispatch the full form object to redux `onSubmit`.

# let's setup some redux

## buzz words

- `store`: where all your state is _stored_
- `dispatch`: how you tell the `store` to update state
- `actions` and action creators: the actual functions that will deliver a payload to the `store`, or change state
- `reducers`: combine previous state with the new, modified state

## how it works under the hood

Here's a typical redux configuration flow. Keep in mind, this is HIGHLY simplified and only to give you a gist of what is going on under the hood:

1. create your `store`
2. subscribe `<Component/>` to the `store` eg: `store.subscribe()`
3. or you can access state any where with `store.getState()`, but know that if you use this method, the component WILL NOT update if the `store` does.
4. dispatch objects to actions `store.dispatch()`
   actions exist outside of components, and can be reused, [separation of concerns](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) (presentation vs stateful UI)
5. `action`s will pass the payload to the `reducer`
6. which then returns a new, immutable state object
7. anything subscribed to the `store` will rehydrate and rerender with new state

# our state tree

> redux dev tools demo

- state tree
- diffing during flows
- time travel

# how to configure redux

we use [Redux Toolkit](https://redux-toolkit.js.org/)

- framework agnostic
- preconfigured `store`
- `reselect` is baked in

Instead of `reducers` and `actions`, you use `createSlice` which does all the heavy lifting for you. Here's an [example](https://codesandbox.io/s/rtk-convert-todos-example-uqqy3?from-embed=&file=/src/index.js).

## createStore

Store is preconfigured with:

- dev tools
- middleware
  - allows for thunks (async store updates)

Includes a utility called 'combineReducers' that combines your reducers into a single reducer that's passed into the `store`. In this example, say we're building an app for a dog store that stocks treats and toys. We want to be able to manage these items in our `store` using their own reducers.

```javascript
import { configureStore } from "@reduxjs/toolkit";
import { reducer as treatsReducer } from "./reducers/treats";
import { reducer as toysReducer } from "./reducers/toys";

const rootReducer = combineReducers({
  treats: treatsReducer,
  toys: toysReducer,
});

// Infer the `RootState` and `AppDispatch` types from the store itself
// this is from the official redux toolkit docs
// https://redux-toolkit.js.org/tutorials/typescript
export type RootState = ReturnType<typeof store.getState>;
// this is useful later when you want to dispatch any action, this types it for you
export type AppDispatch = typeof store.dispatch;

const store = configureStore({
  reducer: rootReducer,
});

export default store;
```

You'll then need to inject your `store` into your application at the highest level (usually an `<App/>` component)

React ships with a higher order component (HOC) called a `<Provider>` that wraps your application and injects state using the [Context API](https://reactjs.org/docs/context.html).

```javascript
<Provider store={store}>
  <App />
</Provider>
```

## benefits:

- no prop drilling
- declare once in a `<Provider>`
- pass state to all consuming components as props

## createSlice

This is your `reducer`, `actions`, and action creators all in one function.

```javascript
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

const treatsSlice = createSlice({
  name: "treats",
  initialState: ["peanut butter", "cheese"],
  reducers: {
    addTreat: (state, action: PayloadAction) => [...state, action.payload],
    takeTreat: (state, action: PayloadAction) => {
      return state.filter((treat) => treat !== action.payload);
    },
  },
});

export const { reducer, actions } = treatsSlice;
export const { addTreat, takeTreat } = actions;
```

Destructure your `reducer` and `actions`... then import your `reducer` into the `rootReducer` so your `store` is aware of it.

## expose to your store

Use the `combineReducers()` utility which will create a single `reducer` that you can pass to your `store`. This will generate your state tree and expose your application to redux state and manipulation methods. You'll still be able to manipulate individual parts of state (specific to each `reducer`) but only have to inject it once into the app.

# how to make your app stateful

## some useful hooks

These hooks are recommended by the docs and allow you to automagically type your action dispatches and state selections. They also make it easier to perform simple redux mutations or read from the store without having to "hook up" your component.

```javascript
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';

import type { AppDispatch, RootState } from '../index';

// Typescript helpers for redux. Use throughout your app instead of plain `useDispatch` and `useSelector`
// this basically makes it easier to pass actions into your components. removes the need for mapDispatchToProps
export const useAppDispatch = () => useDispatch<AppDispatch>();
// this way you don't have to type state:RootState every time
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

```javascript
const MyComponent: React.FC = () => {
  const dispatch = useAppDispatch();
  const stateItem = useAppSelector(state.something);

  return <Button onClick={() => dispatch(action(payload))} />;
};
```

## connect individual components

Use the `react-redux` library to subscribe components to the `store`. First, you'll need to wrap your app with a higher order `<Provider>` component that uses the `Context` API to inject the `store` into the app (sounds familiar, right?). This exposes your `store` to all consuming components.

How do you make a component a consumer? React-redux ships with a `connect` call that will subscribe the component to the `store`. You can then map state as `props` and pass them into the component. Same for dispatch `actions`.

`connect` takes two arguments:

- `mapStateToProps` (a function)
- `mapDispatchToProps` (an object)

Which respectfully injects your state and `dispatch` actions as `props`. When you want to dispatch an action, you can either pass the action in as the second argument to the connect call, or you can use the `useDispatch` hook that comes with the `react-redux` library and call your action as the argument, and pass a payload, eg: `useDispatch(addTreat('carrots'))`.

```javascript
import { connect, useDispatch } from "react-redux";
import { addTreat } from "./reducers/treats";

// you can add your own state interfaces if you want, but this is way easier
interface Props extends StateProps {
  addTreat: React.Dispatch<string>;
}

// pass in state and actions as props
const MyComponent: React.FC<Props> = ({ treats, addTreat }) => {
  // form state is stored in a hook
  const [newTreat, setNewTreat] = useState < string > "";
  // option 1
  const dispatch = useDispatch();
  return (
    <>
      <input
        value={newTreat}
        onChange={(e) => setNewTreat(e.currentTarget.value)}
      />
      <button onClick={() => dispatch(addTreat(newTreat))}>Add a treat</button>
    </>
  );
};

const mapStateToProps = (state: RootState) => ({
  treats: state.treats,
});

// this automagically types your injected state
type StateProps = ReturnType<typeof mapStateToProps>;

// option 2
// you really don't have to make a const for this, instead just pass in addTreat as the second arg... but 🤷‍♀️
const mapDispatchToProps = {
  addTreat,
};

export default connect(mapStateToProps, mapDispatchToProps)(MyComponent);
```

# selectors

`selectors` allow you to transform state before passing it into a component.

> Filter example

Only parent is stateful, pass in transformed state using selectors to improve performance, and then pass transformed state to a stateless child which renders the data.

# best practices

## subscription vs store.getState()

I often see devs using `store.getState()` to access different parts of state, this is really common in AngularJS components in chopshop UI. This is a bad practice and should be avoided, because `store.getState()` does not subscribe that component to state updates.

## separation of concerns

Don't just hook up everything with `connect`... Keep your stateful and presentational components separated to prevent unnecessary rerenders. Remember, you only want a component to rerender if state changes. So only hook up components that need to be aware of state ([read this](https://dottedsquirrel.com/react/rethinking-soc-with-react/)). Same for changing state... only hook up actions to components if they need to directly modify state.

## immutability

There are ways to directly mutate state (even with immer)... ✋🏼🛑 but don't do it. Instead, copy state with the es6 spread `...` operator or `Object.assign()`.

# architecture

Here's how I structure my redux apps:

- src
  - store
    - index.ts `createStore()`
    - reducers.ts `combineReducers()`
  - feature
    - example
      - reducers
        - index.ts `createSlice()`
        - index.test.ts
        - types.ts
      - selectors
        - index.ts `createSelector()`

# redux dev tools

> time travel demo

# testing

Always make sure you write tests that expect an intended behavior, rather than testing the store directly.

test at the highest level. that means if you have a bunch of presentational components that receive state as `props`, you need to test their behavior at the parent level (the subscribed component).

> Table and filter example

## how to mock the store

### reexport RTL

```javascript
function render(ui: any, { initialState = {}, store, ...renderOptions }: any = {}) {
    const mockStore = store ? store : createStore(rootReducer, initialState, applyMiddleware(thunk));
    function Wrapper({ children }: any) {
        return <Provider store={mockStore}>{children}</Provider>;
    }
    return rtlRender(ui, { wrapper: Wrapper, ...renderOptions });
}

// use it like
const {getByText} = render(<MenuList {...props} />, {
    initialState: menu: {
            pizza: ['cheese', 'vegan sausage', 'basil', 'truffle oil'],
        }
    });
```

### mock with a provider and fake store

```javascript
const setupWrapperComponent = (props) => {
  const mockStore = configureStore();
  const store = mockStore({
    menu: {
      pizza: ["cheese", "vegan sausage", "basil", "truffle oil"],
    },
  });
  return mount(
    <Provider store={store}>
      <MenuList {...props} />
    </Provider>
  );
};

// use it like
const { getByText } = setupWrapperComponent();
```

### reducers

Test reducers _directly_ by passing in the `store` as first arg, `action` and `payload` as second.

```javascript
import { reducer as treatsReducer, addTreat } from "./";

const initialState = ["blueberries", "canteloupe"];

describe("treat example", () => {
  it("adds a treat", () => {
    expect(
      treatsReducer(initialState, {
        type: addTreat.type,
        payload: "carrots",
      })
    ).toEqual([...initialState, "carrots"]);
  });
});
```

# persisted state

we are using `redux-persist` to cache our store in `localStorage`

that means, whenever you pull from master, or fire up the API locally, you'll want to clear your cache or use incognito

```javascript
import { persistReducer, persistStore } from "redux-persist";
import storage from "redux-persist/lib/storage";
import rootReducer from "./reducers";
import { configureStore } from "@reduxjs/toolkit";

const persistConfig = {
  key: "root",
  storage,
};

// this allows us to store the redux store in localStorage
// so we can persist preferences across browser sessions
// https://github.com/rt2zz/redux-persist#basic-usage
const persistedReducer = persistReducer(persistConfig, rootReducer);

const store: RootState = configureStore({
  reducer: persistedReducer,
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

// this is used in the <PersistGate> provider that delays the UI from rendering
// until localStorage values are retrieved and stored in redux
export const persistor = persistStore(store);

export default store;
```

# async redux

by default, redux is synchronous. the only way to do async calls, is to inject `thunk` middle ware

## thunks

a `thunk` is a function that wraps an action/dispatch
it delays the execution of the inner function until a `Promise` has been met
that means when you `dispatch` an action that’s a thunk:
you’re really passing a function/promise to the `store`
which is run by middleware
which then takes an action based on how the promise is resolved

## side effects

write your services as promises (we're using `axios`)
and have them update state using a `thunk`

```javascript
import { createAsyncThunk } from "@reduxjs/toolkit";
import {
  getBaseCarePathListings,
  getPayerCarePathListings,
} from "../../../services/carePathService";
import { payers } from "../../../utils/constants";

export const fetchCarePathListings = createAsyncThunk(
  "fetchCarePathListings",
  (payer: payers, thunkApi) => {
    const listings =
      payers.Base === payer
        ? getBaseCarePathListings()
        : getPayerCarePathListings(payer);
    return listings.catch(() => {
      return thunkApi.rejectWithValue(false);
    });
  }
);
```

which is really a `dispatch` of sorts. you'll need to set up a subscribing `reducer` as an `extraReducer` like this:

```javascript
extraReducers: {
        [fetchCarePathListings.fulfilled.type]: (state, action: PayloadAction<CarePath[]>) => {
            const fetchState = action.payload.reduce((map: CarePaths, obj: CarePath) => {
                map[obj.carePathId] = obj;
                return map;
            }, {});
            return fetchState;
        }
    }
```

which will update your state if the promise is fulfilled

# Demo time
