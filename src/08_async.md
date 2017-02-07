# Async stuff

.appear[Async = two synchronous actions at different points in time]

.appear[Track progress with observables]

---

# Async stuff

```javascript
@observable users = []
@observable requestCount = 0

@action.bound requestUsers() {
    this.requestCount++
    window.fetch("https://randomuser.me/api/?results=3")
        .then(r => r.json())
        .then(data => {
            this.users.splice(0, 0, ...data.results)
            this.requestCount--
        })
}
```
---

# Async stuff

https://jsfiddle.net/mweststrate/a8az1nnj/3/

---

# Async: last wins

```javascript
.then(data => {
    this.users = data.results // doesn't guarantee last fired request wins!
    this.requestCount--
})

```
---

# Async: fromPromise

```javascript
@observable users

@action.bound requestUsers() {
    this.users = fromPromise(window.fetch("randomuser.me")
        .then(r => r.json())
        .then(data => data.results)
    )
}
```

---

# Async: fromPromise

```javascript
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
```

---

# Async: fromPromise

* Part of `mobxUtils` package
* https://jsfiddle.net/mweststrate/3LvnpLpg/
