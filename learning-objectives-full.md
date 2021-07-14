# Week 15 Learning Objectives

1.  Recognize a React Class Component

    A Class component is defined with the keyword `class` it extends the
    `Component` class and always has a `render`.

    ```js
    import React from 'react';

    class ClassComponent extends React.Component {
      render() {
        return <div>Class Component</div>;
      }
    }
    ```

2.  Convert the use of component props in a Class Component to a Function
    Component

    ```js
    import React from 'react';

    class ClassComponent extends React.Component {
      render() {
        return (
          <div>
            <h1>{this.props.title}</h1>
          </div>
        );
      }
    }
    ```

    ```js
    function FunctionComponent(props) {
      return (
        <div>
          <h1>{props.title}</h1>
        </div>
      );
    }
    ```

3.  Convert the use of component state in a Class Component to a Function
    Component using useState

    ```js
    import React from 'react';

    class ClassComponent extends React.Component {
      constructor(props) {
        super(props); // must be called if creating a constructor method

        // Initialize the component state object
        this.state = {
          count: 0
        };
      }

      render() {
        return (
          <>
            <h1>{this.props.title}</h1>
            <div>{count}</div>
            <button
              onClick={() =>
                this.setState((state) => ({ count: state.count + 1 }))
              }
            >
              Increment
            </button>
          </>
        );
      }
    }
    ```

    ```js
    import { useState } from 'react';

    function FunctionComponent({ title }) {
      const [count, setCount] = useState(0);

      return (
        <>
          <h1>{title}</h1>
          <div>{count}</div>
          <button onClick={() => setCount(count + 1)}>Increment</button>
        </>
      );
    }
    ```

4.  Understand what lifecycle methods are, the types of lifecycle methods, and
    when they are called.

    Lifecycle methods of a Class Component are methods that will be invoked after
    the rendering of a component. There are three types of lifecycle methods.
    `componentDidMount` will only run once, after the component's first render.
    `componentDidUpdate` will run after every render that isn't the first render.
    `componentWillUnmount` will run right before the component is removed from
    the component tree. The `useEffect` hook can be used to imitate the behavior
    of the lifecycle methods for a Class Component.

5.  Convert the use of lifecycle methods in a Class Component to a Function Component using useEffect

    ```js
    import React from 'react';

    class ClassComponent extends React.Component {
      constructor(props) {
        super(props); // must be called if creating a constructor method

        // Initialize the component state object
        this.state = {
          count: 0
        };
      }

      componentDidMount() {
        setTimeout(() => {
          this.setState({ count: 0 });
        }, 1000);
      }

      componentDidUpdate(prevProps, prevState) {
        if (prevState.count !== this.state.count) {
          console.log('hello world!');
        }
      }

      componentWillUnmount() {
        console.log('cleanup');
      }

      render() {
        return (
          <>
            <div>{count}</div>
            <button
              onClick={() =>
                this.setState((state) => ({ count: state.count + 1 }))
              }
            >
              Increment
            </button>
          </>
        );
      }
    }
    ```

    ```js
    import { useState, useEffect } from 'react';

    function FunctionComponent({ title }) {
      const [count, setCount] = useState(0);

      useEffect(() => {
        setTimeout(() => {
          setCount(0);
        }, 1000);
        return () => console.log('cleanup');
      }, []);

      useEffect(() => {
        console.log('hello world!');
      }, [count]);

      return (
        <>
          <h1>{title}</h1>
          <div>{count}</div>
          <button onClick={() => setCount(count + 1)}>Increment</button>
        </>
      );
    }
    ```

6.  Describe the Redux data cycle.

    An `action` object is dispatched to the reducer using an `action creator`
    function. That action is required to have a type key/value pair and an
    optional payload. Based on that actions type, the reducer will decide how
    state should be updated inside the `store` and then pass it to any component
    that has subscribed to it.

7.  Evaluate when it's appropriate to use Redux.

    - You have large amounts of application state that are needed in many places in the app
    - The app state is updated frequently over time
    - The logic to update that state may be complex
    - The app has a medium or large-sized codebase, and might be worked on by
      many people

8.  Configure a React application to use Redux.

    `/store/index.js`

    ```js
    import {
      createStore,
      applyMiddleware,
      compose,
      combineReducers
    } from 'redux';
    import articleReducer from './articleReducer';
    import fruitReducer from './fruitReducer';
    /* combineReducers turns all the reducer functions into one big reducer function
     */
    /* this is the most important part of this file. you will add your reducers here
    to work with your components. you are creating one big reducer */
    const rootReducer = combineReducers({
      //add reducer
      // this key should be named article
      articleState: articleReducer,
      fruitState: fruitReducer
    });

    let enhancer;

    /* enhancer allows you to alter the store and add functionality such as 
    redux devtools, logger (similar to morgan) middleware */

    /* compose gives you the ability to add more than one enhancer to the store */
    /* env comes automatically in create-react-app */
    /* process.env.NODE_ENV has 3 settings:
    1. running npm start makes process.env.NODE_ENV ='development`
    2. running npm test makes process.env.NODE_ENV = 'test'
    3. running npm run build makes process.env.NODE_ENV = 'production'
    which you will use with heroku */
    /* window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({ trace: true }) along with
     the chrome extension for redux devtools will set up your Redux DevTools in the browser */
    if (process.env.NODE_ENV !== 'production') {
      const logger = require('redux-logger').default;
      const composeEnhancers =
        window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({ trace: true }) || compose;
      enhancer = composeEnhancers(applyMiddleware(logger));
    }

    /* createStore creates a store object literal {} */
    /* preloaded state, not important for now, mainly used for hydrating state from the server */
    /* enhancer see above */

    /*this is the variable you will use in your root index.js to give Redux store access to the full application */
    const configureStore = (preloadedState) => {
      return createStore(rootReducer, preloadedState, enhancer);
    };

    export default configureStore;
    ```

    Root `index.js`

    ```js
    import React from 'react';
    import ReactDOM from 'react-dom';
    import { BrowserRouter } from 'react-router-dom';
    import { Provider } from 'react-redux';
    import App from './App';
    import configureStore from './store';
    import './index.css';

    const store = configureStore();

    ReactDOM.render(
      <React.StrictMode>
        <Provider store={store}>
          <BrowserRouter>
            <App />
          </BrowserRouter>
        </Provider>
      </React.StrictMode>,
      document.getElementById('root')
    );
    ```

9.  Configure Redux to use the browser extension for Redux development tools.

    See LO #8

10. Change the Redux store state using reducers and actions.

    When an action is dispatched, any new state data must be passed along as the payload. However, when these action payloads are generated dynamically, it becomes necessary to extrapolate the creation of the action object into a function. These functions are called action creators. The JavaScript objects they return are the actions.

    For example, an action creator function to create 'ADD_FRUIT' actions looks like this:

    ```js
    const addFruit = (fruit) => {
      return {
        type: 'ADD_FRUIT',
        fruit
      };
    };
    ```

    You can also rewrite the above arrow function to use an implicit return value:

    ```js
    const addFruit = (fruit) => ({
      type: 'ADD_FRUIT',
      fruit
    });
    ```

    Take a look at this example of a reducer function below that adds a fruit.

    ```js
    const fruitReducer = (state = [], action) => {
      switch (action.type) {
        case 'ADD_FRUIT':
          return [...state, action.fruit];
        default:
          return state;
      }
    };
    ```

11. Use composed reducers to organize state management into smaller pieces.

    Splitting up the reducer into multiple reducers handling separate, independent slices of state is called reducer composition, and it's the fundamental pattern of building Redux apps. Because each reducer only handles a single slice of state, its state parameter corresponds only to the part of the state that it manages and it only responds to actions that concern that slice of state.

    To split up the Redux store state for the fruits example with farmers, you can split the root reducer into two reducers:

    fruitReducer - A reducing function that handles actions updating the fruits
    slice of state farmersReducer - A reducing function that handles actions
    updating the new farmers slice of state.

12. Configure a React component to subscribe to changes in the Redux store.

    The `useSelector` hook accepts a function as an argument that will accept the current or updated state of the Redux store. The return value of that function is what is returned from the useSelector hook.

    Here's an example of the `useSelector` hook being used in a React function component:

    ```js
    import { useSelector } from 'react-redux';

    function FruitsList() {
      const fruits = useSelector((state) => state.fruits);

      return (
        <ul>
          {fruits.map((fruit) => (
            <li>{fruit}</li>
          ))}
        </ul>
      );
    }
    ```

    The function passed into the `useSelector` hook will be subscribed to the
    store. Whenever the state gets updated, the function will be invoked with
    the updated state. If the return value of the function is different from the
    return value from the previous state, the React function component that used
    the `useSelector` hook will re-render.

13. Change Redux state in components using hooks.

        Remember, to update the Redux store, you must dispatch an action.
        Dispatching an action may occur on a user input like a button click, as seen
        in the following example.

        ```js
        import { useDispatch } from 'react-redux';

        function AddMelonButton() {
            const dispatch = useDispatch();

            return <button onClick={() => dispatch(addFruit('melon'))}>Melon<button>;

    }

        ```

    Or you can dispatch an action in a useEffect, like so:

    ```js
    import { useEffect } from 'react';
    import { useSelector, useDispatch } from 'react-redux';

    function FruitsList() {
      const dispatch = useDispatch();
      const fruits = useSelector((state) => state.fruits);

      useEffect(() => {
        dispatch(populateFruits());
      }, [dispatch]);

      return (
        <ul>
          {fruits.map((fruit) => (
            <li>{fruit}</li>
          ))}
        </ul>
      );
    }
    ```

14. Define a 'slice of state'.

    Splitting up the reducer into multiple reducers handling separate,
    independent slices of state is called reducer composition, and it's the
    fundamental pattern of building Redux apps. Because each reducer only
    handles a single slice of state, its state parameter corresponds only to the
    part of the state that it manages and it only responds to actions that
    concern that slice of state.

15. Debug Redux by using browser window object.
    You have the ability to place your store and your action creators on the
    window object:

    `/src/index.js`

    ```js
        import \* as sessionActions from './store/session';

        const store = configureStore();

        if (process.env.NODE_ENV !== 'production') {
            window.store = store;
            window.sessionActions = sessionActions;
        }
    ```

    After you have placed the store and session on the window, you can then test
    your action creators and Redux methods in your console.

16. Use debugging tools to determine when an action is called and a reducer
    updates state.

    You can use Redux DevTools & Redux Logger as well as the window object to
    determine state updates and action calls.

17. Describe why reducers return a new reference in memory to update the state.

    If the state needs to be changed, the reducer must return a new object. To avoid mutating the state, a copy of the state is first created then mutated.

18. Compare and contrast an action and an action creator.

    **Action** An action is a POJO (plain old JavaScript object) with a type
    property. Actions contain information that can be used to update the store.
    They can be dispatched, i.e. sent to the store, in response to user actions
    or AJAX requests. Typically Redux apps use functions called action creators
    that return actions. Action creators can take arguments which allow them to
    customize the data contained in the actions they generate.

    **Action Creator**
    An action creator is a function that returns an action object.

    Either could be used in components, however, it is easier for debugging,
    more concise and preferable to use action creators inside comonents.

19. Use constants to define action types to prevent simple typos.

    Instead of using string literals as the type property for an action, use a constant to avoid typos. The store's reducer is checking the action's type for a match. If you use the same constant as the value for the action's type and the case in the reducer, then that will ensure that they match.

    Creating constants for the action type string literals ensures that an error
    is thrown if the constant name is mistyped anywhere that it is used. You
    could make this string anything, it just needs to be unique within your
    application.

    ```js
    const ADD_FRUIT = 'groceryApp/fruit/ADD_FRUIT';

    const addFruit = (fruit) => {
      return {
        type: ADD_FRUIT,
        fruit
      };
    };
    ```

20. Explain why all actions hit all reducers.

    While we have slices of state in separate reducer functions,
    `combineReducers` converts all reducers into one big reducer. Each action
    that is called is evaluated against each case in the one big combined
    reducer.

21. Attach middleware to the Redux store.

    To apply the thunk middleware into the store, you can use the
    applyMiddleware function from redux and use it as a store enhancer when
    creating the store.

    Here's an example:

    `/store/index.js`

    ```js
    import { createStore, applyMiddleware } from 'redux';
    import logger from 'redux-logger';
    import thunk from 'redux-thunk';

    const configureStore = (preloadedState = {}) => {
      return createStore(
        rootReducer,
        preloadedState,
        applyMiddleware(thunk, logger)
      );
    };

    export default configureStore;
    ```

22. Debug using the Redux Logger.

    Redux logger shows us our previous state, what action we have called and the
    next state. So we can use it to confirm if our predicted behavior is the
    actual behavior that executed.

23. Evaluate when it is appropriate to use Thunk.

    One of the most common problems you need middleware to solve is
    asynchronicity. When building web applications that interact with a server,
    you'll need to request resources and then dispatch the response to your
    store when it eventually gets back.

    While it's possible to make these API calls from your components and
    dispatch synchronously on success, for consistency and reusability it's
    preferable to have the source of every change to our application state be an
    action creator. Thunks are a new kind of action creator that will allow you
    to do that.

24. Define a fetch request to an API & Write a thunk action creator to make an
    asynchronous request to an API and dispatch another action when the response
    is received.

    ```js
    export const RECEIVE_FRUITS = 'RECEIVE_FRUITS';

    export const fetchFruits = () => async (dispatch) => {
      const res = await fetch(`/fruits`); // get the fruits at `/fruits`
      const data = await res.json();
      res.data = data;
      if (res.ok) {
        // if response status code is less than 400
        // dispatch the receive fruits POJO action
        dispatch(receiveFruits(data.fruits));
      } else {
        // if response status code is 400 or greater, throw the response as an error
        throw res;
      }
    };

    const receiveFruits = (fruits) => {
      return {
        type: RECEIVE_FRUITS,
        fruits
      };
    };
    ```

25. Refactor an async call in a React component to use Redux with Thunk.

    Dispatching thunk actions in a component is just like dispatching regular
    POJO actions. Use the `useDispatch` React-Redux hook to get the store's
    `dispatch` method and dispatch a thunk action on a user input or in a
    `useEffect`.
