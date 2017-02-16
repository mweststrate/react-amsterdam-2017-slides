# Async stuff

.appear[Async = two synchronous actions at different points in time]

.appear[Track progress with observables]

.appear[Nothing special about it]

---

# Async stuff

```javascript
@observable users = []
@observable requestCount = 0

@action.bound requestUsers() {
    this.requestCount++
    window.fetch("https://randomuser.me/api/?results=3")
        .then(r => r.json())
        .then(action(data => {
            this.users.push(...data.results)
            this.requestCount--
        }))
}
```
---

# Async stuff

https://jsfiddle.net/mweststrate/a8az1nnj/3/

---

# Async: fromPromise

```javascript
@observable users

@action.bound requestUsers() {
    this.users = fromPromise(
        window.fetch("https://randomuser.me/api/?results=3")
        .then(r => r.json())
        .then(data => data.results)
    )
}
```

---

# fromPromise creates observable promise wrapper

```javascript
class observablePromise<T> {
    @observable state: string
    @observable value: T
    case<R>({fulfilled, rejected, pending}): R
}
```

---

# Async: fromPromise

```javascript
<div>
    { this.users.case({
        "pending":   () => "loading",
        "rejected":  (e) => "error: " + e,
        "fulfilled": (users) => users.map(user =>
            <div key={user.login.username}>
                <img src={user.picture.thumbnail} />
                {user.email}
            </div>
        )
    }) }
</div>
```

---

# Async: fromPromise

* Part of `mobxUtils` package
* https://jsfiddle.net/mweststrate/3LvnpLpg/
