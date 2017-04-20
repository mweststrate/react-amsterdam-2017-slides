<img src="img/mobx2.png" height="80px" />

## Complexity: Divide and Conquer!

React.Amsterdam Conf 2017

Michel Weststrate - Mendix - @mweststrate
<br/><br/>

<img src="img/mendix-logo.png" height="80px" /><br/>
<small>(Nr 1. best tech employer in NL according to Glassdoor.com)</small>

???

5 o clock, over time

---

![RomanRepublic40BC.jpg](img/RomanRepublic40BC.jpg)

<!--
By <a href="//commons.wikimedia.org/wiki/User:Tataryn77" class="mw-redirect" title="User:Tataryn77">Tataryn77</a> - <span class="int-own-work" lang="en">Own work</span>, Public Domain, <a href="https://commons.wikimedia.org/w/index.php?curid=11154814">Link</a>
</small>
-->

---

class: top fullscreen center

![Asterix](img/asterix.jpg)

---

![Napeon](img/Napoleoniceurope.png)

<!--
By <a href="https://en.wikipedia.org/wiki/User:Kieran4" class="extiw" title="en:User:Kieran4">Kieran4</a> - <a href="https://en.wikipedia.org/wiki/File:Napoleoniceurope.png" class="extiw" title="en:File:Napoleoniceurope.png">en:File:Napoleoniceurope.png</a>Napoleon Bonaparte by Alan Schom, map at start of book, <a href="http://creativecommons.org/licenses/by-sa/3.0" title="Creative Commons Attribution-Share Alike 3.0">CC BY-SA 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=6006526">Link</a>
-->
---

class: top fullscreen center

![Napoleon](img/1200px-Napoleon_Wagram.jpg)

<!--
By <a href="https://en.wikipedia.org/wiki/Horace_Vernet" class="extiw" title="en:Horace Vernet">Horace Vernet</a> - <a rel="nofollow" class="external autonumber" href="http://www.biografiasyvidas.com/monografia/napoleon/fotos/napoleon_wagram.jpg">[1]</a>, Public Domain, <a href="https://commons.wikimedia.org/w/index.php?curid=246073">Link</a>
-->


---

## Divide et Impera

---

class: top fullscreen center

![ken](img/ken.jpg)

---

## Small, isolated problems<br/>are much easier to deal with<br/>than intertwined big problems

---

# Reactive programming

.appear[
The essence of functional reactive programming is to specify the dynamic behavior of a value completely at the time of declaration.

_Heinrich Apfelmus, reactive-banana_
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
        )
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
* .appear[...But strongly couples _state_ and _view_]
  * .appear[Hard to share state]
  * .appear[Hard to move components around]
  * .appear[Hard to optimize]
  * .appear[Hard to test state & logic in isolation]
  * .appear[Hard to test components in isolation]

???

Now there is an ongoing debate on whether just setState alone doesn't suffice.
That might be the case for relatively simple apps indeed.
However, at mendix our domain state alone consists of 500, highly cyclical classes.
So for us putting all our state in component state is kinda outof the question.


---

## We to divide and conquer more!

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
            <div>component state & props</div>
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
import { observable } from "mobx"
import { observer } from "mobx-react"

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
* MobX separates the _state_ from the _view_.<br/>Components can now react to state _outside_ component.

---

# Question

`view = f(state)`

* .appear[What happens if we eliminate `f`?]
* .appear[Is the app still usable?]
* .appear[How to navigate around without anchors?]
* .appear[Who will fetch data without smart components?]

---

## Can we make our UI dumb enough to make our app usable without it?

---

class: fullscreen

<video id="shopMovie" src="img/shop.mp4" height="700px" controls></video>

---

class: top

<img src="img/napoleon3.jpg" style="float:left;position:relative;top:-1em;left:-1em;" />

.right[
## We divided state and view

We can now conquer them one by one
]

???

Imagine what this means for testing

---

# How was this achieved

1. .appear[Don't depend on `componentDidMount` for data fetching]
2. .appear[Don't depend on router / links for navigation]
3. .appear[Leverage the 4 reactive boxes]

---

# Eliminating `componentDidMount`

* .appear[Q: **Why do we want to fetch data?**]
* .appear[A: <i>Component needs it</i>]
* .appear[Q: **Why does component exist?**]
* .appear[A: <i>42</i>]
* .appear[A: <i>It's just always there</i>]
* .appear[A: <i>Parent is in certain state</i>]
* .appear[A: <i>The router mounted it</i>]
* .appear[Q: **Can we make component caller responsible?**]

---

# If the component is always there...

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

# ... fetching can be done outside tree

```javascript
const BookList = observer(({ store }) =>
    store.books.map(book => <stuff />)
)
```

```javascript
const store = new BookStore()
store.fetchAllTheData()
ReactDOM.render(<BookList store={store} />)
```

???

Now remember, the reason that this lifting is possible is because with mobX our components can react to state changes outside the component

---

# If parent causes existence...


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

# ...state transition can trigger fetch

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

???

This is the most common case. And the reasoing behind this lift is:
if the existence of a component is a reaction to a state change,
then there is somewhere an action that causes that state change to happen, and we can lift
our data fetching to that location.

---

# ...or use a reactive side effect


```javascript
import { when } from "mobx"

when(
    () => store.showBooks === true,
    () => store.fetchAllTheData()
)
```


???

More experienced MobX users take this even futher and declare data fetches as being reactions on their own, that looks like this.
But in this talk we keep it simple and just model them as part of our actions

---

# If router mounts component...

```javascript
<Route path="/books/" component={ BookList } />
```

.appear[...there is no action outside tree to lift fetching to]

---

class: fullscreen

<img src="img/routing4.png" />

---

class: fullscreen

<img src="img/routing5.png" />

<div style="position:absolute;top:35px;left:755px">URL change</div>

---

class: fullscreen

<img src="img/routing6.png" />

<div style="position:absolute;top:35px;left:755px">URL change</div>

---

## The bookshop architecture

.appear[(and Mendix WebModeler architecture)]

---

```javascript
import { Provider } from "mobx-react"

const shop = new ShopStore(window.fetch)
window.shop = shop // for demo / debug

ReactDOM.render(
    <Provider shop={shop}>
        <App />
    </Provider>,
    document.getElementById('root')
)
```

---

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


```javascript
import { when } from "mobx"

const bookFetcher = () => Promise.resolve(/* Fixed data set */)

test("It should start loading books when starting the app", () => {
    const shop = new ShopStore(bookfetcher)
    expect(shop.bookStore.isLoading).toBe(true)

    when(
        () => !shop.isLoading,
        () => {
            expect(shop.books.size).toBe(4)
        }
    )
})
```

---

class: fullscreen

<img src="img/routing6.png" />

<div style="position:absolute;top:35px;left:755px">URL change</div>

---

## If view is to be purely .box3[derived] from the .box1[state], then routing should affect our .box1[state], not the .box4[component tree]

---

class: fullscreen

<img src="img/routing7.png" />

<div style="position:absolute;top:35px;left:55px">URL change</div>

---

```javascript
<Route path="/books/" component={ BookList } />
```

becomes

```javascript
<Route path="/books/" onEnter={ () => shop.openBooksPage() } />
```
---

## Capturing navigation state

---

```javascript
class ViewStore {
    @observable page = "books"
    @observable selectedBookId = null
}
```

---

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
        this.shop.bookStore.fetchBookDetails(id)
    }
}
```

---

## Testing user flow

---

```javascript
it('as a user I can buy books', (done) => {
    const shop = new ShopStore(bookFetcher)

    shop.view.openBooksPage()
    when(
        () => !shop.isLoading,
        () => {
            expect(shop.books.size).toBe(4)

            shop.view.openBookPageById("978-1423103349")
            when(
                () => !shop.isLoading,
                () => {
                    expect(shop.view.selectedBook.name).toBe("The Sea of Monsters")
                    shop.cart.addBook(shop.view.selectedBook)

                    shop.view.openCartPage()
                    expect(shop.cart.canCheckout).toBe(true)

                    shop.cart.checkout()
                    expect(shop.cart.canCheckout).toBe(false)
                    done()
                }
            )
        }
    )
})
```

---

class: fullscreen

<img src="img/napoleon5.jpg" />

---

## Rendering

---

```javascript
const App = ({ shop }) => (
    <div className="App">
        <AppHeader />
        {shop.isLoading
            ? <h1>Loading...</h1>
            : renderContents(shop.view)
        }
    </div>
))

function renderContents(viewStore) {
    switch(viewStore.page) {
        case "books":
            return <Books />
        case "book":
            return <BookDetails book={viewStore.selectedBook} />
        // etc...
    }
}

export default inject("shop")(observer(App))
```

???

.appear[Bye, `componentDidMount`!]

---

```javascript
const BookDetails = ({book, shop}) => (
    <section className="Page-book">
        <h2>{book.name}</h2>
        <p><i>By: {book.author}</i></p>
        <p>Price: {book.price} €</p>
        <button
            onClick={() => {
                shop.cart.addBook(book);
            }}
        >
            Add to cart
        </button>
    </section>
)))

export default inject("shop")(observer(BookDetails))
```

---

```javascript
const BookEntry = inject("shop")(observer(({book, shop}) => (
  <li>
    <a
      href={`/book/${book.id}`}
      onClick={(e) => {
        e.preventDefault();
        shop.view.openBookPage(book);
        return false;
      }}
    >{book.name}</a>
  </li>
)))
```

---

## Routing

---

```javascript
import { Router } from "director"

// React to route changes
new Router({
    "/book/:bookId": (bookId) => shop.view.openBookPageById(bookId),
    "/cart": shop.view.openCartPage,
    "/": shop.view.openBooksPage
}).configure({
    html5history: true
}).init()
```

---

```javascript
class ViewStore {
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

```javascript
import { reaction } from "mobx"

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

class: fullscreen

# Separating state and view

* Enables independent development of stores and components
* Makes user flow testing a breeze

1. Capture crucial app state in a browser unaware store
2. UI just renders this state
3. Don't make state dependent on component hooks
4. Routing & history is just another set kind of actions / reactions

.appear[
<img src="img/napoleon4.jpg" />
]

---

# More info

* [Demo repository](https://github.com/mweststrate/react-mobx-shop/tree/react-amsterdam-2017/src)
* [Workshop @Xebia, June 1st](https://training.xebia.com/developer-skills/building-real-life-apps-with-react-mob/)
* [Blog: How to decouple state and UI (a.k.a. you don’t need componentWillMount)](https://medium.com/@mweststrate/how-to-decouple-state-and-ui-a-k-a-you-dont-need-componentwillmount-cc90b787aa37#.q5ksxr1is)
* [MobX fundamentals, free egghead course](https://egghead.io/courses/mobx-fundamentals)

---

## Q: Is it ok to still have some component state?

.appear[A: Yes]

---

## Q: Will this architecture work with Redux?

.appear[A: Yes]

---