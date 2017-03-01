# State

1. Domain state
2. View state (volatile, shared)
3. Local component state (volatile)

.appear[MobX treats them all the same]

---

# Domain State

* "What your app is about"
* Should be reusable, distributable and testable
* Should not be owned by UI
* Should not be affected by UI

---

# Domain State

* Stores: responsible for a _subdomain_ in the app: users, todos, view
* Models: data model + actions for a _concept_ in the app: user, address, todo

---

# Model

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

# Model

```javascript
class Todo {
    //...

    @action toggle() {
        this.done = !this.done
    }

    @action delete() {
        this.store.deleteTodo(this)
    }
}
```

---

# Store

```javascript
class TodoStore {
    @observable todos = []

    constructor() {
        this.fetchTodosFromServer(action((data) => {
            this.todos = data.map(item => new Todo(this, item))
        })
        autorun(() => {
            this.sendDataToServer(this.json)
        })
    }

    @action deleteTodo(todo) {
        this.todos.remove(todo)
    }
}
```

---

# Serialization

JSON is just another representation of the state.
.box2[So can be derived by MobX]

---

# Serialization

```javascript
    @computed get asJSON() {
        return {
            title: this.title,
            done: this.done
        }
    }

    constructor(store, json) {
        // ...
        // Set up side effect: each time the JSON view changes, submit it
        reaction(
            () => this.asJSON(),
            (data) => this.store.sendToServer(data)
        )
    }
```
---


---

# Real life example

[Lionshare Desktop](https://github.com/lionsharecapital/lionshare-desktop/blob/ef9d153a0033a446b8057cfb1fb726751be329b9/src/stores/PricesStore.js)

---

# Domain State

* Many architectural patterns possible; MobX is unopionated
* Classes &larr;&rarr; plain objects
* Object graph &larr;&rarr; Object tree
* Normalized &larr;&rarr; Denormalized Data
* Class methods &larr;&rarr; Dispatching Actions (Flux)
* Tip: [serializr](https://github.com/mobxjs/serializr)
* Tip: [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree)

.appear[Quiz: benefits of either approach]

