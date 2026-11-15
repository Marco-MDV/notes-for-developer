# React

React is a JavaScript library for building user interfaces from reusable components. A component is typically a function that returns JSX. When state changes, React renders the affected components and updates the DOM as needed.

## Start with Vite

Install a supported Node.js LTS release and check the current [Vite requirements](https://vite.dev/guide/). Create React App has been [deprecated](https://react.dev/blog/2025/02/14/sunsetting-create-react-app).

```sh
npm create vite@latest react-notes -- --template react
cd react-notes
npm install
npm run dev
```

Use `npm run build` to create a production build. The Vite template mounts the application in `src/main.jsx` using `createRoot` from `react-dom/client`.

## Components, props, and JSX

Props are values passed to a component. You can pass strings, numbers, arrays, objects, and callback functions. Use `children` to render content placed between the opening and closing tags of a component.

```jsx
function Panel({ title, children }) {
    return (
        <section>
            <h2>{title}</h2>
            {children}
        </section>
    );
}

export default function App() {
    return (
        <>
            <h1>React notes</h1>
            <Panel title="Props">
                <p>This paragraph is passed as children.</p>
            </Panel>
        </>
    );
}
```

A fragment, written as `<></>`, groups sibling elements without adding a wrapper to the DOM. Use braces for JavaScript expressions, `className` for CSS classes, and event props such as `onClick` to pass handlers.

See [Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx).

## To-do list

With plain JavaScript, you can build a list by creating a DOM node, attaching handlers, and appending it to the list. In React, store the tasks in state and describe the list in JSX.

The example below can replace `src/App.jsx` in the Vite project.

```jsx
import { useState } from 'react';

let nextTaskId = 1;

export default function App() {
    const [tasks, setTasks] = useState([]);
    const [draft, setDraft] = useState('');

    function addTask(event) {
        event.preventDefault();
        const text = draft.trim();
        if (!text) return;

        const task = { id: nextTaskId++, text, completed: false };
        setTasks(current => [...current, task]);
        setDraft('');
    }

    function toggleTask(id) {
        setTasks(current => current.map(task =>
            task.id === id ? { ...task, completed: !task.completed } : task
        ));
    }

    function removeTask(id) {
        setTasks(current => current.filter(task => task.id !== id));
    }

    return (
        <main>
            <h1>To-do list</h1>
            <form onSubmit={addTask}>
                <label htmlFor="task">New task</label>
                <input id="task" value={draft} onChange={event => setDraft(event.target.value)} />
                <button type="submit">Add task</button>
            </form>
            <ul>
                {tasks.map(task => (
                    <li key={task.id}>
                        <label>
                            <input
                                type="checkbox"
                                checked={task.completed}
                                onChange={() => toggleTask(task.id)}
                            />
                            {task.completed ? <s>{task.text}</s> : task.text}
                        </label>
                        <button type="button" onClick={() => removeTask(task.id)}>Remove</button>
                    </li>
                ))}
            </ul>
        </main>
    );
}
```

Each task keeps the same key when other tasks are removed. The functional form of `setTasks` computes each update from the previous state.

## Hooks

| Hook | Use |
| --- | --- |
| `useState` | Store a value and update it to trigger a render. |
| `useEffect` | Synchronize with an external system, such as a subscription or timer. Return a cleanup function when needed. |
| `useContext` | Read a value from the nearest matching context provider. |
| `useReducer` | Manage state transitions with a reducer and dispatched actions. |
| `useMemo` | Cache the result of a calculation between renders. |
| `useCallback` | Cache a function reference between renders. |
| `useRef` | Keep a mutable value without triggering a render, or reference a DOM node. |
| `useLayoutEffect` | Measure or update layout before the browser repaints. |
| `useImperativeHandle` | Customize the handle exposed through a ref. |
| `useDebugValue` | Label a custom hook in React DevTools. |

Custom hooks are functions whose names start with `use`. They let components reuse stateful logic. Call hooks at the top level of a function component or custom hook.

See the [React Hooks reference](https://react.dev/reference/react/hooks).

### Lifting state up

When two components need the same state, move it to their closest common parent. Pass the current value and any update handlers as props.

```jsx
import { useState } from 'react';

function ToggleButton({ visible, onToggle }) {
    return (
        <button type="button" aria-expanded={visible} aria-controls="details" onClick={onToggle}>
            Toggle details
        </button>
    );
}

function Details({ visible }) {
    return <p id="details" hidden={!visible}>Example text</p>;
}

export default function App() {
    const [visible, setVisible] = useState(false);

    return (
        <>
            <ToggleButton visible={visible} onToggle={() => setVisible(current => !current)} />
            <Details visible={visible} />
        </>
    );
}
```

### Sharing state with Context

Use Context to share a value such as a theme among descendants. The provider below scopes the value to the part of the interface it wraps. Redux Toolkit is a separate option for applications that need a store with explicit actions and tooling.

```jsx
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(null);

function ThemeProvider({ children }) {
    const [dark, setDark] = useState(false);
    const toggleTheme = () => setDark(current => !current);

    return (
        <ThemeContext value={{ dark, toggleTheme }}>
            {children}
        </ThemeContext>
    );
}

function ThemeSwitch() {
    const { dark, toggleTheme } = useContext(ThemeContext);

    return (
        <label>
            <input type="checkbox" checked={dark} onChange={toggleTheme} />
            Dark theme
        </label>
    );
}

function Footer() {
    const { dark } = useContext(ThemeContext);
    return <footer>Active theme: {dark ? 'dark' : 'light'}</footer>;
}

export default function App() {
    return (
        <ThemeProvider>
            <ThemeSwitch />
            <Footer />
        </ThemeProvider>
    );
}
```

The provider syntax above works in React 19 and later. See [createContext](https://react.dev/reference/react/createContext) and [Redux Toolkit](https://redux-toolkit.js.org/introduction/getting-started).

## Routing

In declarative React Router, `BrowserRouter` tracks the current location, `Routes` selects the matching `Route`, and `Link` navigates within the application. Use a regular anchor for an external URL.

```sh
npm install react-router
npm run dev
```

```jsx
import { BrowserRouter, Link, Route, Routes } from 'react-router';

export default function App() {
    return (
        <BrowserRouter>
            <header>
                <nav aria-label="Main navigation">
                    <Link to="/">Home</Link>{' '}
                    <Link to="/about">About</Link>
                </nav>
            </header>
            <main>
                <Routes>
                    <Route path="/" element={<h1>Home</h1>} />
                    <Route path="/about" element={<h1>About</h1>} />
                    <Route path="*" element={<h1>Page not found</h1>} />
                </Routes>
            </main>
            <footer>React notes</footer>
        </BrowserRouter>
    );
}
```

When deploying a single-page app with `BrowserRouter`, configure your host to serve `index.html` for application routes so a direct visit to `/about` works. See the [React Router installation guide](https://reactrouter.com/start/declarative/installation).

## React-Bootstrap

React-Bootstrap provides React components for Bootstrap. Install both packages and import Bootstrap's CSS once. React-Bootstrap components manage their own interactions.

```sh
npm install react-bootstrap bootstrap
```

```jsx
import 'bootstrap/dist/css/bootstrap.min.css';
import Col from 'react-bootstrap/Col';
import Container from 'react-bootstrap/Container';
import Row from 'react-bootstrap/Row';

export default function App() {
    return (
        <Container>
            <Row>
                <Col xs={12} md={6}>First column</Col>
                <Col xs={12} md={6}>Second column</Col>
            </Row>
        </Container>
    );
}
```

These columns stack on narrow screens and sit side by side starting at the `md` breakpoint. The equivalent Bootstrap classes are `col-12 col-md-6`. See [React-Bootstrap installation](https://react-bootstrap.github.io/docs/getting-started/introduction/) and the [HTML and CSS guide](HTML%20%26%20CSS.md).
