## Working with Redux with react SSR and Typescript.

### First wee need to create folder for redux. Many people like to call it Store. Go name it "Store" and there should be 3 files (but it also depends on you, you can create more files).

### actions.ts
### reducers.ts
### store.ts

### actions.ts. It should include all actions (types). Redux will recognise your functions (reducers) based on this actions (types).

```javascript
    // actions.ts
    
    export const counterButtonClicked = (amount: number) => ({
        // it is just type, it can be anything, it depends on you.
        type: 'COUNTER_BUTTON_CLICKED',
        payload: { amount: amount },
    })
```

### reducers.ts. It should include all functions (reducers). Later, we will call them with useDispatch() function.

```javascript
    // reducers.ts
    
    
    type CounterButtonClickedAction = {
        type: 'COUNTER_BUTTON_CLICKED';
        payload: {
            amount: number;
        };
    };

    export const numberOfClicksReducers = (state = 0, action:   CounterButtonClickedAction) => {
        const { type } = action;

        // Here we are make function to work by recognizing types.
        switch (type) {
            case 'COUNTER_BUTTON_CLICKED':
                return state + action.  payload.amount;
            default:
                return state;
        }
    }
```


### store.ts. It help us to get access to reducers and other needed functions.

```javascript
    import { createStore, combineReducers } from "redux"
    import { numberOfClicksReducers } from "./reducers";


    const rootReducer = combineReducers({
        numberOfClicks: numberOfClicksReducers,
    });

    export const store = createStore(rootReducer)
```

## Next thing that we need to pay attention is App.tsx. We need to wrap our main componet. It can be intex.tsx or App/tsx. So, we need to pass store to our Provider. After that, we can access our functions from any component. 

```javascript
import Home from './Home'
import { Provider } from 'react-redux'
import { store } from './container/store'


function App() {

  return (
    <Provider store={store}>
        <Home />
    </Provider>
  )
}

export default App

```

## our Home.tsx. You can see We are accesing our function from our Home.tsx

```javascript

import { useState } from "react";

// Redux
import { useSelector, useDispatch } from "react-redux";
import { counterButtonClicked } from "../container/actions";

type StateType = {
    numberOfClicks: number;
};

const Home = () => {

    // Context
    const [num, setNum] = useState(0);
    const [incrementBy, setIncrementBy] = useState(1)

    // Redux
    const numberOfClicks = useSelector((state: StateType) => state.numberOfClicks)
    const dispatch = useDispatch();


    return (
        <div>

            <input value={incrementBy} onChange={(e) => setIncrementBy(parseInt(e.target.value))} type="number" />

            {/* Redux */}
            <div>
                <p>Redux Clicks: {numberOfClicks}</p>
            </div>
            <button onClick={() => dispatch(counterButtonClicked(incrementBy))}>increment redux</button>

        </div>
    )
}

export default Home

```


## So it is done with our Redux project. Hope it was helpfull for you. The End )





