<img src="img/mobx2.png" height="80px" />

## Next Generation State Management

<small>
React Europe 2017

Michel Weststrate - @mweststrate
</small>
<br/><br/>

<img src="img/mendix-logo.png" height="80px" /><br/>

---


Dead tree

???
Not these trees, but actual living trees like this

If you havent been outside for a while, and your like; I have

seen these things before. Your right, they were featured in lord of the rings. That's where you might have seen them before.

But lets first talk about state management first


---

Living tree

---

`redux || mobx`

.appear[
    `true`
]

???

There is often a heated debate. I am no neutral party in the discussion, but lets recap the two strategies in general. And I oversimplify and generalize to do that

---

State management; recap

---

## Mutable graphs of instances

---

![graph1](img/graph1.png)

---

![graph2](img/graph2.png)

---

![graph3](img/graph3.png)

---

## Immutable trees of data

---

![redux](img/redux1.png)

---

![redux](img/redux2.png)

---

![redux](img/redux3.png)

---

| |  Immutable Trees | Mutable Model Graphs |
| --- | ---- | --- |
| .appear[Cheap snapshots] | <i class="em em-white_check_mark"></i>  | |
| .appear[Cheap patches] | | <i class="em em-white_check_mark"></i> |
| .appear[Tree structure] | <i class="em em-white_check_mark"></i> | |
| .appear[Graph structure] | | <i class="em em-white_check_mark"></i> |
| .appear[Hydration] | <i class="em em-white_check_mark"></i> | |
| .appear[Co-location of operations and data] | | <i class="em em-white_check_mark"></i> |
| .appear[Protected against unintended modifications] | <i class="em em-white_check_mark"></i> | |
| .appear[Replayable actions] | <i class="em em-white_check_mark"></i> | |
| .appear[Straight forward operations] | | <i class="em em-white_check_mark"></i> |
| .appear[Devtools] | <i class="em em-white_check_mark"></i> | |
| .appear[Static typing / analysis] | | <i class="em em-white_check_mark"></i> |
| .appear["Stable" references] | <i class="em em-white_check_mark"></i> |  |
| .appear["Stable" references] | | <i class="em em-white_check_mark"></i> |

---

Can we have both?

.appear[mobx-state-tree: The living tree]

.appear[img eva]

???

Living trees are awesome. Unless you eat from the wrong one. Then you die.

But that is a feature in itself. I will elaborate on that later.

---

Snapshots are awesome

.appear[What if I could just modify some state, and get a snapshot for free?]

???

Snapshots are awesome; like taxes are awesome. I like the idea because all the benefits it yields. I just don't like to pay

So what if..

.. structural sharing is mandatory

---

```javascript
class Todo {
    title: string = "Get Coffee"
    done: boolean = false

    toggle() {
        this.done = !this.done
    }
}

class Store {
    todos: Todo[] = []
}
```

---

```javascript
class Todo {
    title: string = "Get Coffee"
    done: boolean = false

    toggle() {
        this.done = !this.done
    }

    get snapshot() {
        return {
            title: this.title,
            done: this.done
        }
    }
}

class Store {
    todos: Todo[] = []

    get snapshot() {
        return {
            todos: this.todos.map(todo => todo.snapshot)
        }
    }
}
```

---

```javascript
class Todo {
    @observable title: string = "Get Coffee"
    @observable done: boolean = false

    toggle() {
        this.done = !this.done
    }

    @computed get snapshot() {
        return {
            title: this.title,
            done: this.done
        }
    }
}

class Store {
    @observable todos: Todo[] = []

    @computed get snapshot() {
        return {
            todos: this.todos.map(todo => todo.snapshot)
        }
    }
}
```

---

Img

---

```javascript
const history = []
const store = new Store()

reaction(
    () => store.snapshot,
    snapshot => history.push(snapshot)
)
```

---

Plaatje

---

Tick: cheap snapshots

[Img: tree -> seed]

---

New problems

* `get snapshot` is boilerplate method
* How to get back from snapshot to a real `Store`?
* Cycles are tricky

[Img: seed -> tree]

.appear[Type information]
.appear[* Design time + code generator]
.appear[* Run time]

.appear[Mattia & Ganti]

---

MobX-state-tree

---

Tree with living data.

Contents (state) may vary
Shape is consistent (type information)

---

Why a tree

Trees can be traversed in predicatable, finite order

---


Planting a seed

.appear[
```javascript
import {types, getSnapshot} from "mobx-state-tree"

const Type = types.model(properties, actions)
```
]

.appear[
```javascript
const instance = Type.create(snapshot)
```
]

.appear[
```javascript
const snapshot = getSnapshot(instance)
```
]

---

```javascript
const Todo = types.model({
    title: types.string,
    todo: false
}, {
    toggle() {
        this.done = !this.done
    }
})

const Store = types.model({
    todos: types.array(Todo)
})
```

---

```javascript
const store = Store.create({
    todos: [{
        title: "Get coffee"
    }]
})
```
.appear[
```javascript
store.todos[0].toggle()
```
]

---


Enting a tree

[ Tree picture ]

???

Promised to talk about real trees

---

store.todos[0] = { title: "Prep slides" }
store.todos[0].toggle()

???

When modifying the tree at any point, type is infered from the typesystem and applied

---

Tick: (De) Hydration
Tick: Colocation of actions

[Img: seed -> tree]

???

Can freely convert snapshots to state trees and vice versa

---

Redux todos demo

---

Tick: DevToos

---

Actions

* Nodes can only be modified through actions
* Actions can only modify own subtree
* Middleware support
* Replayable
* Method invocation _produces_ action description

---

```javascript
store.todos[0].done = false

// TODO: exception
```

---

```javascript
onAction(store, (action, next) => {
    console.dir(action)
    return next()
})
store.todos[0].toggle()

// prints:
{
    "name":"toggle",
    "path":"/todos/0",
    "args":[]
}
```

---

Use case: user modifies data tree throug wizard

plaatje

---

```javascript
import { clone } from "mobx-state-tree"

const tempStore = clone(store)
```

.appear[
```javascript
function clone(x) {
    return getType(x).create(getSnapshot(x))
}
```
]

---


```javascript
import { clone, recordActions } from "mobx-state-tree"

const tempStore = clone(store)
const recorder = recordActions(tempStore)
```

.appear[
```javascript
...user modifies the tempStore in a wizard...
```
]

.appear[
```javascript
recorder.replay(store)
```
]

---

Tick: straightforward actions

---

Tick: protection

---

Tick: replayable actions

???

Actually, if you check out the sources, you will notice the original reducer unit tests are still in there

---

Strong typing

[img compile error]
[img autocomplete]

???

Type system that does not only work at runtime, it also works at design time

---


---

Tree semantics

Live; solve more fundamental problem

---


# References

```javascript
function logTodo(todo: Todo) {
    setTimeout(
        () => console.log(todo.title),
        1000
    )
}
```

|  Immutable Trees | Mutable Model Graphs |
| --- | ---- | --- |
| `title` won't have changed | `title` might have changed |
| `title` will probably be stale | `title` will be latest* |

???

---

```
BookStore {
    books: Books[]
}

Cart {
    entries: BookEntry[]
}

BookEntry {
    amount: number
    book: Book
}
```

1. Fetch the books
2. Cart view should be consistent
3. No `book` reference to a detached book
   * Immutable tree: normalize; symbolic ref
   * Mutable graph: make sure `Book` instance is recycled during update

---

* MST ensures nodes are reconciled
* MST throws when trying to use orphaned objects
  * Immutability defends against accidental modifications
  * MST also defends against accidental stale reads

"Death is a feature" (Elrond in a LOTR movie)

---

CODE

---

# First class support for references

```javascript
const BookEntry = types.model({
    amount: types.number,
    book: types.reference(Book)
})
```

---

Tree, that can be used as graph

Tick graph

---

Other cool features not fitting in this talk:

* Patches
* Middleware
* Full MobX compatibility




---

Summary

---

Try to go home early tweet

???

True for MobX, mobx-state-tree tries to take that idea to the next level

---

Release

