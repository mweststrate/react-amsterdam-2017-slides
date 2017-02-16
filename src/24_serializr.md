## Serializr..

_Serialize and deserialize complex object graphs to JSON_

---

```javascript
class User {
    @serializable(identifier())
    uuid = Math.random();

    @serializable
    displayName = "John Doe";
}

class Message {
    @serializable
    message = "Test";

    @serializable(object(User))
    author = null;

    @serializable(list(object(Message)))
    comments = [];
}
```

---

```javascript
const message = deserialize(Message, {
    message : "Hello world",
    author  : {uuid: 1, displayName: "Alice"},
    comments: [
        {
            message: "Welcome!",
            author : {uuid: 1, displayName: "Bob"}
        }
    ]
});

console.log(message instanceof Message)
// true

console.log(serialize(message))
// json
```