
# Making the UI dumb

* The less logic lives in components, the easier your app is to test
* Portable components and domain logic

---

# The problem

```javascript
const User = React.createClass({
  componentDidMount() {
    this.setState({
      // route components are rendered with useful information, like URL params
      user: findUserById(this.props.params.userId)
    })
  },
```

---

# The problem

![routing](img/routing1.png)

* 'Smart' components parse routes and fetch data
* Breaks view = f(state)
* Hard to test application flow without testing components

---

# Ideal situation

![img/routing2.png](img/routing2.png)

---

# Capturing navigation state

```javascript
class ViewStore {
    @observable currentUser = null;
    @observable currentView = null;
}
```

---

# Navigating programmatically

```javascript
 @action showOverview() {
    this.currentView = {
        name: "overview",
        documents: fromPromise(fetch(`/json/documents.json`))
    }
}
```

---

# Testing app flow

```javascript
test("it should be possible to open the documents overview", t => {
    const viewStore = new ViewStore()
    viewStore.showOverview()

    t.equal(viewStore.currentView.name, "overview")
    when(
        () => viewStore.currentView.documents.state !== "pending",
        () => {
            t.equal(viewStore.currentView.documents.state, "fulfilled")
            t.equal(viewStore.currentView.documents.value.length, 2)
            t.end()
        }
    )
})
```

---

# Rendering app to DOM with dumb comps

```javascript
const App = observer(({viewStore}) => {
    const view = viewStore.currentView;
    switch (view.name) {
        case "overview":
            return <DocumentOverview documents={view.documents} />
        default:
            return <PageNotFound />
    }
})
```

---

# Rendering app to history

```javascript
@computed get currentPath() {
    switch(this.currentView.name) {
        case "overview": return "/document/"
        case "document": return `/document/${this.currentView.documentId}`
    }
}
```

.appear[
```javascript
autorun(() => {
    const path = viewStore.currentPath
    if (path !== window.location.pathname)
            window.history.pushState(null, null, path)
})
```
]

---

```javascript
import { Router } from 'director';

export function startRouter(store) {
    // update state on url change
    const router = new Router({
        "/document/:documentId": (id) => store.showDocument(id),
        "/document/": () => store.showOverview()
    }).configure({
        notfound: () => store.showOverview(),
        html5history: true
    }).init()
}
```

---

![img/routing3.png](img/routing3.png)

---

# Solution: view store

* Capture crucial app state in a browser unaware store
* UI just renders this state
* Browser url / history is just another derivation
* Navigation triggers state changes instead of component hooks
* [How to decouple state and UI (a.k.a. you don’t need componentWillMount)](https://medium.com/@mweststrate/how-to-decouple-state-and-ui-a-k-a-you-dont-need-componentwillmount-cc90b787aa37#.q5ksxr1is)

---

# Libraries

* [mobx-router](https://www.npmjs.com/package/mobx-router) - Hooks, renders comps, store structure
* [yester](http://basarat.com/yester/#/) - Hooks, strong typed
* [mobx-react-router](https://www.npmjs.com/package/mobx-react-router) - Simplifies MobX + React-router, like react-router-redux

---