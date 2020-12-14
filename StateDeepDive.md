# redux recap

## buzz words

- store: where all your state is _stored_
- dispatch: how you tell the store to update state
- actions and action creators: the actual functions that will deliver a payload to the store, or change state
- reducers: combine previous state with the new, modified state

## how it works under the hood

Here's a typical redux configuration flow. Keep in mind, this is HIGHLY simplified and only to give you a gist of what is going on under the hood:

1. create your `store`
2. subscribe component to the `store` `store.subscribe()`
3. or you can access state any where with `store.getState()`, but know that if you use this method, the component WILL NOT update if the store does.
4. dispatch objects to actions `store.dispatch()`
   actions exist outside of components, and can be reused, [separation of concerns](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) (presentation vs stateful UI)
5. actions will pass the payload to the reducer
6. which then returns a new, immutable state object
7. anything subscribed to the store will rehydrate and rerender with new state

# our state tree

> redux dev tools demo

- state tree
- diffing during flows
- time travel

# how to configure redux

we use [Redux Toolkit](https://redux-toolkit.js.org/)

- framework agnostic
- preconfigured store
- reselect is baked in

Instead of reducers and actions, you use `createSlice` which does all the heavy lifting for you. Here's an [example](https://codesandbox.io/s/rtk-convert-todos-example-uqqy3?from-embed=&file=/src/index.js).

## createStore

Store is preconfigured with:

- dev tools
- middleware
  - allows for thunks (async store updates)

Includes a utility called 'combineReducers' that combines your reducers into a single reducer that's passed into the store. In this example, say we're building an app for a dog store that stocks treats and toys. We want to be able to manage these items in our store using their own reducers.

```javascript
import { configureStore } from "@reduxjs/toolkit";
import { reducer as treatsReducer } from "./reducers/treats";
import { reducer as toysReducer } from "./reducers/toys";

const rootReducer = combineReducers({
  treats: treatsReducer,
  toys: toysReducer,
});

// this 🧙‍♂️ line of code generates a type interface for your store
export type RootState = ReturnType<typeof rootReducer>;

const store = configureStore({
  reducer: rootReducer,
});

export default store;
```

You'll then need to inject your store into your application at the highest level (usually an `<App/>` component)

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

This is your reducer, actions, and action creators all in one function.

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

Destructure your reducer and actions... then import your reducer into the root reducer so your store is aware of it.

## expose to your store

Use the `combineReducers()` utility which will create a single reducer that you can pass to your store. This will generate your state tree and expose your application to redux state and manipulation methods. You'll still be able to manipulate individual parts of state (specific to each reducer) but only have to inject it once into the app.

# how to make your app stateful

## connect individual components

Use the `react-redux` library to subscribe components to the store. First, you'll need to wrap your app with a higher order `<Provider>` component that uses the `Context` API to inject the store into the app (sounds familiar, right?). This exposes your store to all consuming components.

How do you make a component a consumer? React-redux ships with a `connect` call that will subscribe the component to the store. You can them map state as props and pass them into the component. Same for dispatch actions.

`connect` takes two arguments:

- `mapStateToProps` (a function)
- `mapDispatchToProps` (an object)

Which respectfully injects your state and `dispatch` actions as `props`. When you want to dispatch an action, you can either pass the action in as the second argument to the connect call, or you can use the `useDispatch` hook that comes with the `react-redux` library and call your action as the argument, and pass a payload, eg: `useDispatch(addTreat('carrots'))`.

```javascript
import { connect, useDispatch } from "react-redux";
import { addTreat } from "./reducers/treats";

interface Props {
  treats: string[];
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

// option 2
// you really don't have to make a const for this, instead just pass in addTreat as the second arg... but 🤷‍♀️
const mapDispatchToProps = {
  addTreat,
};

export default connect(mapStateToProps, mapDispatchToProps)(MyComponent);
```

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

time travel

# testing

Always make sure you write tests that expect an intended behavior, rather than testing the store directly.

test at the highest level. that means if you have a bunch of presentational components that receive state as props, you need to test their behavior at the parent level (the subscribed component).

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

test directly
Test reducers by passing in the store as first arg, action and payload as second.

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

which is really a dispatch of sorts. you'll need to set up a subscribing reducer as an `extraReducer` like this:

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

# local state

## useState

two years ago, we got a gift from the mighty React gods... a state management method that was purely functional -- `useState()` [hooks](https://reactjs.org/docs/hooks-intro.html)!

#### benefits:

- immutable
- fast
- isolated to the component where its declare
  - unless you pass state setters and state as props into other components

## useReducer

Hooks paved the way for a simplified version of redux that combines the principles of redux with hooks into a [single hook](https://reactjs.org/docs/hooks-reference.html#usereducer) called `useReducer()`.

Say you don't want to prop drill with hooks, but you don't want to deal with the overhead of redux... you can configure a single `useReducer` hook and pass down the dispatch method to child components. That means you don't have to pass down each of your callbacks individually or pieces of state since they're all packaged in the dispatch method 🤯

Yep, it's 80% of redux without a fuck ton of work.

Under the hood, `useReducer` takes a reducer which contains all the actions (like a redux slice) and initial state. When you dispatch an action, it will pass that action payload and state into the reducer and output new state, rehydrating all components that are accepting dispatch as a prop.

# how do I know which state manager is right for me?

- need to share state across multiple views/routes?
- sick of prop drilling?
- tired of lifting state to the App level?

... if you answered yes to any of these questions... you should be using Redux.

## when you shouldn't

Redux is not the "end all be all". Don't use Redux:

- when you don't need it. save yourself the overhead.
- forms

If you're using forms in a Redux app, set state locally using hooks or the OG `setState()`, and dispatch the full form object to redux `onSubmit`.

# demo time

> all state management techniques in one component (add/edit care path form)
> how to create a new stateful component
> how to test
