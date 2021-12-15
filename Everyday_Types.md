# [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)

### Primatives:
1. string
2. number
3. boolean
4. bigint
5. symbol

* Uppercase primatives are wrapper classes 

### Arrays:
* ex: `number[]`, `string[]`, `Array<number>`

### `any`:
* isn't type checked

### Type Annotations on Variables:
* Type annotations go _after_ the thing being typed
	* example: `let myName: string = "Alice";`
*  Wherever possible, TypeScript tries to automatically infer the types in your code
	* example: `let myName = "Alice"; // No type annotation needed -- 'myName' inferred as type 'string'`

### Functions
#### Parameter Type Annotations
```
// Parameter type annotation
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}

Even if you don’t have type annotations on your parameters, TypeScript will still check that you passed the right number of arguments.
```

#### Return Type Annotations
```
function getFavoriteNumber(): number {
  return 26;
}

usually don’t need a return type annotation because TypeScript will infer the function’s return type based on its return statements
```

#### Anonymous Functions
* _contextual typing_ - context that the function occurs within informs tsc what type it should have
```
names.forEach((s) => {
  console.log(s.toUpperCase());
});
```

### Object Types
* `?` specify that all or some of an objects properties are optional
* `undefined` if access a property that doesn't exist

### Union Type
* build new types out of existing ones using a large variety of operators
```
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}

printId(101); // OK
printId("202"); // OK
printId({ myID: 22342 }); // Error
```
* TypeScript only allows unions when if its valid for every member of the union (an intersection of properties)
	* For example, if you have the union `string | number`, you can’t use methods that are only available on string
	* Can narrow the function however:
```
	function printId(id: number | string) {
		  if (typeof id === "string") {
		    // In this branch, id is of type 'string'
		    console.log(id.toUpperCase());
		  } else {
		    // Here, id is of type 'number'
		    console.log(id);
		  }
		}
```
* `boolean` itself is just an alias for a union of `true | false`

### Type Aliases
* Only useful as an alias. Can't treat like a class and create different versions
```
type Point = {
  x: number;
  y: number;
};

or

type ID = number | string;
```

### Interfaces
```
interface Point {
  x: number;
  y: number;
}
```

#### interface vs type alias
* the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

### Type Assertions
```
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
	or
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```
* Because type assertions are removed at compile-time, there is no runtime checking associated with a type assertion. 
	* There won’t be an exception or null generated if the type assertion is wrong.
* Only allowed to convert to a _more specific_ or _less specific_ type

### Literal Types
* we can refer to _specific_ strings and numbers in type positions
`let x: "hello" = "hello";` 
* Not particularly useful on its own but combining literals can be useful
```
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left"); // OK
printText("G'day, mate", "centre"); // Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
```
```
interface Options {
  width: number;
}
function configure(x: Options | "auto") {
  // ...
}
configure({ width: 100 });
configure("auto");
configure("automatic"); // Argument of type '"automatic"' is not assignable to parameter of type 'Options | "auto"'.
```

### Literal Inference
* When you intialize a variable with an Object, TypeScript assumes the properties ight change later.
```
const obj = { counter: 0 };
if (someCondition) {
  obj.counter = 1;
}
```
	* If we change from 0 -> 1, it's not an error. 
```
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
```
	* compiler thinks method is a `string` - 2 ways to avoid
		1. Change the inference by adding a type assertion in either location
```
// Change 1:
const req = { url: "https://example.com", method: "GET" as "GET" };
// Change 2
handleRequest(req.url, req.method as "GET");
```
		2. add `as const` to the entire object
			* `const req = { url: "https://example.com", method: "GET" } as const;`
				* ensures assigned the literal type



