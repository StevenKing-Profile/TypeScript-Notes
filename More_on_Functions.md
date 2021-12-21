# [More on Functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)

### Function Type Expressions
* syntatically similiar to arrow functions
```
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);
```
* `(a: string) => void` means "a function with one parameter, named `a`, of type string that doesn't return a value"
Could rewrite the above using a type alias to name a function type:
```
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
	// ...
}
```

### Call Signatures
* In JavaScript, functions can have properties in addition to being callable. However, the function type expression doesn't allow for declaring properties. In order to do that, we write a _call signature_ in an object type:
```
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}
```

### Construct Signatures
* functions that involve the `new` operator are usually a constructor
	* some functions don't require `new` like Javascript's `Date` object
```
type SomeConstructor = {
  new (s: string): SomeObject;
};
function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}
```

### Generic Functions
* _generics_ are used when we want to describe a correspondence between two values. We do this by declaring a _type_ parameter in the function signature
```
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```
^ By adding a type parameter `Type` to this function and using it in two places, we've created a link between the input of the function (the array) and the output (the return function) 

```
const s = firstElement(["a", "b", "c"]); // s is of type 'string'
const n = firstElement([1, 2, 3]); // n is of type 'number'
const u = firstElement([]); // u is of type undefined
```

Inference:
The `Type` is inferred by TypeScript, but we can also use multiple type parameters for different params 


Constraints:
* limits the kind of types that a type parameter can accept
```
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
 
const longerArray = longest([1, 2], [1, 2, 3]); // longerArray is of type 'number[]'
const longerString = longest("alice", "bob"); // longerString is of type 'alice' | 'bob'
const notOK = longest(10, 100); // Error! Numbers don't have a 'length' property
```


Guidelines for Writing Good Generic Functions
* When possible, use the type parameter itself rather than constraining it
* Use as few type parameters as possible
* If a type parameter only appears in one location, strongly reconsider if you actually need it

### Optional Parameters
* `?`

Optional Parameters in Callbacks:
* When writing a function type for a callback, never write an optional parameter unless you intend to call the function without passing that argument

### Function overloads
_overload signatures_:
```
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3); // ERROR
```
^^ can't actually call the above function with just 2 params

Overload Signatures and the Implementation Signature:
* The signature of the implementation is not visible from the outside. When writing an overloaded function, you should always have two or more signatures above the implementation of the function.
* Always prefer parameters with union types instead of overloads when possible

### Declaring `this` in a Function
* TS will infer what `this` should be in a function via code flow analysis

### Rest Parameters and Arguments
* functions that take an unbounded number of arguements using `...` syntax are called _rest parameters_
```
function multiply(n: number, ...m: number[]) {
  return m.map((x) => n * x);
}
const a = multiply(10, 1, 2, 3, 4); // 'a' gets value [10, 20, 30, 40]

```

### Parameter Destructuring
* conveniently unpack objects provided as an argument into one or more locable variables

Examples:
```
function sum({ a, b, c }: { a: number; b: number; c: number }) {
  console.log(a + b + c);
}

```
```
// Same as prior example
type ABC = { a: number; b: number; c: number };
function sum({ a, b, c }: ABC) {
  console.log(a + b + c);
}
```

### Assignability of Functions
* return type `void`
  * can produce some unusual, but expected behavior.
  * a return type of `void` does **not** force functions to **not** return something. 
    * when implmented, a `void` return type can return _any_ other value, but it will be ignored
    * so the following is valid:
```
type voidFunc = () => void;
 
const f1: voidFunc = () => {
  return true;
};
```
