# How does MobX know what to react to?

Demo: [Todo App](https://jsfiddle.net/mweststrate/wv3yopo0/)

---

# Some background:

```
> var x = "abc"
undefined
> x[2] = "d"
'd'
> x
'abc'
```

---

# Some background:

* Most JavaScript concepts are immutable
* Exception: Collections (object, array) and variables
* Need to box primitives to be able to "modify" primitive variables, or to "pass by reference"
* So, only objects, arrays and variables can hold state!

---

# Boxing primitives

```javascript
const temperature = observable.box(37)

autorun(() => {
    console.log(temperature.get());
})
// Prints: 37

temperature.set(21)
// Prints: 21
```

---

# Boxing primitives

```javascript
class Box {
    value;

    get() {
        return this.value
    }

    set(v) {
        this.value = v
    }
}
```

---

# Object properties

```javascript
const store = observable({
    temperature: 37
})

store.temperature = 17
```

```javascript
const store = new class Store {
    @observable temperature = 37
}

store.temperature = 17
```

---

# Object properties

```javascript
const store = {
    __temperature: observable.box(37),
    get temperature() {
        return this.__temperature.get()
    },
    set temperature(v) {
        this.__temperature.set(v)
    }
}
```

---

# Object properties

```javascript
extendObservable(target, propName, initialValue) {
    const box = observable.box(initialValue)
    Object.definePropety(target, propName, {
        get: box.get.bind(box),
        set: box.set.bind(box)
    })
}
```

---

## MobX tracks property _access_, not values!

---


<img src="img/observed-refs.png" width="1000px" />

---

# Autorun

* Takes a function without arguments
* Determines which observables it depends on
* Re-runs when any of them changes
* Bridges from reactivity to the imperative side effecty world
* Network requests, flushing DOM, logging..

---

# Autorun

```javascript
const temperature = observable.box(37)

autorun(() => {
    console.log(temperature.get());
})
// Prints: 37

temperature.set(21)
// Prints: 21
```

---

## How does autorun work?

---

```javascript
class Derivation {
    constructor(fn) {
        this.fn = fn
    }
    run() {
        mobx.currentDerivation = this
        this.fn()
        mobx.currentDerivation = null
    }
}

function autorun(fn) {
    const derivation = new Derivation(fn)
    derivation.run()
}
```

---

```javascript
class Box {
    value;
    observers = new Set();

    get() {
        if (mobx.currentDerivation)
            this.observers.push(mobx.currentDerivation)
        return this.value
    }

    set(v) {
        this.value = v
        this.observers.forEach(o => o.run())
    }
}
```

In more detail [YouTube: Magic MobX](https://www.youtube.com/watch?v=TfxfRkNCnmk)

---

# Computed values

* .appear[Like `autorun`, but; produce value using pure function]
* .appear[Can have observers like boxes]
* .appear[Will suspend if nobody is observing it's values (auto GC)]
* .appear[If suspended, can still compute on demand if needed]
* .appear[Just a caching point, if left out, behavior of app is the same]
* .appear[Can't be used enough]

---

# Computed values

```javascript
const logDisposer = autorun(() => {
  console.log(elise.fullName)
})
// prints fullname

logDisposer()
// stop printing *and* computing fullname at all!

console.log(elise.fullName)
// prints correct fullname, on-demand evaluation
```

---

# Actions

* Wrap functions that modify state in them
* Batch changes, reactions will only run if outer-most action complete
* Computed values will be consistent inside actions
* use `action.bound` as autobind
* use `mobx.strictMode(true)` to forbid changing observables outside actions

---

## MobX reacts to any _existing_ observable property that is read _during_ the execution of a tracked function.


