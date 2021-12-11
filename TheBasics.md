# [The Basics](https://www.typescriptlang.org/docs/handbook/intro.html)

#### ECMAScript (ES)
* Standard for scripting languages to ensure interoperability of web pages across different web browsers
* ECMAScript is the language, whereas JavaScript is the dialect (most popular implementation)
* JS is an implementation of ES and has core features but also additional features
* Every browser has a JS interpreter


#### Non-exception failures
* ES has instructions how to behave when a Runtime error occurs
* Example in TS vs JS:
```
const user = {
  name: "Daniel",
  age: 26,
};
user.location; // returns undefined
```

```
const user = {
  name: "Daniel",
  age: 26,
};
 
user.location;
Property 'location' does not exist on type '{ name: string; age: number; }'.
```
* So TS catches things like: typos, uncalled functions, basic logic errors, and type checking

## `tsc`, the TypeScript compiler
* `npm install -g typescript`
	* installs `tsc` locally
* `tsc hello.ts` complies/transforms our file into a plain JavaScript file
* `tsc` will still convert code to .js even if it provides an error
	* can use [noEmitOnError](https://www.typescriptlang.org/tsconfig#noEmitOnError) to prevent that

#### Explicit Types
```
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}

greet("Maddison", new Date());
```
* `Date` and `string` are type annotations
* We don't always have to write explicit type annotations

#### Erased Types
* Type annotations never change the runtime behavior of your program

#### Downleveling
* Moving from a higher version of ECMAScript down to a lower one
* tsc converts the following because it has the ability to rewrite code from newer ES versions to older. Template strings didn't come out until ES6
```
`Hello ${person}, today is ${date.toDateString()}!`; -> "Hello " + person + ", today is " + date.toDateString() + "!";
```
* By default, TypeScript targets ES3 but most developers can safely speciy ES2015

#### Strictness
* TypeScript has several type-checking strictness flags in `tsconfig.json`
* The strict flag or `"strict": true` turns them all on by default
* The two biggest ones are: noImplicitAny and strictNullChecks

## [noImplicitAny](https://www.typescriptlang.org/tsconfig#noImplicitAny)
* By default, TypeScript doesn't try to infer types and instead falls back to `any`

## [strictNullChecks](https://www.typescriptlang.org/tsconfig#strictNullChecks)
* By default, `null` and `undefined` are assignable to any other type