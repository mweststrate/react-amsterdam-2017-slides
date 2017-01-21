## MobX patterns

Michel Weststrate - @mweststrate

MobX - Mendix

<img src="img/mobx2.png" width="150px" />

---

# MobX

Simple & Scalabe State Management

Transparent Reactive Programming Library

---

MobX:

1. Find minimal state that expresses the state of your app
2. Derive all other things from the minimal state
3. MobX keeps derivations in sync using runtime analysis

---

Advantages

---

1. Super simple to write

_Focus on what you want to achieve. Ignore how it should affect your application. It will be derived._

---

2. Eliminates bugs related to cascading updates

_If the name of the user changes, don't forget to update the entry in the lookup table..._

---

3. Eliminates bugs related to staleness

_If the name of the user changes, don't forget to redraw the toolbar and all his tweets..._

TWITTER IMG

---

4. Fine grained updates perform much better then event based updates

_Don't notify all tweet components, only those who will be affected by this change..._

---

Demo

---

# Thinking in MobX

The MobX taxes system:

<table width="100%">
    <tr>
        <td rowspan="2" style="background:yellow; height: 300px; width: 33%">
            Box 4: Actions: Modify state
        </td>
        <td rowspan="2" style="background:cyan; width: 33%">
            Box 1: State: observable (complex) values
        </td>
        <td  style="background:lightgreen; width: 33%; height: 50%;">
            Box 2: Computed values: observable, derived values
        </td>
    </tr>
    <tr>
        <td  style="background:red">
            Box 3: Reactions: Run side effects when needed
        </td>
    </tr>
</table>

---

# Excercise

<img src="img/excel.png" width="80%" />

---

What is the price of a product including VAT?

COLORS

* .appear[Price of a product _excluding_ VAT?] .appear[Box 1]
* .appear[Price of a product _including_ VAT?] .appear[Box 2]
* .appear[VAT rate?] .appear[Box 1]
* .appear[Changing the VAT reate?] .appear[Box 4]
* .appear[Rendering the price of a product in the DOM?] .appear[Box 3]
* .appear[Sending an SMS with the updated price?] .appear[Box 3]

---

Some tax rules:

COLORS

* It is simply impossible to observe a stale derived (Box 2) value
* MobX won't update computed values (Box 2) that are not (indirectly) in use by a reaction (Box 3)
* (Unless explicitly asking for it)
* In strict mode, only actions (Box 4) can modify state (Box 1)
* If you compute a new value in Box 3 (reaction) it probably belongs to Box 2
* Before an action ends, all affected derived values will be updated, and all reactions will have fired.
* For each action, every computed value and reactoin will run only once and the right order

---

MobX api:

COLORS

* `@observable`
* `@computed`
* `observer`, `autorun`, `reaction`, `when`
* `@action`

---

TODO APP (with colors)


---

# ES 6 Recap

Classes

Field initializers

Decorators

---

# Working with observables

Box

Object

Array

Infective / references

---

TODO Demo

---

# Organizing your

1. Component state
2. Domain state

---

# Component state

Rule of thumb: state that doesn't need to survive a reload

`setState` or `@observable`?

https://twitter.com/mrjjwright/status/822637233417162752

Tip: [3 reasons why I stopped using setState](https://medium.com/@mweststrate/3-reasons-why-i-stopped-using-react-setstate-ab73fc67a42e#.3tuprki3h)

---

# Domain State

* Should be reusable, distributable and testable
* Should not be owned by UI
* Should not be affected by UI

---

# Domain State

* Many architectural patterns possible; MobX is unopionated
* Classes <-> plain objectjs
* Object graph <-> Object tree
* Normalized <-> Denormalized Data
* Class methods <-> Dispatching Actions (Flux)
* Tip: serializr
* Tip: mobx-state-tree

---

# Domain Models

```javascript
class Todo {
    @observable title
    @observable done = false

    constructor(store, json) {
        this.store = store
        Object.assign(this, json)
    }
}
```
---

```javascript
    @action toggle() {
        this.done = !this.done
    }

    @action delete() {
        this.store.deleteTodo(this)
    }
}
```

---

Serialization?

JSON is just another representation of the state.

Box .appear[2]

---

```javascript
    @computed get asJSON() {
        return {
            title: this.title,
            done: tis.done
        }
    }
```

.appear[
```javascript
    constructor(store, json) {
        // ...
        // Set up reactin: each time the JSON view changes, submit it
        reaction(
            () => this.asJSON(),
            (data) => this.store.sendToServer(data)
        )
    }
```
]

---

# Domain Stores

```javascript
class TodoStore {
    @observable todos = []

    @action deleteTodo(todo) {
        this.todos.remove(todo)
    }
```

---

# Connecting stores?

* Global imports .appear[-> global state, hard to test / reset]
* Dependency injection (InversifyJS)
* Root store
  * _One for all, all for one_
  * No global state
  * Straight forward
  * Can provide environment specific things (`fetch`)
  * Strongly typed
  * Can be made generic


---

```javascript
class RootStore {
    todoStore: TodoStore
    userStore: UserStore
    fetch

    constructor(fetchImpl) {
        this.todoStore = new todoStore(this)
        this.userStore = new userStore(this)
        // etc...
    }
}
```

---

```javascript
class User {
    @computed get todos() {
        return this.store.rootStore.todoStore.todos.filter(
            todo => todo.assignee === this
        )
    }
}
```
---

## Client usage

```javascript
const rootStore = new RootStore(window.fetch)

React.render(
    <Provider rootStore={rootStore}>
        <App />
    </Provider>
)

rootStore.loadInitialData()
```

(`Provider` will be discussed later)

---

## Test usage

```javascript
test("It should test something, t => {
    const root = new RootStore(() =>
        Promise.resolve({ todos: [], users: ["Michel] })
    })
    .loadInitialData()
    .then(() => {
        t.equal(root.userStore.users.length, 1)
        t.end()
    })
})
```
---

# Root store

Scalable pattern:
* Transpartion layer
* Browser history management
* Environment specific configuration

Test all logic of your app without a single render
* Beware of routing. To be continued.

---

<p>egghead.io/courses/mobx-fundamentals</p>
<p>@mweststrate</p>

