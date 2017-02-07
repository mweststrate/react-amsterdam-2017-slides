# Domain State

* "What your app is about"
* Unlike component state which is volatile
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

# Serialization?

JSON is just another representation of the state.

Box 2: Computed values

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
        // Set up reaction: each time the JSON view changes, submit it
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
}
```
---

# Real life example

https://github.com/lionsharecapital/lionshare-desktop/blob/master/src/stores/PricesStore.js


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

---

# Root store

Scalable pattern:
* Transpartion layer
* Browser history management
* Environment specific configuration

Test all logic of your app without a single render
* Beware of routing. To be continued.

---

# Provider / inject

* Provider / inject: Inject stores in components
* Uses context, but: doesn't break shouldComponentUpdate
* mobx-react package
* [How to safely use React context](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076#.1y806u7cy)

---

# Provider / inject

```javascript
class App extends React.Component {
  render() {
    return <Provider color="red">
        <div>
            {this.props.messages.map((message) =>
                <Message text={message.text} />
            )}
        </div>
    </Provider>;
  }
}
```

---

# Provider / inject

```javascript
@inject("color") @observer
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.props.color}}>
        {this.props.children}
      </button>
    );
  }
}
```