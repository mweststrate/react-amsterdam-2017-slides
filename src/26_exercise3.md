## Exercise 3:

1. Store cart in local storage
2. Handle receiving updates from server

* Re-fetch books each 5 seconds
* Update name / author / price in `books.json`
* Add / remove books
* Handle updated / removed books in cart
* Solution: [local storage](https://github.com/mweststrate/react-mobx-shop/compare/3_inject...4_localstorage), [server updates](https://github.com/mweststrate/react-mobx-shop/compare/4_localstorage...5_serversync)

---

Discuss exercise 3

---

# Alternative: normalize-behind-the-scenes

```javascript
class BookEntry {
    private bookId: string

    @computed get book() {
        return this.shop.books.get(this.bookId)
    }
    set book(book) {
        this.bookId = book.id
    }
}
```
