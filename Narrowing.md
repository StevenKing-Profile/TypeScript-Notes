# [Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)

* Because of unions, we can have different parameter types. Need to decide what type sometimes
```
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```
* `typeof padding === "number"` is a _type guard_, it allows you to anrroe down the type of a variable within a conditional block
* the process of refining types to more specific types than declared is called _narrowing_

### `typeof` type guards
Types:
* "string"
* "number"
* "bigint"
* "boolean"
* "symbol"
* "undefined"
* "object"
* "function"

* Typescript encodes how `typeof` operates on different values, it won't return string `null`
	* in javascript, `null` is actually of type `object`

### Truthiness narrowing
* be careful about wrapping entire functions in a truthiness check
```
function printAll(strs: string | string[] | null) {
  // !!!!!!!!!!!!!!!!
  //  DON'T DO THIS! - null is not iterable
  //   KEEP READING 
  // !!!!!!!!!!!!!!!!
  if (strs) {
    if (typeof strs === "object") {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === "string") {
      console.log(strs);
    }
  }
}
```

### Equality narrowing
```
function example(x: string | number, y: string | boolean) {
  if (x === y) {
	...        
  } else {
  	...
  }
 }
 ```
 * typescript is smart enough to know that the first block only applies if `x` and `y` are both strings

### The `in` operator narrowing
`in` - an operator for determining if an object has a property with a name

example: 
`"value" in x` where `"value"` is a string literaly and `x` is a union type
```
type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }
 
  return animal.fly();
}
```
 
### `instanceof` narrowing
* an operator for checking whether or not a value is an "instance" of another value
	* In javascript, `x instanceof Foo` checks whether the prototype chain of `x` contains `Foo.prototype`

### Assignments
* TS looks at the right side and narrows the left side accordingly
* `let x = Math.random() < 0.5 ? 10 : "hello world!";`
	* Wherever else you use `x`, the assignability is always checked against the declared type

### Control flow analysis
* analysis of code based on reachability
	* when a variable is analyzed, control flow splits off and looks for where code is unreachable. If it is unreachable, TS will remove the type from the variable for the rest of the function

### Using type predicates
* To define a user-defined type guard, we need to define a function whose return type is a _type predicate_
```
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```
	* `pet is Fish` is our type predicate
	* Any time `isFish` is called with some var, TS will _narrow_ that variable to that specific type if the original type is compatible
* Predicate takes the from of `parameterName is Type` where `parameterName` must be the name of a parameter from the current function signature

### Discriminated unions
```
incorrect:

interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}
```
* TS compiler gets angry if we were to write a `getArea` function because it isn't sure if fields are undefined/null. Need to explicity tell TS that `Circle` and `Square` were two seperate types 
```
correct:

interface Circle {
  kind: "circle";
  radius: number;
}
 
interface Square {
  kind: "square";
  sideLength: number;
}
 
type Shape = Circle | Square;
```
* To writer a `getArea` function, we would use
```
function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;
                      
(parameter) shape: Circle
  }
}
```

### The `never` type
* Usually used tp represent a state which shouldn't exist

