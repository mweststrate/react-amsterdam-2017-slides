# MobX:

1. Find minimal set of .box1[observables] that expresses the state of your app
2. .box2[Derive all other things] from the minimal state
3. MobX keeps derivations in sync using runtime analysis

---

1. Super simple to write

_Focus on what you want to achieve. Ignore how it should affect your application. It will be derived._

---

...Leads to productivity

![productive](img/productive.png)

---

2. Eliminates bugs related to cascading updates

_If the family name changes, don't forget to update spouse name..._

---

3. Eliminates bugs related to staleness

_If the spouce name changes, should be reflected on screen..._

---

4. Fine grained updates perform much better then event based updates

_Don't notify components rendering persons, only those which will be affected by this change..._

---

![fast](img/fast.png)

.appear[
# Q & A time!
]
---

Demo: https://jsfiddle.net/mweststrate/wv3yopo0/

---

Demo: WebModeler

---

# How does this relate to RxJS?

* .appear[With MobX one reacts to _values_, not to _events_]
* .appear[MobX does decide to which values. No manual subscribing to `husband$`, `firstName$` etc...]
* .appear[Events stream are powerfull to control _actions_ or _reactions_, throttling etc!]

---

# How is this different to Angular / Meteor / Ember?

* .appear[Generic applicable, not bound to framework]
* .appear[No digest loop, but mutation broadcasting (efficient!)]
* .appear[Predictable: synchronous, glitch-free, optimized]

---

# Redux?

* .appear[Fine grained updates (fast) vs. coarse grained (snapshots, immutability)]
* .appear[Unopionated vs. opionated]
* .appear[Keep an eye on `mobx-state-tree` & React-Europe!]
