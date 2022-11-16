
## Basic Types

### *Number:*
```tsx
let decimal: number = 6
let hex: number = 0xf00d
let binary: number = 0b1010
let octal: number =0o744
```

### *Boolean:*
```tsx
let isAlive: boolean = false
```

### *String:*
```tsx
let name: string = "bob"

// Can also use woth templates, which can be multi-line and have built in expressions
let age: number = 37
let sentence: string = `Hello, my name is ${name}, I will be ${ age + 1 } next year `
```

### *Array:*
```tsx
let list: number[] = [1,2,3]

// can also use generalization Array<elemType>
let list: Array<number> = [1,2,3]; </number>
```

### *Tuple:*
```tsx
// Declare a tuple type
let x: [string, number]
// Initialize it
x = ['hello', 10] 

// Tuples can also be destructed
clg('tupleType[0]: ${tupleType[0]}')
clg('tupleType[1]: ${tupleType[1]}')
// Make Element optional with ? symbol
let optionalTuple: [sting, boolean?]
```

### *ENUM:*

`enum` is a special type that provides a convenient way to give clear (human readable) names to a set of numeric values
```tsx
enum Color {Red, Green, Blue}
let c: Color = Color.Green
```

By default `enum` start with 0 but this can be changed by directly specifying a value for the enum member
```tsx
enum Color {Red=1, Green, Blue}
let c: Color = Color.Green

// Note: all values of a enum can be set manually if desired

enum DoorState {
	Open = 3,
	Closed = 7,
	Ajar = 10
}

// enum values can also be replaced with strings
enum DoorStateString {
	Open = "open",
	Closed = "closed",
	Ajar = "ajar"
}
```

Convenient feature of `enum` is you can get the name of an `enum` member by passing its numeric value.
```tsx
enum Color {Red=1, Green, Blue}
let colorName: string = Color[2]

alert(colorName)
```

### *Any:*

`any` type is a useful way to disable type checking in event the desired type is not yet know and/or dynamic. 

```tsx
let looselyTyped: any = 4
// OK, ifItExists might exist at runtime
looselyTyped.ifItExists()
// OK, toFixed exists (but the compiler doesn’t check)
looselyTyped.toFixed()
let strictlyTyped: unknown = 4
strictlyTyped.toFixed()

// also useful if you know part of the variable type but not all
let list: any[] = [1, true, "free"]
list[1] = 100 // [1,100,"free"]
```

### *Void:*

Opposite of Any: the absence of any types. Most often used as the return type of functions that do not return any value. 
```tsx
function warnUser(): void {
	alert("This is my warning message")
}
```

**Note: declaring variables as void type is useless**

### *Null and Undefined:*

The Null and Undefined types correspond to the same types in JS. These types are subtypes for all other types by default.
```tsx
let n: number = null; // Primitive types can be null
let m: number = undefined; // Primitive types can be undefined
let x = null; // same as x: any = null
let y = undefined; / / same as y: any = undefined
let e: Null; / / Error
let r: Undefined; // Error
```

**Note: as with Void, declaring a variable type as null or undefined is pointless because said variable's value could only ever be null or undefined.**

### *Never:* 
```tsx
function error(message: string): never {
throw new Error (message);
}
// The output type of fail() is never
function fail() {
return error ("Something failed");
}
// no exit from this function
function infiniteLoop () function: never {
while (true) {
}
}
```
### *Symbol:*
```tsx
var secretKey = Symbol();
var obj = {};
obj[secretKey] = "secret message"; / / Symbol as
property
obj[Symbol.toStringTag] = "test";
```

### *Type Assertion*
In the event you know more about the variable type than TS, it can be asserted by the developer. *Similar to type casting in other languages*
```tsx
// Method-1: as-syntax
let someValue: any = "this is a string"
let strLength: number = (someValue as string).length

// Method-2: angle brackets
let someValue: any = "this is a string"
let strLength: number = (<string>someValue).length
```

**Note: Type assertion method is user preference unless TS is being used in conjunction with JSX, in which case only typecasting via syntax is allowed**

### *Function Types:*


