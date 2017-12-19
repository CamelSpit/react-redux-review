## When should you use Redux?
* Implementing Redux takes a lot of code, so as a professional dev, you're going to use Redux only when the project is complex enough that it's worth it. How do you know if it's worth it? If the effort to use Redux will be outweighed by how much it simplifies managing your app's state.  
* In order to get a good handle on Redux while you are at DevMountain, you should use it in almost all projects, even if you normally wouldn't use it.  
* When using Redux, if parent components can pass to child components easily, then don't put it on redux. Use it when you need to pass state sideways, or at the top etc.

## Can you use the Redux pattern with other libraries and frameworks?
* Yes. Angular, Node, etc., can all use it. It is just a pattern.

## What does the React-Redux library do?
* React-Redux manages all of the talking between React and Redux. Redux itself manages the store/reducer (the box).

## What does every action object you push into the reducer have to have?
Every action object you want to push into redux must have a type property. The type is used within the reducer switch statement case.

## When using Redux with React, what do you need to do with `index.js`?
1. Import the `store` from `'./store'`.
```
import store from './store'
```
2. Import `{Provider}` from `'react-redux'`. 
```
import { Provider } from 'react-redux'`
```
3. Wrap `<App />` with `<Provider store={store} />`.
```
<Provider store={store}>
    <App />
</Provider>  
```
Provider is going to watch for any changes to state in the store. Importing the Provider into index.js gives Provider access to the whole app so it can trigger re-rendering in any component that needs to re-render (based on changes to state).
The state information that Provider provides is stored in the store! So the store needs to be imported as well, and then passed into Provider, which has a store "attribute" (i.e. `store={store}`).
Note: If your component uses `react-router-dom`, your imported HashRouter component will generally wrap the Provider component.

Meaning it is going to watch for our state to change in the store and when it does it will tell the components to rerender all the things that need rerendering. Helps us force that rerender on change and helps direct those changes to the right pieces.


## How do you setup the "store" for Redux?
1. Import `{ createStore }` from `'redux'`. (Not `'react-redux'`.)
```
import { createStore } from 'redux';
```
2. Import `reducer` from the location of your reducer file. 
```
import reducer from './reducer';
```
3. Pass the reducer into `createStore` and export the result of that, so that store is available to `index.js`. 
```
export default createStore(reducer); 
```

## What is an Action Creator?
A function that builds an action (object). 
```
function updateAge(age) {
  return {
      type: "UPDATE_AGE",
      payload: age
  }
}
```

## How do you setup a reducer?
A reducer has action names (which usually are made as constants), Initial state, Action Builders (functions that return action objects), and a Reducer function (which accepts incoming action objects and updates state with them). Below are some examples.

##### Action Names (generally made using "const")
```
const UPDATE_NAME = "UPDATE_NAME"; 
```

##### Initial State
```
let initialState = {
    name: ''
}
```

##### Reducer function
* The reducer function takes two parameters: the current state, and an action.
* Then it passes the type of action to a switch statement, which fires code that updates state.
```
function reducer (state = initialState, action) { 

    switch (action.type) {
        case UPDATE_NAME:
            return Object.assign({}, state, { name: action.name })
    }

    return state;
}
```
Note: If you see an equals sign in a parameter, you are seeing ES6 syntax for a default value. If state is equal to `null` or `undefined`, the value of initialState will be passed in. If state is not `null` or `undefined`, then the value of state will be passed in. What this means with Redux is that after anything changes the state, the function is just going to use state as you would expect.

##### Action Creators / Action Builders
```
export function updateName(name) {
    return {
        type: UPDATE_NAME,
        payload: name
    }
}
```

The function below has another ES6 shortcut. Rather than saying `name: name` inside the return for this object, which would create a key called `name` and assign it the value of the parameter called `name`, you can just put `name`. JavaScript will interpret this as you saying `name: name`. If you wanted to set name to a different value (for example, if your parameter was called `val`, it wouldn't work.
```
export function updateName(name) {
    return {
        type: UPDATE_NAME,
        name 
    }
}
```


## What do we need to do to connect individual React components to Redux?
1. Import the connect function from `react-redux`.
```
import { connect } from 'react-redux' // To make this component work we need to 'connect' it with our top level redux store
```

2. Import the action creator functions from the reducer.
```
import { updateName, addPerson } from './reducer' //import action creator functions from reducer
```


3. Include a "mapStateToProps" function to subscribe to state and make it available from props.
```
function mapStateToProps( state ) {
  return { 
     addressOne: state.addressOne,
     addressTwo: state.addressTwo,
     addressThree: state.addressThree
  };
}
```

4. Include an export of the connect function, passing in mapStateToProps, action creators, and the component.
```
export default connect(mapStateToProps, { updateAddOne, updateAddTwo, updateAddThree })(WizardNine); 
```

## How do the provider, store, and reducer all work together?
The reducer takes in the incoming change from the component and creates a new state from the old state and the action. The store holds the new state. Provider is going to watch for our state to change in the store and when it does it will tell the components to rerender all the things that need rerendering. 

React: Uses Provider. 
Provider: Uses the store. Watches the state at a global level and re-renders components as needed based off the new state in the store. 
Store: Uses the reducer(s). Is the box that holds the state with all its key value pairs. 
Reducer: The reducer takes in a state and action and returns a new state. It manages your box.


## Why is Object.assign() important to redux?
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
* Object.assign can be used to combine the properties of two different objects to make a new object.
* We have to replace state with a new copy of state when an action hits the reducer because we can't "mutate" or change state directly in React, including in Redux.
* We use it with Redux in the reducer when we merge the old state and the new incoming change with an empty object.
```   
case SET_VISIBILITY_FILTER:
    return Object.assign({}, state, { visibilityFilter: action.filter })
```
<!-- It makes a new empty object and we merge the state object with the third parameter object and copy it to the new one as well. Brand new empty object that replaces the original object. React JS checks to see if they are the same box but doesn't look inside the box so if the memory allocation is the same on both objects it won't tell the components to rerender. You must use Object.assign() to create a whole new memory allocation to create a new box with the changes. -->

## What do you use inside the reducer function to check the types of the actions which are being passed in?
A switch statement. Different actions have different "type" properties, so you can use the switch statement to switch the code that runs based on the type. Your reducer will have different code for different cases.
```
function reducer(state, action) {
//SINGLE POINT OF TRUTH
  switch (action.type) {
      case "UPDATE_AGE":
          return Object.assign({}, state, { age: action.age })
  }
}
```