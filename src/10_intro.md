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

## Complexity: Divide and Conquer!

---

# The MobX philosophy

---

<table width="100%">
    <tr>
        <td>Input</td>
        <td>State</td>
        <td>Output</td>
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">Box 2: Modify data</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Box 1: The data your app is about</div>
        </td>
        <td class="box-computed">
            <div class="appear">Box 3: Data that could be computed from other data</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Box 4: Effects that should happen on state changes</appear>
        </td>
    </tr>
</table>

---

# Why?

<img src="img/excel.png" height="150px" />

* .appear[Pattern used in the most successful software product]
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

# Thinking in mimimal state

---

# Imperative updates

```javascript
class Store {
    todos = []
    completedTodos = []

    updateCompletedTodos() {
        this.completedTodos = this.todos.filter(t => t.done)
    }

    toggleTodo(i) {
        this.todos[i].done = !this.todos[i].done
        this.updateCompletedTodos()
    }
}
```

---

# Mweh...

```javascript
    addTodo(todo) {
        this.todos.push(todo)
        this.updateCompletedTodos()
    }

    removeTodo(idx) {
        this.todos.splice(idx, 1)
        this.updateCompletedTodos()
    }
```

---

# Blegh...

```javascript
    onReceiveTodos(data) {
        data.forEach(item => {
            // make sure not to update completed list, kills performance!
            this.addTodo(new Todo(item), { updateCompleted: false })
        })
        this.updateCompletedTodos()
    }
```

<!-- contrived example, in ReactNext talk I demonstrate that these kind of updates increase exponentially in complexity if the amount of features increases incrementally -->
---

# Better..

```javascript
    get completedTodos() {
        return this.todos.filter(t => t.done)
    }


    addTodo(todo) {
        this.todos.push(todo)
    }
```

---

# But..

* .appear[... How get `completedTodo` users notified? Some pub / sub mechanism?]
* .appear[We need _reactivity_]

---

# MobX

Makes sure .box1[data] is always, automatically and efficiently reflected in .box3[derived values] and that necessary .box4[side effects] are fired.

---

<table width="100%">
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">Box 2: Actions</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Box 1: Observable values</div>
        </td>
        <td class="box-computed">
            <div class="appear">Box 3: Computed values</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Box 4: Reactions</appear>
        </td>
    </tr>
</table>

---

# MobX api

* Box 1: .box1[`@observable`]
* Box 2: .box2[`@action`]
* Box 3: .box3[`@computed`]
* Box 4:  .box4[`autorun`, `observer`, `reaction`, `when`]

---

# MobX

```javascript
    @computed get filteredTodos() {
        return this.todos.filter(t => t.done)
    }

    @action addTodo(todo) {
        this.todos.push(todo)
    }
```

---

# What problem did we solve?

```
view = f(state)
```

* .appear[React to state changes inside components &rarr; React]
* .appear[React to state changes outside components &rarr; MobX]

---

# Decoupling components from state


# Making the UI dumb

* The less logic lives in components, the easier your app is to test
* Portable components and domain logic


* .appear[We can now decouple state from components
  - Reuse
  - Testing
]

* local, volatile component state is ok

<!--  (At mendix most of our state is seperately packaged from UI) -->

---

# Book store demo

<!--

Code: some render compnents +
Code: store

Demo: bookshop,
modify some data
-->
---

# Are you really decoupling your?

.appear[
```
xxxx = xxxx(state)
```
]

---

If the view is just a derived value. Does our app still work without view?

---

* Does our app still function?
  - Is our data still loaded?
  - Can we still 'click' a book to navigate to a next page?
  - Can we still check our inputs?
* Can we run our app without browser (simulator)?
* Can all our components be (practdically) stateless?
* Goal: headless testing
* Goal: reuse across technology stacks

---
Can we build an app entirely out of stateless function components?

---

Yes we can!

---


live Kill `React.render()`

---

```javascript
class Books {
    componentDidMount() {
        this.setState({ loading: true })
        this.props.store.fetchBooksSomehow(books => {
            this.setState({
                loading: false,
                books: books
            })
        })
    }

    render() {
        // stuff
    }
}

---

Fetch in `ComponentDidMount`. Why?!

Component needs books

---

Why is component there?

1: A state transation of the parent caused component to be present
-> Then books should be fetched as result of the state transation

---

Why is component there?

2: Router mounted it
-> Books should be fetched as result of route change

---

Current route is state

Not treating routing as state introduces an orthogonal lifecycle forces non-view responsibilities to be managed by components

----


Components need data
  - Pass in props
  - Initiate data fetch.
  - state = f(view) ?!
  - componentWillReceiveProps?
  - How do I otherwise know when to start data fetching?
  _ Well, you know when to render that component. The cause for that should also cause the data fetching
  - In other words. Data fetching is the result of a state transition. Note the result of some obscure VDOM lifecycle hook

Why is the component there? Cause Router mounted it
  - Ah, there is secretly state in the router
  - Can we get it out?



---

Webmodeler test example

Reduce complexity by:

reducing state, deriving as much as possible