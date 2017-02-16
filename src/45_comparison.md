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
