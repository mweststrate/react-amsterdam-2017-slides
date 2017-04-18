
# The problem

![routing](img/routing1.png)

* 'Smart' components parse routes and fetch data
* Breaks view = f(state)
* Hard to test application flow without testing components
* Often hard to test components without appliation flow

---

_Placing async effects also often happens in some component’s componentDidMount, which is an ad-hoc solution, since components are often meant only for view concerns (markup).
It often feels incorrect to mix these concerns since Redux and its async solutions are meant to separate them from markup._

André Staltz in Some Problems with React/Redux

---

# Ideal situation

![img/routing2.png](img/routing2.png)

---

# Intermezzo: capturing app state in stores

```javascript
import BookStore from './BookStore'
import CartStore from './CartStore'

export default class ShopStore {
  fetch
  bookStore
  cartStore

  constructor(fetcher) {
    this.fetch = fetcher
    this.bookStore = new BookStore(this)
    this.cart = new CartStore(this)
    this.bookStore.loadBooks()
  }
}
```

---

# Intermezzo: capturing app state in stores

App usage:

```
const shop = new ShopStore(window.fetch)

ReactDOM.render(
  <Provider shop={shop}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

---

# Intermezzo: capturing app state in stores

Testing:

```javascript
const bookFetcher = () => Promise.resolve(JSON.parse(fs.readFileSync("./test-assets/books.json")))

test("It should start loading books when starting the app", () => {
    const shop = new ShopStore(bookfetcher)
    expect(shop.bookStore.isLoading).toBe(true)
})

```

---


# Capturing navigation state

```javascript
class ViewStore {
  @observable page = "books"
  @observable selectedBookId = null
}
```

---

# Capturing navigation state

```javascript
class ViewStore {
  @observable page = "books"
  @observable selectedBookId = null

  @action openBooksPage() {
    this.page = "books"
  }

  @action openBookPageById(id) {
    this.page = "book"
    this.selectedBookId = id
  }
}
```

---

# Capturing navigation state

```javascript
class ViewStore {
  @observable page = "books"
  @observable selectedBookId = null

  @action openBooksPage() {
    this.page = "books"
    this.bookstore.loadBooks()
  }

  @action openBookPageById(id) {
    this.page = "book"
    this.selectedBookId = id
    this.bookstore.fetchBookById(id)
  }
}
```

---

# Rendering

```javascript
const Books = observer(({store}) =>
    store.bookStore.isLoading
        ? <div>Wait for it...</div>
        : <div>{ store.bookStore.books.map(book =>
            <Book key={book.id} book={book} />
        )}
)
```

.appear[Bye, `componentDidMount`!]

---

---

# Back to routing

---

# Rendering app to URL

```javascript
export default class ViewStore {
  @observable page = "books"
  @observable selectedBookId = null

  @computed get currentUrl() {
    switch (this.page) {
      case "books":
        return "/"
      case "book":
        return "/book/" + this.selectedBookId
    }
  }
}
```

---

# Rendering app to URL

```javascript
reaction(
  () => shop.view.currentUrl,
  (path) => {
    if (window.location.pathname !== path)
      window.history.pushState(null, null, path)
  }
)
```

---

TODO: which router? yester?

---

# Reacting to route changes

```
const router = createRouter({
  "/book/:bookId": ({bookId}) => shop.view.openBookPageById(bookId),
  "/cart":         shop.view.openCartPage,
  "/":             shop.view.openBooksPage
})

window.onpopstate = function historyChange(ev) {
  if (ev.type === "popstate")
    router(window.location.pathname)
}

router(window.location.pathname)
```

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