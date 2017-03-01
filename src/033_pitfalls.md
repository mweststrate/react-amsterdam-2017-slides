

## Pitfalls

* .appear[Forgotten `observer`]
* .appear[observable: `mobx` package]
* .appear[observer: `mobx-react` package]
* .appear[Passing observables to external non-observer components]
* .appear[Use `observable.map` for dynamic keyed stuff]
* .appear[`slice()` observable arrays before passing to external tools]
* .appear[`computed` values are suspended if not in use]
* .appear[Updating values in reaction? use `computed` instead!]
* .appear[use _modifiers_ to disable auto conversion]

---

## Modifiers

```javascript
const store = observable({
    todos: [
        title: "get coffee",
        done: false
    ]
})

store.todos[1] = {
    title: "get cookie",
    done: true
}

console.log(isObservable(store.todos[1])) // True
```

---

## Modifiers

```javascript
class Box {
    set(v) {
        this.value = observable(v) /* auto convert */
        this.observers.forEach(o => o.run())
    }
}
```

---

## Modifiers

* default `deep`: recursively convert & clone plain, complex values
* `ref`: never convert values
* `shallow`: convert maps, arrays, objects. Don't recurse into values
* Use `ref` / `shallow`  to store objects not managed by your code
* Class instances are never converted &rarr; responsiblity of constructor

---

## Modifiers

```javascript
class Class {
    @observable.ref prop = complex value
}
```

---

## Non decorator syntax overview

```javascript
extendObservable(object, {
    prop1: value

    prop2: observable.ref(value),
    action1: action(function(v) {
        this.prop1 = v
    }),

    get computed1() {
        return this.prop1
    }
    set computed1(v) {
        this.prop1 = v
    }
})
```