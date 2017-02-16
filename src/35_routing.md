
# Making the UI dumb

* The less logic lives in components, the easier your app is to test
* Portable components and domain logic

---

# The problem

```javascript
const User = React.createClass({
  componentDidMount() {
    this.setState({
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
    const docs = viewStore.currentView.documents
    when(
        () => docs.state !== "pending",
        () => {
            t.equal(docs.state, "fulfilled")
            t.equal(docs.value.length, 2)
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

# Rendering app to URL

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

# Updating state when location changes

```javascript
import { Router } from 'director';

export function startRouter(store) {
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

<img src="img/routing3.png" width="900px" />

---

# Dumb UI: create a view store

* Capture crucial app state in a browser unaware store
* UI just renders this state
* Browser url / history is just another derivation
* Navigation triggers state changes instead of component hooks
* [Blog: How to decouple state and UI (a.k.a. you don’t need componentWillMount)](https://medium.com/@mweststrate/how-to-decouple-state-and-ui-a-k-a-you-dont-need-componentwillmount-cc90b787aa37#.q5ksxr1is)

---

# Libraries

* [mobx-router](https://www.npmjs.com/package/mobx-router) - Hooks, renders comps, store structure
* [yester](http://basarat.com/yester/#/) - Hooks, strong typed
* [mobx-react-router](https://www.npmjs.com/package/mobx-react-router) - Simplifies MobX + React-router, like react-router-redux
* [mobx-location](https://www.npmjs.com/package/mobx-location) - Window.location as observable
---