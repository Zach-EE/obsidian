
# JavaScript:

## Copy Pasta (REPL)
```jsx
// Adds clear() to node REPL similar to Chrome console
function clear() {
  process.stdout.write('\u001B[2J\u001B[0;0f');
}

Array.matrix = function(numrows, numcols, initial){
	let arr = []
	for (let i = 0; i < numrows; i++) {
		let columns = []
		for(let j = 0; j < numcols; j++) {
			coulums[j] = initial
		}
		return arr
	}
}	
```

## Arrays:

### **Creating Arrays:**
```jsx
let numbers = [] // best practice

let numbers = new Array()

// elements don't need to be the same type
let object = [1, "Joe", true, null] 

// Creating arrays from string 
let string = "the,string,in,this,is,straight,fire"
let words = string.split(",") //breaks up string using common delimitter

for(let i = 0, i<words.length; i++){
	print(`word ${i}: ${words[i]}`)
}
```

You can verify an object is an array by calling `Array.isArray()`, which will return a boolean.

### **Aggregate Array Operations:**

**Shallow Copy**

Simply assigning one array to another array. However, this only assigns a reference to the assigned array. When a change is made to the original array it will be reflected in the copy as well. Better alternative is a *deep copy*

**Deep Copy**

This method makes it so each of the original array's elements are actually copied to the new array's elements.
```jsx
// Shallow Copy
let nums = []
for (let i=0; i < 10; i++){
nums[i]=i+1
}
let samenums = nums
nums[0] = 400
print(samenums[0]) // displays 400

// Deep Copy
function copy(arr1, arr2){
	for(let i = 0; arr1.length; i++){
		arr2[i] = arr1[i]
	}
}

let newnums = []
copy(nums, newnums)
nums[0] = 0
print(newnums[0])//displays 400
```

### Accessor Functions:

**Searching for Value**
- `indexOf()` - Looks to see if the argument passed to the function is found in the array. *`indexOf()` will only return first occurrence in an array not multiple*
- `lastIndexOf()` - Similar to `indexOf()` but returns last occurrence

**String Representation of Arrays**
- `join()` & `toString()` - both return string containing elements of the array delimited by commas
- `print()` - when the print function is called with an array name it automatically calls the `toString()` function.

```jsx
let names = ["David", "Mike", "Cynthia", "Raymond", "Clayton", "Mike",
"Jennifer"]

let name = "Mike"
let firstPos = names.indexOf(name) // 1
let lastPos = names.lastIndexOf(name) // 5,

let namestr = names.join() // David, Clayton,.....
namestr = names.toString() // David, Clayton,.....
print(namestr) // David, Clayton,.....
```

**Creating New Arrays from Existing Arrays**
- `concat()` - allows you to put together two or more arrays to create a new array
- `splice()` - allows you to create a new array from a subset of an existing array. Can use splice to add elements to middle of array by passing in following arguments:
	- Starting index (where you want to begin adding elements)
	- The number of elements that you want to remove (0 when adding elements)
	- The elements you want to add
```jsx
let arr1 = ["Mike", "Clayton", "Terrill", "Danny", "Jennifer"]
let arr2 = ["Raymond", "Cynthia", "Bryan"]

let arrconcat = arr1.concat(arr2) // Mike,Clayton,Terrill,Danny,Jennifer,Raymond,Cynthia,Bryan

// Adding elements to middle of array via splice()
let nums = [1,2,3,7,8,9]
let newNums = [4,5,6]
nums.splice(3,0,newNums) // 1,2,3,4,5,6,7,8,9
nums.splice(3,0,4,5,6) // 1,2,3,4,5,6,7,8,9

let nums = [1,2,3,100,200,300,400,4,5]
nums.splice(3,4) // 1,2,3,4,5
```

### Mutator Functions:

Mutator functions allow the modification of the contents of an array without referencing the individual elements.

**Adding Elements to an Array**
- `push()` - adds element to end of the array
- `unshift()` - adds element to start of array object

**Removing elements from an Array**
- `shift()` - returns first element from array, and removes element from array.
- `pop()` - Returns last element from array, and removes element from array.

**Putting Array Elements in Order**
- `sort()` - sorts data lexicographically, assuming the data elements are strings. 
	- To get function to work as intended for numbers need a comparison function to compare elements of array and order correctly
- `reverse()` - reverses array element order.
 
```jsx
let nums = [1,2,3,4,5]
let ranNums = [1,2,300,260,250,4,5]
let names = ['Chris', 'Andrew', 'Mike', 'Rick']

nums.push(6) // 1,2,3,4,5,6
nums.unshift(0) // 0,1,2,3,4,5,6 returns 7
nums.shift()// 1,2,3,4,5,6 returns 6
nums.pop() // 1,2,3,4,5 returns 6

names.sort() // Andrew,Chris,Mike,Rick
ranNums.sort() // 1,2,260,250,300,4,5

function compare(num1, num2){
	return num1 - num2
}

ranNums.sort(compare) // 1,2,4,5,250.260,300
ranNums.reverse() // 300,260,250,5,4,2,1

```

### Iterator Functions

These functions apply a function to each element of an array, either returning a value, set of values, or a new array after applying the function to an element of an array.

**Non-Array-Generating Iterator Functions**
- `forEach()` - Takes a function as an argument and applies the called function to each element of an array.
- `every()` - applies a boolean function to an array and returns `true` if the function can return `true` for every element in the array
- `some()` - takes a boolean function and returns `true` if at least one element of the array meets the criterion of the boolean function
- `reduce()` - applies a function to an accumulator and the successive elements of an array until the end of the array is reached
- `map()` - works similar to `forEach()` by applying a function to each element of an array, however `map()` will return a new array with the update values as elements
- `filter()` - function works similarly to `every()` but instead of returning `true` if all elements satisfy boolean, the function returns an array comprised of elements that satisfy the boolean instead. 

```jsx
let nums = [1,2,3,4,5]
let even = [2,4,6,8,10]
let words = ["this", "is", "a", "string"]
let grades = [77,65,81,92,83]

function square(num){
	return num*num
}

function isEven(num){
	return num % 2 == 0
}

function isOdd(num){
	return num % 2 != 0
}

function add(runningTotal, currentValue){
	return runningTotal + currentValue
}

function concat(accumulatedString, item) {
	return accumulatedString + item
}

function curve(grade) {
	return grade += 5
}

function first(word) {
	return word[0]
}

nums.forEach(square) // 1,4,9,16,25

nums.every(isEven) // false

even.every(isEven) // true

nums.some(isOdd) // true

even.some(isOdd) // false

let sum = nums.reduce(add) // 15

let sentence = words.reduce(concat) // this is a string

let revSentence = words.reduceRight(concat) // string a is this

let newGrades = grades.map(curve) // 82, 70, 86, 97, 88

let acronym = words.map(first) // tias

let evens = nums.filter(isEven) // 2,4
```

### Two-Dimensional and Multidimensional Arrays

Arrays are only one-dimensional, but can be made multi dimensional by making arrays of arrays. 

**Make Two Dimensional Arrays:**
```jsx
Array.matrix = function(numrows, numcols, initial){
	let arr = []
	for (let i = 0; i < numrows; i++) {
		let columns = []
		for(let j = 0; j < numcols; j++) {
			coulums[j] = initial
		}
		return arr
	}
}	
// Example
let nums = Array.matrix(5,5,0)
let names = Array.matrix(3,3,"")

names[1][2] = "Joe" 
```