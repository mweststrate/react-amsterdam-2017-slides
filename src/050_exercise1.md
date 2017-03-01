## The book shop exercise

* Demo
* Create-react-app based
* [custom react scripts](https://github.com/kitze/custom-react-scripts) or [rewired](https://github.com/timarney/react-app-rewired/tree/master/packages/react-app-rewire-mobx) for decorators
* HTML Mockup in [0_base branch](https://github.com/mweststrate/react-mobx-shop/tree/0_base)
* Running reference implementation: http://book-shop-2017-02-17.surge.sh/
* Tip: https://mobx.js.org/ !

---

## Exercise 1: Fetch data, test, render

* Design data model / book store in `stores/` folder
* Fetch books from `public/books.json`
* Render data from the store (sorted)
* Design questions: classes? references? decorators?
* Verify with UI-unaware tests
* Render loading state
* [Solution](https://github.com/mweststrate/react-mobx-shop/compare/0_base...1_fetch)

---

```
git clone git@github.com:mweststrate/react-mobx-shop.git
git checkout 0_base
yarn install
yarn start
```

Discuss exercise 1