## Working with MobX with react SSR and Typescript.

### First wee need to create file for mobX such as Counter.ts

```javascript
    // Import makeObservable, observable and action from mobx

    import { makeObservable,observable, action } from "mobx";

    export class Couter {

        // create initial value (if needed)
        numberofClicks = 0;

        // create constructor for observing observable values and actions

        constructor() {
            makeObservable(this, {
                numberofClicks: observable,
                increment: action
            });
        }

        // write functions

        increment = (amount: number) => {
            this.numberofClicks += amount;
        }
    }
```

### After that go your App.tsx component or any component such as Home

```javascript
    import { useState } from "react";

// MobX
import { Couter } from "../mobx/Counter";

// You need to wrap your component with Observer
import { observer } from "mobx-react-lite";

const counter = new Couter()

// observer
const Home = observer(() => {

    // number
    const [num, setNum] = useState(0);
    const [incrementBy, setIncrementBy] = useState(1);

    return (
        <div>

            <input value={incrementBy} onChange={(e) => setIncrementBy(parseInt(e.target.value))} type="number" />
            

            {/* MobX */}
            <div>
                <p>MobX Clicks: {counter.numberofClicks}</p>
            </div>
            <button onClick={() => counter.increment(incrementBy)}>increment MobX</button>

        </div>
    )
})

export default Home
```


### So then It is done, you can do other operations with the same logic. So good luck.


