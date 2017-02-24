# [hyperapp](https://hyperapp.gomix.me)
[![travis](https://img.shields.io/travis/hyperapp/hyperapp/master.svg)](https://travis-ci.org/hyperapp/hyperapp)
[![codecov](https://img.shields.io/codecov/c/github/hyperapp/hyperapp/master.svg)](https://codecov.io/gh/hyperapp/hyperapp)
[![CDNJS version](https://img.shields.io/cdnjs/v/hyperapp.svg)](https://cdnjs.com/libraries/hyperapp)
[![version](https://img.shields.io/npm/v/hyperapp.svg)](https://www.npmjs.org/package/hyperapp)
[![slack](https://hyperappjs.herokuapp.com/badge.svg)](https://hyperappjs.herokuapp.com)

HyperApp is a JavaScript library for building frontend applications.

[Elm Architecture]: https://guide.elm-lang.org/architecture/
[Hyperx]: https://github.com/substack/hyperx
[JSX]: https://facebook.github.io/react/docs/introducing-jsx.html
[CDN]: https://unpkg.com/hyperapp

* **Declarative**: HyperApp's design is based on the [Elm Architecture]. Create scalable browser-based applications using a functional paradigm. The twist is you don't have to learn a new language.
* **Stateless components**: Build complex user interfaces from micro-components. Stateless components are framework agnostic, reusabl and easier to debug.
* **Batteries-included**: Out of the box, HyperApp has Elm-like state management, a virtual DOM engine and a router; it still weighs `1kb` and has no dependencies.

HyperApp is not opinionated about your stack either; we're compatible with all bundlers and work well with [Hyperx] or [JSX] -- it's your choice.


[Get started with HyperApp](https://github.com/hyperapp/hyperapp/wiki).

## Installation
<pre>
npm i -S <a href=https://npmjs.com/package/hyperapp>hyperapp</a>
</pre>

## Usage
In Node.js.
```jsx
import { h, app } from "hyperapp"
```

In the browser via the [CDN].
```jsx
const { h, app } = hyperapp
```

## [Examples](https://hyperapp.gomix.me)
<details><summary>Hello World</summary>

```jsx
app({
    model: "Hi.",
    view: model => <h1>{model}</h1>
})
```

[View online](http://codepen.io/jbucaran/pen/Qdwpxy?editors=0010)
</details>

<details><summary>Counter</summary>

```jsx
app({
    model: 0,
    reducers: {
        add: model => model + 1,
        sub: model => model - 1
    },
    view: (model, actions) =>
        <div>
            <button onClick={actions.add}>+</button>
            <h1>{model}</h1>
            <button onClick={actions.sub} disabled={model <= 0}>-</button>
        </div>
})
```

[View online](http://codepen.io/jbucaran/pen/zNxZLP?editors=0010)
</details>

<details><summary>Input</summary>

```jsx
app({
    model: "",
    reducers: {
        text: (_, value) => value
    },
    view: (model, actions) =>
        <div>
            <h1>Hi{model ? " " + model : ""}.</h1>
            <input onInput={e => actions.text(e.target.value)} />
        </div>
})
```

[View online](http://codepen.io/jbucaran/pen/qRMEGX?editors=0010)
</details>

<details><summary>Drag & Drop</summary>

```jsx
const model = {
    dragging: false,
    position: {
        x: 0, y: 0, offsetX: 0, offsetY: 0
    }
}

const view = (model, actions) =>
    <div
        onMouseDown={e => actions.drag({
            position: {
                x: e.pageX, y: e.pageY, offsetX: e.offsetX, offsetY: e.offsetY
            }
        })}
        style={{
            position: "absolute",
            left: model.position.x - model.position.offsetX + "px",
            top: model.position.y - model.position.offsetY + "px",
            backgroundColor: model.dragging ? "gold" : "deepskyblue"
        }}
    >DRAG ME
    </div>

const reducers = {
    drop: model => ({ dragging: false }),
    drag: (model, { position }) => ({ dragging: true, position }),
    move: (model, { x, y }) => model.dragging
        ? ({ position: { ...model.position, x, y } })
        : model
}

const subscriptions = [
    (_, actions) => addEventListener("mouseup", actions.drop),
    (_, actions) => addEventListener("mousemove", e =>
        actions.move({ x: e.pageX, y: e.pageY }))
]

app({ model, view, reducers, subscriptions })
```

[View online](http://codepen.io/jbucaran/pen/apzYvo?editors=0010)
</details>

<details><summary>Todo</summary>

```jsx
const FilterInfo = { All: 0, Todo: 1, Done: 2 }

app({
    model: {
        todos: [],
        filter: FilterInfo.All,
        input: "",
        placeholder: "Add new todo!"
    },
    view: (model, actions) =>
        <div>
            <h1>Todo</h1>
            <p>
                Show: {Object.keys(FilterInfo)
                    .filter(key => FilterInfo[key] !== model.filter)
                    .map(key =>
                        <span><a data-no-routing href="#" onClick={_ => actions.filter({
                            value: FilterInfo[key]
                        })}>{key}</a> </span>
                    )}
            </p>

            <p><ul>
                {model.todos
                    .filter(t =>
                        model.filter === FilterInfo.Done
                            ? t.done :
                            model.filter === FilterInfo.Todo
                                ? !t.done :
                                model.filter === FilterInfo.All)
                    .map(t =>
                        <li style={{
                            color: t.done ? "gray" : "black",
                            textDecoration: t.done ? "line-through" : "none"
                        }}
                            onClick={e => actions.toggle({
                                value: t.done,
                                id: t.id
                            })}>{t.value}
                        </li>)}
            </ul></p>

            <p>
                <input
                    type="text"
                    onKeyUp={e => e.keyCode === 13 ? actions.add() : ""}
                    onInput={e => actions.input({ value: e.target.value })}
                    value={model.input}
                    placeholder={model.placeholder}
                />{" "}
                <button onClick={actions.add}>add</button>
            </p>
        </div>,
    reducers: {
        add: model => ({
            input: "",
            todos: model.todos.concat({
                done: false,
                value: model.input,
                id: model.todos.length + 1
            })
        }),
        toggle: (model, { id, value }) => ({
            todos: model.todos.map(t =>
                id === t.id
                    ? Object.assign({}, t, { done: !value })
                    : t)
        }),
        input: (model, { value }) => ({ input: value }),
        filter: (model, { value }) => ({ filter: value })
    }
})
```

[View online](http://codepen.io/jbucaran/pen/zNxRLy?editors=0010)
</details>

## Issues

Please report liberally. If you're not sure if something is a bug or not, feel free to file a bug anyway. If you have a feature request, create an issue for that as well.

## Documentation

HyperApp's getting started guide and documentation is located in the [wiki](https://github.com/hyperapp/hyperapp/wiki). Anyone can create and edit pages and contributions are very welcome.

## Other

If you need help with your HyperApp project, [join the Slack](https://hyperappjs.herokuapp.com/) community and don't be afraid to ask any questions.

To learn about updates and new releases, [follow us](https://twitter.com/hyperappjs) on Twitter.


## License

HyperApp is distributed under the terms of the MIT license. See [LICENSE](LICENSE).
