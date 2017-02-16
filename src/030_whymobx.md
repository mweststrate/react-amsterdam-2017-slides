# MobX:

1. Find minimal set of .box1[observables] that expresses the state of your app
2. .box3[Derive all other things] from the minimal state
3. MobX keeps derivations transparently in sync using runtime analysis

---

1 - Super simple to write

_Focus on what you want to achieve. Ignore how it should affect your application. It will be derived._

---

...Leads to productivity

![productive](img/productive.png)

---

2 - Increases decoupling

_If you change one fact, you don't have to think what other facts need to be updated and what side effects should be triggered_

https://www.youtube.com/watch?v=Aws40KOx90U

---

2 - Eliminates bugs related to cascading updates

_If the family name changes, don't forget to update spouse name..._

---

3 - Eliminates bugs related to staleness

_If the name of the spouse changes, should be reflected on screen..._

---

4 - Easy interoperability & freedom of architecture

_(Also a down-side)_

---

4 - Fine grained updates perform much better then event based updates

_Don't notify all components rendering persons, only those which will be affected by this change..._

.appear[
![fast](img/fast.png)
]

---

5 - Optimal tracking is only feasible if tracking is transparent

```javascript
@computed get fullName() {
    return this.firstName + " " +
        (this.husband ? this.husband.lastName: this.familyName)
}
```

tracks:

.appear[
```
firstName                                    firstName
husband                  -OR-                husband
husband.lastName                             familyName
```
]

---

## Break