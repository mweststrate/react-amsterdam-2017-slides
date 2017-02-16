# MobX

Simple & Scalabe State Management

Transparent Reactive Programming Library

---


## Thinking in derivations

---

# Dutch Tax System: 3 Boxes

* .box1[box 1]: belastbaar inkomen uit werk en woning
* .box2[box 2]: belastbaar inkomen uit aanmerkelijk belang
* .box3[box 3]: belastbaar inkomen uit sparen en beleggen

---

# Dutch Tax System: 3 Boxes

If you have income in multiple boxes, this income is processed separately: Different rates apply for the taxable income in box 1, 2 and 3. The rates are equal to the rates that apply for residents in the Netherlands.

---

# Dutch Tax System: 3 Boxes

TL;DR: Put money in the wrong box, and all your monies are belong to us.

---

# Apps: 3 Boxes

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

# Exercise: Marriage

```javascript
const elise = {
    firstName: "Elise",
    familyName: "Hagendoorn",
    fullName: // It depends,
    husband: // on him. maybe.
}
```

---

# Exercise: Marriage

```javascript
function setHusband(wife, husband) {
    wife.husband = husband
    wife.lastName = husband ? husband.lastName : wife.familyName
}
```

---

# Marriage, what could possibly go wrong?

.appear[
```javascript
function setHusband(wife, husband) {
    wife.husband = husband
    wife.lastName = husband ? husband.lastName : wife.familyName
}
```
]

* .appear[Getting broke]
* .appear[Fights]
* .appear[Divorce]
* .appear[Husband changes last name]

---

# Marriage, what could possibly go wrong?

How to fix marriage:

* .appear[Keep using own name]
* .appear[Or derive name]

---

# Exercise: Marriage

```javascript
const elise = {
    firstName: "Elise",
    familyName: "Hagendoorn",
    get fullName() {
        return this.firstName + " " +
            (this.husband ? this.husband.lastName: this.familyName)
    },
    husband: null
}
```

---

# Exercise: Marriage

* .appear[.box1[familyName]]
* .appear[.box1[husband]]
* .appear[.box3[lastName]]
* .appear[.box2[assigning new husband]]
* .appear[.box4[/me going to cry in corner]]

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

# MobX api:

* Box 1: .box1[`@observable`]
* Box 2: .box2[`@action`]
* Box 3: .box3[`@computed`]
* Box 4:  .box4[`autorun`, `observer`, `reaction`, `when`]

---

```javascript
const {observable} = mobx;

const elise = observable({
  firstName: "Elise",
  familyName: "Hagendoorn",
  husband: null,
  get fullName() {
  	return this.firstName + " " +
      (this.husband ? this.husband.lastName: this.familyName)
  }
})

const michel = observable({
  firstName: "Michel",
  lastName: "Weststrate"
})
```

---

```javascript
const {observable, reaction, autorun} = mobx;

autorun(() => {
  console.log(elise.husband === michel ? "Michel happy" : "Michel crying")
})

autorun(() => {
  console.log(elise.fullName)
})
```

---

[Demo](http://jsbin.com/lusoqe/1/edit?js,console)

---

# Exercise: Multi selection

```javascript
class TodoStore {
    todos: Todo[]
    selectedTodos: Todo[]
}

class Todo {
    title: string
    selected: boolean
}
```

---

# Exercise: Multi selection

```javascript
class TodoStore {
    @observable todos: Todo[]
    @computed selectedTodos: Todo[]
}

class Todo {
    @observable title: string
    @observable selected: boolean
}
```

---

# Exercise: Multi selection

```javascript
class TodoStore {
    @observable todos: Todo[]
    @computed get selectedTodos() {
        return this.tods.filter(t => t.selected)
    }
}

class Todo {
    @observable title: string
    @observable selected: boolean
}
```

---

# Exercise: Multi selection

```javascript
class TodoStore {
    @observable todos: Todo[]
    @observable selectedTodos: Todo[]
}

class Todo {
    @observable title: string
    @computed get selected() {
        return this.todoStore.selectedTodos.indexOf(this) !== -1
    }
}
```
