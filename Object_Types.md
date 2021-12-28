# Object Types
In JavaScript, the fundamental way that we group and pass around data is through objects. 
In TypeScript, we represent those through object types.
```javascript
// anonymous fnctions
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}

// intefaces
interface Person {
  name: string;
  age: number;
}

// type alias
type Person = {
  name: string;
  age: number;
};
```

## Property Modifiers
Each property in an object type can specify:
1. type
2. whether property is optional
3. whether the property can be written too

#### Optional properties:
```javascript
interface PaintOptions {
  shape: Shape;
  xPos?: number;
  yPos?: number;
}
```
* In Javascript, even if the property has never been set, we can still access it as `undefined`
```javascript
function paintShape(opts: PaintOptions) {
  let xPos = opts.xPos === undefined ? 0 : opts.xPos;
       
let xPos: number
  let yPos = opts.yPos === undefined ? 0 : opts.yPos;
       
let yPos: number
  // ...
}
```
^ the above is so popular, JavaScript has has a syntax to support it
```javascript
function paintShape({ shape, xPos = 0, yPos = 0 }: PaintOptions) {
  console.log("x coordinate at", xPos);
                                  
(parameter) xPos: number
  console.log("y coordinate at", yPos);
                                  
(parameter) yPos: number
  // ...
}
```
^ refered to as a [destructuring pattern](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) for paintShape's parameter 

#### `readonly` Properties
a property marked as `readonly` cannot be written to during type-checking
* note: doesn’t necessarily imply that a value is totally immutable - or in other words, that its internal contents can’t be changed. It just means the property itself can’t be re-written to.

#### Index Signatures
Sometimes you don't know all of a type's properties ahead of time, but you do know the shape of the values. 
Use an _index signature_ to describe the types of possible values

```javascript
interface StringArray {
  [index: number]: string;
}
 
const myArray: StringArray = getStringArray();
const secondItem = myArray[1];
```
^ `StringArray` interface has an index signature, which tells us that when `Stringarray` is indexed with a number, it will return a `string`

* An index signature property type must be either a `string` or a `number`
* Can enforce only one return type of a index signature:
```javascript
interface NumberDictionary {
  [index: string]: number;
 
  length: number; // ok
  name: string; // ERROR: Property 'name' of type 'string' is not assignable to 'string' index type 'number'.
}
```
* Can use unions to allow both
```javascript
interface NumberOrStringDictionary {
  [index: string]: number | string;
  length: number; // ok, length is a number
  name: string; // ok, name is a string
}
```
* can also make index signatures `readonly` to prevent assignment to their indices 

## Extending Types
```javascript
interface BasicAddress {
  name?: string;
  street: string;
  city: string;
  country: string;
  postalCode: string;
}
 
interface AddressWithUnit extends BasicAddress {
  unit: string;
}
```

## Intersection Types
Used to combine existing object types, defined using the `&` operator
```javascript
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
 
type ColorfulCircle = Colorful & Circle;
````
^ has all members of `Colorful` and `Circle`

## Interfaces vs Intersections
The principle difference between the two is how conflicts are handled
	* and that difference is typically one of the main reasons why you’d pick one over the other between an interface and a type alias of an intersection type

## Generic Object Types
```javascript
interface Box {
  contents: any; // a box that can contain any value: strings, numbers, Giraffes, whatever.
}
```
^ `contents` is typed as any, which works, but can lead to accidents down the line
	* we could use `unknown` but that would mean in cases where we know the type, we'd have to use precautionary checks (if type)

Solution to this is to create a _generic_ `Box` type which declares a _type parameter_
```javascript
interface Box<Type> {
  contents: Type;
}
```
* You might read this as “A `Box` of `Type` is something whose contents have type `Type`”. Later on, when we refer to Box, we have to give a type argument in place of Type like `let box: Box<string>;`
	* think of `Box` as a template for a real type
	* When TypeScript sees `Box<string>`, it will replace every instance of `Type` in `Box<Type>` with `string` and end up working with something like `{ contents : string }` 

* Allows use to avoid overloads entirely by using [generic functions](https://www.typescriptlang.org/docs/handbook/2/functions.html#generic-functions)
```
function setContents<Type>(box: Box<Type>, newContents: Type) {
  box.contents = newContents;
}
```

* Since type aliases can describe more than just object types, we can also use them to write generic helper types

#### The `Array` Tyoe
* `numer[]` is just shorthand for `Array<number>`
* `Array` itself is a generic type
```
interface Array<Type> {
  /**
   * Gets or sets the length of the array.
   */
  length: number;
 
  /**
   * Removes the last element from an array and returns it.
   */
  pop(): Type | undefined;
 
  /**
   * Appends new elements to an array, and returns the new length of the array.
   */
  push(...items: Type[]): number;
 
  // ...
}
```

#### The `ReadonlyArray` Type
describes arrays that shouldn't be changed

* When we see a function that returns ReadonlyArrays, it tells us we’re not meant to change the contents at all
* Wen we see a function that consumes ReadonlyArrays, it tells us that we can pass any array into that function without worrying that it will change its contents

* no constructor, we can assign regular `Array`s to `ReadonlyArray`s 
  * `const roArray: ReadonlyArray<string> - ["red", "green", "blue"];`
  * shorthand syntax is `readonly Type[]`

#### Tuple Types
A _tuple type_ is another `Array` type that knows exactly how many elements it contains, and exactly which type it contains at specific positions
`type StringNumberPair = [string, number];`

* has no representation at runetime, but it describes arrays whose `0` index is a `string` and `1` index contains a `number`
* useful in heavily convention-based APIs