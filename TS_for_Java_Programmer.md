## Typescript in 5 minutes 

### Erased Structural Types
* Typescript type system is `structural`, not nominal
   * The relationships between types is determined by the properties they contain, not whether they were declared with some particular relationship

### Consequences of Structural Typing
1. Empty Types
	```
		class Empty {}
		 
		function fn(arg: Empty) {
		  // do something?
		}
		 
		// No error, but this isn't an 'Empty' ?
		fn({ k: 10 });
	```
	* this is a valid call because `{ k: 10 }` has all of the properties that `Empty` does (because `Empty` has no properties)

2. Identical Types
	```
		class Car {
		  drive() {
		    // hit the gas
		  }
		}
		class Golfer {
		  drive() {
		    // hit the ball far
		  }
		}
		// No error?
		let w: Car = new Golfer();
	```
	* not an error because the _structures_ of these classes are the same

3. Reflection
	* TypeScripts type system is fully erased, so it's not avaliable at runtime