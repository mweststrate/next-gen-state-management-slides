<img src="img/mobx2.png" height="80px" />

## Next Generation State Management

<small>
React Europe 2017

Michel Weststrate - @mweststrate
</small>
<br/><br/>

<img src="img/mendix-logo.png" height="80px" /><br/>

---

![tree](img/tree.png)

???
Not these trees, but actual living trees like this

If you havent been outside for a while, and your like; I have

seen these things before. Your right, they were featured in lord of the rings. That's where you might have seen them before.

But lets first talk about state management first


---

class: fullscreenw

![tree](img/tree.jpg)

---

class: fullscreen

![tree](img/treebeard.png)

---

`redux || mobx`

.appear[
    `true`
]

???

There is often a heated debate. I am no neutral party in the discussion, but lets recap the two strategies in general. And I oversimplify and generalize to do that

---

## state management; a recap

---

## mutable graphs of class instances

---

![graph1](img/graph1.png)

---

![graph2](img/graph2.png)

---

![graph3](img/graph3.png)

---

## immutable trees of data

---

![redux](img/redux1.png)

---

![redux](img/redux2.png)

---

![redux](img/redux3.png)

---

class: ticks2

<p style="position:relative;left: 106px;font-style:italic;">
    <span style="font-size: 0.6em">Immutable Trees &nbsp;&nbsp;&nbsp; Model Graphs<span>
</p>

.tick1.appear[cheap snapshots<i class="em em-white_check_mark"></i>]
.tick2.appear[fine grained updates<i class="em em-white_check_mark"></i>]
.tick1.appear[tree structure<i class="em em-white_check_mark"></i>]
.tick2.appear[denormalized data<i class="em em-white_check_mark"></i>]
.tick1.appear[easy hydration<i class="em em-white_check_mark"></i>]
.tick2.appear[co-location of actions<i class="em em-white_check_mark"></i>]
.tick1.appear[protection of data<i class="em em-white_check_mark"></i>]
.tick1.appear[replayable actions<i class="em em-white_check_mark"></i>]
.tick2.appear[straight forward actions<i class="em em-white_check_mark"></i>]
.tick1.appear[devtool support<i class="em em-white_check_mark"></i>]
.tick2.appear[static analysis / typing<i class="em em-white_check_mark"></i>]
.tick1.appear[predictable references<i class="em em-white_check_mark"></i>]
.tick2.appear[predictable references<i class="em em-white_check_mark"></i>]

???

Why a tree Trees can be traversed in predicatable, finite order
---

## Can we have both?

---

class: fullscreenw

![tree](img/tree.jpg)

???

Living trees are awesome. Unless you eat from the wrong one. Then you die.

But that is a feature in itself. I will elaborate on that later.

---

class: fullscreenw

![tree](img/eden2.jpg)

---

## Snapshots are awesome

.appear[What if I could get them for free after each mutation?]

???

Snapshots are awesome; like taxes are awesome. I like the idea because all the benefits it yields. I just don't like to pay

So what if..

.. structural sharing is mandatory

---

![img](img/mst1.png)

---

## mobx-state-tree LOGO

*The living tree*

---

class: boxedimg

![img](img/mst3.png)

---

class: boxedimg

![img](img/mst4.png)

---

class: boxedimg

![img](img/mst5.png)

---

class: boxedimg

![img](img/mst6.png)

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
```

---

```javascript
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

```javascript
const history = []
const store = new Store()

reaction(
    () => store.snapshot,
    snapshot => history.push(snapshot)
)
```

---

class: boxedimg

![img](img/todo1.png)

---

class: boxedimg

![img](img/todo2.png)

---

class: boxedimg

![img](img/todo3.png)

---

# But...

* `get snapshot` boilerplate
* How to get back from a seed to a full tree?
* Cycles are tricky

---

class: boxedimg

![img](img/seed1.png)

---

class: boxedimg

![img](img/seed2.png)

???

[Img: seed -> tree]

.appear[Type information]
.appear[* Design time + code generator]
.appear[* Run time]

.appear[Mattia & Ganti]

time for code

---

```javascript
import {types, getSnapshot} from "mobx-state-tree"

const Type = types.model(properties, actions)
```

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
    done: false
}, {
    toggle() {
        this.done = !this.done
    }
})
```

```javascript
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

class: boxedimg

![img](img/seed3.png)

???

Promised to talk about real trees
Enting

---

class: boxedimg

![img](img/seed4.png)

---

```javascript
store.todos[0] = { title: "Prep slides" }
store.todos[0].toggle()
```

???

When modifying the tree at any point, type is infered from the typesystem and applied

---

class: ticks3

<p style="position:relative;left: 106px;font-style:italic;">
    <span style="font-size: 0.6em">Immutable Trees &nbsp;&nbsp;&nbsp; Model Graphs&nbsp;&nbsp;&nbsp; Mobx-State-Tree<span>
</p>

.tick1[cheap snapshots<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2[fine grained updates<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1[tree structure<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2.faded[denormalized data<i class="em em-white_check_mark"></i>]
.tick1[easy hydration<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2[co-location of actions<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1.faded[protection of data<i class="em em-white_check_mark"></i>]
.tick1.faded[replayable actions<i class="em em-white_check_mark"></i>]
.tick2[straight forward actions<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1.faded[devtool support<i class="em em-white_check_mark"></i>]
.tick2.faded[static analysis / typing<i class="em em-white_check_mark"></i>]
.tick1.faded[predictable references<i class="em em-white_check_mark"></i>]
.tick2.faded[predictable references<i class="em em-white_check_mark"></i>]

---

```javascript
import { types } from 'mobx-state-tree'

const Todo = types.model({
    text: 'Learn Redux',
    completed: false,
    id: 0
})

const TodoStore = types.model({
    todos: types.array(Todo),

    findTodoById: function (id) {
      return this.todos.find(todo => todo.id === id)
    }
}, {
    DELETE_TODO({id}) {
      this.todos.remove(this.findTodoById(id))
    }
    // .. and more
})
```

???

why the weird function name?
why 'learn redux'?

Actually, if you check out the sources, you will notice the original reducer unit tests are still in there


---

Demo

???

Devtools

---

## Actions

```javascript
const Todo = types.model({
    title: types.string,
    done: false
}, {
    toggle() {
        this.done = !this.done
    }
})
```

---

# Actions

* Instances can only be modified through actions
* Actions can only modify own subtree
* Replayable
* Method invocation _produces_ action description
* Middleware support

---

# Protection against modifications

```javascript
store.todos[0].done = false
```

.appear[
```
Error: [mobx-state-tree] Cannot modify 'Todo@/todos/0',
the object is protected and can only be modified by using an action.
```
]

---

# Serializable actions

```javascript
onAction(store, (action, next) => {
    console.dir(action)
    return next()
})

store.todos[0].toggle()
```

.appear[
```
{
    "name":"toggle",
    "path":"/todos/0",
    "args":[]
}
```
]

---

## Use case: user modifies complex data tree in a wizard

???

Use case syncing actions over websockets with other clients

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
...user modifies the tempStore in a wizard
And upon commit...
```
]

.appear[
```javascript
recorder.replay(store)
```
]

---

class: ticks3

<p style="position:relative;left: 106px;font-style:italic;">
    <span style="font-size: 0.6em">Immutable Trees &nbsp;&nbsp;&nbsp; Model Graphs&nbsp;&nbsp;&nbsp; Mobx-State-Tree<span>
</p>

.tick1.faded[cheap snapshots<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[fine grained updates<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1.faded[tree structure<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[denormalized data<i class="em em-white_check_mark"></i>]
.tick1.faded[easy hydration<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[co-location of actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[protection of data<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1[replayable actions<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2.faded[straight forward actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[devtool support<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2.faded[static analysis / typing<i class="em em-white_check_mark"></i>]
.tick1.faded[predictable references<i class="em em-white_check_mark"></i>]
.tick2.faded[predictable references<i class="em em-white_check_mark"></i>]

---

## Type System

---

# Runtime type checks

```javascript
Store.create({
   todos: [{
       turtle: "Get tea"
   }]
})
```

.appear[
```
Error: [mobx-state-tree] Value '{\"todos\":[{\"turtle\":\"Get tea\"}]}'
is not assignable to type: Store, expected an instance of Store or a snapshot like
'{ todos: { title: string; done: boolean? }[] }' instead.
```
]

???

Type system that does not only work at runtime, it also works at design time

---

# Design time type checks

![img](img/tserror.png)

---

class: fullscreenw

![tree](img/eden2.jpg)

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
| .appear[`title` won't have changed] | .appear[`title` might have changed] |
| .appear[`title` will probably be stale] | .appear[`title` will not be stale] |

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

1. .appear[Fill a cart]
2. .appear[Refresh books from server]
3. .appear[Cart view should be consistent]
   * .appear[Immutable tree: normalize; symbolic ref]
   * .appear[Mutable graph: recycle `Book` instances]

???

Or use a mutable graph with symbolic references to get best of both world, but that is a different story

---

# Livelyness guarantees

* .appear[Reconcily instances automatically]
* .appear[Throw when trying to use orphaned objects]
* .appear[Defends against accidental stale reads]

???

"Death is gift to men" (Elrond in a LOTR movie)

Immutability defends against accidental modifications

---

# Livelyness guarantees

```
const oldTodo = store.todos[0]
store.removeTodo(0)

console.log(oldTodo.title)
```

.appear[
```
Error: [mobx-state-tree] This object has died and is no longer part
of a state tree. It cannot be used anymore. Blah blah.
```
]

???

"Death is gift to men" (Elrond in a LOTR movie)

---

# First class support for references

```javascript
const BookEntry = types.model({
    amount: types.number,
    book: types.reference(Book)
})

bookEntry.book = bookStore.books[0]
console.log(bookEntry.book.title) // OK
console.dir(getSnapshot(bookEntry))
```

.appear[
```javascript
{
    amount: 3,
    book: "24"
}
```
]

---

class: ticks3

.tick1.faded[cheap snapshots<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[fine grained updates<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1.faded[tree structure<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[denormalized data<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1.faded[easy hydration<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[co-location of actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1.faded[protection of data<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1.faded[replayable actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2.faded[straight forward actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1.faded[devtool support<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[static analysis / typing<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick1[predictable references<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]
.tick2[predictable references<i class="em em-white_check_mark"></i>.appear[<i class="em em-palm_tree"></i>]]

---

class: ticks3

.tick1[cheap snapshots<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[fine grained updates<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[tree structure<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[denormalized data<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[easy hydration<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[co-location of actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[protection of data<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[replayable actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[straight forward actions<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[devtool support<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[static analysis / typing<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick1[predictable references<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]
.tick2[predictable references<i class="em em-white_check_mark"></i><i class="em em-palm_tree"></i>]

---

# ...and more...

* Composable; every node is a tree in itself
* JSON Patch support
* Middleware
* Full MobX compatibility
* Full fledged type system: models, lists, maps, union, maybe, defaults, refinements...
* Thanks! [Mattia Manzati](https://twitter.com/@mattiamanzati) and [Giulio Canti](https://twitter.com/GiulioCanti)

---

Can we combine the best features of immutable and mutable state trees and create the next generation of state management?

Try to go home early tweet

https://twitter.com/codylindley/status/862679380228685826

???

True for MobX, mobx-state-tree tries to take that idea to the next level

---

Release

