
# Connecting multiple stores

* Global imports
* Dependency injection (e.g. InversifyJS)
* Root store

---

# Root store pattern

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

    constructor(fetch) {
        this.fetch = fetch
        this.todoStore = new todoStore(this)
        this.userStore = new userStore(this)
        // etc...
    }
}
```

---

```javascript
class User {
    constructor(userStore) {
        this.store = userStore
    }

    @computed get todos() {
        return this.store.rootStore.todoStore.todos.filter(
            todo => todo.assignee === this
        )
    }
}
```
---

# Test usage

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

# Client usage

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

* Scalable pattern
* Abstract away environment specific things
* Easily testable and zero-state

---
