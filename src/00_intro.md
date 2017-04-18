<img src="img/mobx2.png" height="80px" />

## Complexity: Divide and Conquer!

React.Amsterdam Conf 2017

Michel Weststrate - Mendix - @mweststrate
<br/><br/>

<img src="img/mendix-logo.png" height="80px" /><br/>
.appear[
    <small>(Nr 1. best tech employer in NL according to Glassdoor.com)</small>
]

---

![RomanRepublic40BC.jpg](img/RomanRepublic40BC.jpg)

<!--
By <a href="//commons.wikimedia.org/wiki/User:Tataryn77" class="mw-redirect" title="User:Tataryn77">Tataryn77</a> - <span class="int-own-work" lang="en">Own work</span>, Public Domain, <a href="https://commons.wikimedia.org/w/index.php?curid=11154814">Link</a>
</small>
-->

---

![Asterix](img/asterix.jpg)

---

![Napeon](img/Napoleoniceurope.png)

<!--
By <a href="https://en.wikipedia.org/wiki/User:Kieran4" class="extiw" title="en:User:Kieran4">Kieran4</a> - <a href="https://en.wikipedia.org/wiki/File:Napoleoniceurope.png" class="extiw" title="en:File:Napoleoniceurope.png">en:File:Napoleoniceurope.png</a>Napoleon Bonaparte by Alan Schom, map at start of book, <a href="http://creativecommons.org/licenses/by-sa/3.0" title="Creative Commons Attribution-Share Alike 3.0">CC BY-SA 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=6006526">Link</a>
-->
---

![Napoleon](img/1200px-Napoleon_Wagram.jpg)

<!--
By <a href="https://en.wikipedia.org/wiki/Horace_Vernet" class="extiw" title="en:Horace Vernet">Horace Vernet</a> - <a rel="nofollow" class="external autonumber" href="http://www.biografiasyvidas.com/monografia/napoleon/fotos/napoleon_wagram.jpg">[1]</a>, Public Domain, <a href="https://commons.wikimedia.org/w/index.php?curid=246073">Link</a>
-->


---

## Divide et Impera

---

## Small, isolated problems are much easier to deal with than intertwined big problems

---

# Build a webapp?

1. .appear[Pick a tool: React]
2. .appear[Pick a compiler...]
3. .appear[Pick a bundler...]
3. .appear[Get them to work together]
4. .appear[Build the web app]

.appear[<img src="img/OwlProblem.jpg" style="position:absolute;left: 40px; top:200px; width: 400px"/>]

---

# Reactive programming

.appear[
    > The essence of functional reactive programming is to specify the dynamic behavior of a value completely at the time of declaration.

    Heinrich Apfelmus, reactive-banana
]

---

# Reactive programming

Separate the _how_ from the _when_ question

---

```javascript
class Timer extends React.Component {
  render() {
    return (
      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
    );
  }

  componentDidMount() {
    setInterval(() => {
       this.setState(prevState => ({
          secondsElapsed: prevState.secondsElapsed + 1
       }))
     }, 1000)
  }
}
```

---

`view = f(state)`

.appear[
`view`<sub>1</sub>` = f(state`<sub>1</sub>`)`<br/>
`view`<sub>2</sub>` = f(state`<sub>2</sub>`)`<br/>
...<br/>
`view`<sub>n</sub>` = f(state`<sub>n</sub>`)`<br/>
]

---

* .appear[React only reacts to component state changes]
* .appear[Separates the _how_ and _when_]
* .appear[...But strongly couples _state_ and _view_]
  * .appear[Hard to share state]
  * .appear[Hard to move components around]
  * .appear[Hard test state & logic in isolation]
  * .appear[Hard test components in isolation]

---

## We need more divide and conquer!

<img src="img/napoleon2.jpg" />
.appear[
<img src="img/mobx2.png" style="position:absolute;top:100px;left:480px;width:150px" />
]

---

<table width="100%">
    <tr>
        <td>Input</td>
        <td>State</td>
        <td>Output</td>
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear"><i>@action</i>'s<br/><br/>modify state</div>
        </td>
        <td rowspan="2" class="box-state">
            <div><i>@observable</i> state<br/><br/>the data your app is about</div>
        </td>
        <td class="box-computed">
            <div class="appear"><i>@computed</i> values<br/><br/>all data that can be expressed in terms of other data</div>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear"><i>reaction</i>'s<br/><br/>trigger side effects when necessary</div>
        </td>
    </tr>
</table>

---


# MobX

Makes sure .box1[data] is always, automatically and efficiently reflected in .box3[derived values] and that necessary .box4[side effects] are fired.

---

# Why?

<img src="img/excel.png" height="150px" />

* .appear[Pattern used in the most successful software product]
* .appear[Makes programming more declarative]
* .appear[Natural way of thinking]
* .appear[Helps to define minimal state]

---

<table width="100%">
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">Users enters data</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Data Cells</div>
        </td>
        <td class="box-computed">
            <div class="appear">Formulas</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Draw on screen</appear>
        </td>
    </tr>
</table>

---

<table width="100%">
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">setState</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>component state</div>
        </td>
        <td class="box-computed">
            <div class="appear">render()</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">VDOM</appear>
        </td>
    </tr>
</table>

---

# MobX + React

```javascript
const state = observable({
    secondsElapsed: 0
})

setInterval(() => {
    state.secondsElapsed++
}, 1000)
```


```javascript
const Timer = observer(() =>
    <div>Seconds Elapsed: {state.secondsElapsed}</div>
)

ReactDOM.render(<Timer />, document.body)
```

---

# Recap

* Reactive programming separates the _how_ from the _when_.<br/>Eliminates the need to call `render()`<br/><br/>
* MobX separates the _state_ from the _view_.<br/>Components will now react to state _outside_ component.

---

# Question

`view = f(state)`

.appear[What happens if we eliminate `f`?]
.appear[
    * No view
    * Is the app still usable?
    * Can we still navigate around?
    * Without smart components, who will fetch data?
]
.appear[Can we make our UI dumb enough to make our app usable without it?]

---

Demo

---

## `componentDidMount` fetching data makes state dependent on the view

_Placing async effects also often happens in some component’s componentDidMount, which is an ad-hoc solution, since components are often meant only for view concerns (markup).
It often feels incorrect to mix these concerns since Redux and its async solutions are meant to separate them from markup._

André Staltz in "Some Problems with React/Redux"

---

* .appear[Q: **Why do we want to fetch data?**]
* .appear[A: <i>Component needs it</i>]
* .appear[Q: **Why does component exist?**]
* .appear[A: <i>It's just always there</i>]
* .appear[A: <i>Parent is in certain state</i>]
* .appear[A: <i>The router mounted it</i>]
* .appear[A: <i>42</i>]
* .appear[Q: **Can we make component caller responsible?**]

---

# ... If the component is always there...

```javascript
class BookList {
    componentDidMount() {
        this.props.store.fetchAllTheData()
    }

    render() {
        return this.props.store.books.map(book => <stuff />)
    }
}
```

```javascript
const store = new BookStore()
ReactDOM.render(<BookList store={store} />)
```

---

# ... If the component is always there...

```javascript
const BookList = ({ store } =>
    store.books.map(book => <stuff />)
)
```

```javascript
const store = new BookStore()
store.fetchAllTheData()
ReactDOM.render(<BookList store={store} />)
```

---

# ... Because of parent state...

```javascript
class HomePage {
    render() {
        if (this.state.showBooks)
            return <BookList store={this.props.store} />
        else
            return <AboutPage />
    }

    onClickShowBooks() {
        this.setState({
            showBooks: true
        })
    }
}
```

---

# ... Because of parent state...

```javascript
class HomePage {
    render() {
        if (this.state.showBooks)
            return <BookList store={this.props.store} />
        else
            return <AboutPage />
    }

    onClickShowBooks() {
        this.props.store.fetchAllTheData()
        this.setState({
            showBooks: true
        })
    }
}
```

---

# ... being mounted by router...

```javascript
<Route path="/books/" component={ BookList } />
```

.appear[Where to put fetch responsibility?]

.appear[Architectural mismatch]

.appear[![routing](img/routing1.png)]
---

## If view is to be purely .box3[derived] from the .box1[state], then routing should affect our .box1[state], not the .box4[component tree]

![routing](img/routing2.png)

```javascript
<Route path="/books/" onEnter={ () => store.showBooks() } />
```

---

## The bookshop architecture

.appear[(and Mendix WebModeler architecture)]
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

# Testing app flow

```javascript
it('as a user I can buy books', (done) => {
  const shop = new ShopStore(bookFetcher)

  shop.view.openBooksPage()
  expect(shop.view.page).toBe("books")
  expect(shop.bookStore.isLoading).toBe(true)

  when(
      () => !shop.isLoading,
      () => {
          expect(shop.books.size).toBe(4)

          shop.view.openBookPageById("978-1423103349")
          expect(shop.view.selectedBook.name).toBe("The Sea of Monsters")

          shop.cart.addBook(shop.view.selectedBook)
          shop.view.openCartPage()
          expect(shop.cart.canCheckout).toBe(true)

          shop.cart.checkout()
          expect(shop.cart.entries.length).toBe(0)
          expect(shop.cart.canCheckout).toBe(false)
          done()
      }
  )
})
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

## Routing

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

# The URL as yet-another-view

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

# Side effect: update browser history

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

<img src="img/routing3.png" width="900px" />

---

# Divide & conquer: separate UI and state

* Makes user flow testing a breeze
* Allows independent development of stores, logic and components

1. Capture crucial app state in a browser unaware store
2. UI just renders this state
3. Don't make state dependent component hooks
4.  Browser url and history are just another set kind of actions / reactions

---

# Need more?

* [Blog: How to decouple state and UI (a.k.a. you don’t need componentWillMount)](https://medium.com/@mweststrate/how-to-decouple-state-and-ui-a-k-a-you-dont-need-componentwillmount-cc90b787aa37#.q5ksxr1is)
* [yester](http://basarat.com/yester/#/) - Hooks, strong typed
* [mobx-router](https://www.npmjs.com/package/mobx-router) - Hooks, renders comps, store structure
* Or generic libraries, `directory`, or `history` + `path-to-regexp`

workshop 2 juni Xebia

[Egghead course](https://egghead.io/courses/mobx-fundamentals)

---