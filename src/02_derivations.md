## 1. Thinking in derivations

---

# Most complex things in programming?

1. .appear[Caching]
2. .appear[Naming things]

---

# Most complex things in life?

2. .appear[The Dutch Tax System]
1. .appear[Marriage]

---

# Dutch Tax System: 3 Boxes

* box 1: belastbaar inkomen uit werk en woning
* box 2: belastbaar inkomen uit aanmerkelijk belang
* box 3: belastbaar inkomen uit sparen en beleggen

---

# Dutch Tax System: 3 Boxes

If you have income in multiple boxes, this income is processed separately: Different rates apply for the taxable income in box 1, 2 and 3. The rates are equal to the rates that apply for residents in the Netherlands <i>(only available in Dutch)</i>.

---

# Dutch Tax System: 3 Boxes

TL;DR: Put money in the wrong box, and all your money are belong to us.

---

# Apps: 3 Boxes

---

<table width="100%">
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">Box 4: Modify data</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Box 1: The data your app is about</div>
        </td>
        <td class="box-computed">
            <div class="appear">Box 2: Things that could be computed from data</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Box 3: Freaking side effects</appear>
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
            <div class="appear">Box 4: Users enters data</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Box 1: Data Cells</div>
        </td>
        <td class="box-computed">
            <div class="appear">Box 2: Formulas</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Box 3: Draw on screen</appear>
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

* .appear[Fights]
* .appear[Divorce]
* .appear[Getting broke]
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

* `familyName` .appear[state]
* `husband` .appear[state]
* `lastName` .appear[derived value]
* assigning new husband .appear[action]
* /me crying in corner .appear[reaction]

---

# MobX

Makes sure changes in 'Data cells' get reflected in Box 2 & 3

---

<table width="100%">
    <tr>
        <td rowspan="2" class="box-actions">
            <div class="appear">Box 4: Actions</appear>
        </td>
        <td rowspan="2" class="box-state">
            <div>Box 1: Observable values</div>
        </td>
        <td class="box-computed">
            <div class="appear">Box 2: Computed values</appear>
        </td>
    </tr>
    <tr>
        <td class="box-reactions">
            <div class="appear">Box 3: Reactions</appear>
        </td>
    </tr>
</table>

---

# MobX api:

* Box 1: `@observable`
* Box 2: `@computed`
* Box 3: `observer`, `autorun`, `reaction`, `when`
* Box 4: `@action`

---

Demo:

http://jsbin.com/lusoqe/1/edit?js,console

---

classname: fullscreen

```javascript
const {observable, reaction, autorun} = mobx;

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

reaction(
  () => elise.husband === michel,
  (married) => console.log(married ? "Michel happy" : "michel crying")
)

autorun(() => {
  console.log(elise.fullName)
})
```

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
```
.appear[
```javascript
class Todo {
    @observable title: string
    @observable selected: boolean
}
```
]

---

# Exercise: Multi selection

```javascript
class TodoStore {
    @observable todos: Todo[]
    @observable selectedTodos: Todo[]
}
```
.appear[
```javascript
class Todo {
    @observable title: string
    @computed selected: boolean
}
```
]

---

Note, using decorators is optional!