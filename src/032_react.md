
# MobX & React

* `observer` from `mobx-react` package
* roughly: `autorun(() => this.render())`
* plus: `PureRenderMixin` as `shouldComponentUpdate`!
* .appear[Changes inside objects will be observed by MobX]
* .appear[React only needs to act on new props]

---

# MobX & React tips

* Every component that renders observables should have `observer`
* Put `observer` everywhere
* More observers &rarr; smaller components &rarr; faster app
* Prefer passing objects over primitives

---

## Quiz: which components will react?

---

```javascript
const user = observable({
    name: "John"
})

const App = observer(user => (
    <Label text={user.name} />
))

const Label = observer(text => (
    <label>{text}</label>
))

React.render(<App user={user} />)

user.name = "Jane"
```

.appear[App, then Label]

---

```javascript
const user = observable({
    name: "John"
})

const App = observer(user => (
    <User user={user} />
))

const User = observer(user => (
    <label>{user.name}</label>
))

React.render(<App user={user} />)

user.name = "Jane"
```

.appear[just User]

---


```javascript
const user = observable({
    name: "John"
})

const App = observer(() => (
    <User />
))

const User = observer(() => (
    <label>{user.name}</label>
))

React.render(<App />)

user.name = "Jane"
```

.appear[just User]

---

```javascript
const user = observable({
    name: "John"
})

const App = observer(user => (
    <SuperCoolWidget onRender={
        () => <label>{user.name}</label>
    }/>
))

React.render(<App user={user} />)

user.name = "Jane"
```

.appear[None!]

.appear[
```javascript
() => <Observer><label>{user.name}</label></Observer>
```
]
