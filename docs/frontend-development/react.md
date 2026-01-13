# React

React is a JavaScript library for building user interfaces, particularly single-page applications. It allows developers to create reusable UI components.

## Getting Started

### Installation

```bash
# Create a new React app
npx create-react-app my-app
cd my-app
npm start
```

### Using Vite (Faster Alternative)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

## Core Concepts

### Components

Components are the building blocks of React applications.

#### Function Components

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

#### Arrow Function Components

```jsx
const Welcome = ({ name }) => {
  return <h1>Hello, {name}</h1>;
};
```

### JSX

JSX is a syntax extension for JavaScript that looks similar to HTML.

```jsx
const element = <h1>Hello, world!</h1>;
const user = { firstName: 'John', lastName: 'Doe' };
const greeting = <h1>Hello, {user.firstName}!</h1>;
```

### Props

Props (properties) are used to pass data from parent to child components.

```jsx
function ParentComponent() {
  return <ChildComponent name="John" age={25} />;
}

function ChildComponent({ name, age }) {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
}
```

### State

State is used to manage data that changes over time within a component.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

## Hooks

### useState

Manages component state.

```jsx
const [state, setState] = useState(initialValue);
```

### useEffect

Performs side effects in function components.

```jsx
import { useEffect } from 'react';

function Example() {
  useEffect(() => {
    // Side effect code here
    document.title = 'Component Mounted';

    // Cleanup function (optional)
    return () => {
      console.log('Component will unmount');
    };
  }, []); // Dependency array
}
```

### useContext

Access context values.

```jsx
import { useContext } from 'react';

const value = useContext(MyContext);
```

### useRef

Create a mutable reference that persists across renders.

```jsx
import { useRef } from 'react';

function TextInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}
```

### useMemo

Memoize expensive calculations.

```jsx
import { useMemo } from 'react';

const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### useCallback

Memoize callback functions.

```jsx
import { useCallback } from 'react';

const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

## Event Handling

```jsx
function Button() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log('Button clicked');
  };

  return <button onClick={handleClick}>Click Me</button>;
}
```

## Conditional Rendering

```jsx
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? <h1>Welcome back!</h1> : <h1>Please sign in.</h1>}
    </div>
  );
}
```

## Lists and Keys

```jsx
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

## Forms

```jsx
function Form() {
  const [value, setValue] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Submitted:', value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Useful Libraries

- **React Router**: Navigation and routing
- **Redux / Zustand**: State management
- **React Query**: Data fetching and caching
- **Axios**: HTTP client
- **Formik / React Hook Form**: Form handling
- **Material-UI / Chakra UI**: Component libraries

