# Generics

[TypeScript Generics for People Who Gave Up on Understanding Generics](https://ts.chibicode.com/generics/)

Example:

```ts
function makeState() {
  let state: number;
  function getState() {
    return state;
  }
  function setState(x: number) {
    state = x;
  }
  return { getState, setState };
}
```

## What if we use a string?

```ts
const { getState, setState } = makeState();

setState("foo"); // ERROR !
console.log(getState());
```

## Union Types ?

```ts
function makeState() {
  let state: number | string;
  function getState() {
    return state;
  }
  function setState(x: number | string) {
    state = x;
  }
  return { getState, setState };
}
```

**This does NOT work.** You’ll end up creating a state that allows both numbers and strings, which is not what we want. Instead, we want makeState() to support creating two different states: one that allows only numbers, and the other that allows only strings.

---

## Use generics

```ts
function makeState<S>() {
  let state: S;
  function getState() {
    return state;
  }
  function setState(x: S) {
    state = x;
  }
  return { getState, setState };
}
```

```ts
// Creates a number-only state
const numState = makeState<number>();

numState.setState(1);

console.log(numState.getState());

// numState.setState('foo') will fail!
```

```ts
// Creates a string-only state
const strState = makeState<string>();

strState.setState("foo");

console.log(strState.getState());

// strState.setState(1) will fail!
```

## Do not allow other types ?

How to avoid things like this:

```ts
const boolState = makeState<boolean>();

boolState.setState(true);

console.log(boolState.getState());
```

Do something like this:

```ts
function makeState<S extends number | string>() {
  let state: S;

  function getState() {
    return state;
  }

  function setState(x: S) {
    state = x;
  }

  return { getState, setState };
}

const boolState = makeState<boolean>(); // ERROR
```

## Default type

```ts
// Set the default type of S as number

function makeState<S extends number | string = number>();
```

---

## Generic makePair

```ts
function makePair<F, S>() {
  let pair: { first: F; second: S };

  function getPair() {
    return pair;
  }

  function setPair(x: F, y: S) {
    pair = {
      first: x,
      second: y,
    };
  }

  return { getPair, setPair };
}
```

Here’s an example usage. By calling makePair with <number, string>, it forces first to be number and second to be string.

```ts
// Creates a (number, string) pair
const { getPair, setPair } = makePair<number, string>();

// Must pass (number, string)
setPair(1, "hello");
```

To summarize, you can create a generic function that takes multiple type parameters.

```ts
// makeState() has 1 type parameter
function makeState<S>();

// makePair() has 2 type parameters
function makePair<F, S>();
```

## Default types to makePair

```ts
function makePair<
  F extends number | string = number,
  S extends number | string = string
>();
```

## Make the second type (S) to be related to the first type (F)

```ts
// The second parameter S must be either
// boolean or whatever was specified for F
function makePair<F extends number | string, S extends boolean | F>();

// These will work
makePair<number, boolean>();
makePair<number, number>();
makePair<string, boolean>();
makePair<string, string>();

// This will fail because the second
// parameter must extend boolean | number,
// but instead it’s string
makePair<number, string>();
```

## Generic interfaces

```ts
// Extract into a generic interface
// to make it reusable
interface Pair<A, B> {
  first: A;
  second: B;
}
```

```ts
function makePair<F, S>() {
  // Usage: Pass F for A and S for B
  let pair: Pair<F, S>;
  // ...
}
```

## Generic type aliases

```ts
// Extract into a generic type alias. It’s
// basically identical to using an interface
type Pair<A, B> = {
  first: A;
  second: B;
};
```

```ts
function makePair<F, S>() {
  // Usage: Pass F for A and S for B
  let pair: Pair<F, S>;
  // ...
}
```
