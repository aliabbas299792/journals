# Arrays

Making arrays:

```javascript
let x = new Array(); //new array
let y = new Array(10); //new array with an initial size of 10
let z = new Array(1,2,3,4,5); //new array initialised as [1,2,3,4,5]
let w = new Array("Pineapple"); //new array initialised with one element, ["Pineapple"]

let a = ['potato', 'leaf', 'green']; //new array initialised with these elements
let b = []; //empty new array
let undefinedArray = [,,] //non empty array, [undefined, undefined]

```

Accessing arrays and modifying:

```javascript
let z = new Array(1,2,3,4,5);
console.log(z[0], z[1], z[4]); //1 2 5

z[2] = "hello";
console.log(z); //[1, 2, "hello", 4, 5]

```

Modifying the size of an array:

```javascript
let z = new Array(1,2,3,4,5);
console.log(z.length); //5

z.length = 4; //one element is deleted (5)
console.log(z); //[1,2,3,4]

z.length = 6; //2 elements are appended
console.log(z); //[1,2,3,4,undefined,undefined]

console.log(z[15]); //undefined (as it exceeds the size of the array)

```

The maximum number of a JS array is 2^(32)-1 (4294967295).

Other basic operations:

```javascript
let x = ['potato', 'leaf', 'green'];

console.log(typeof x); //object
console.log(Array.isArray(x)); //true

console.log(x.toString()); //"potato,leaf,green"
console.log(x.valueOf()); //returns the array ['potato', 'leaf', 'green']

console.log(x.join("@")); //potato@leaf@green

let y = [ 1, 2, 'potato', 3, 'leaf', null, 'green', undefined]; //mixed array
console.log(y.toString()); //1,2,potato,3,leaf,,green,

```

Concatenating arrays:

```javascript
const array1 = ['a', 'b', 'c'];
const array2 = ['d', 'e', 'f'];
const array3 = array1.concat(array2);

console.log(array3); //["a", "b", "c", "d", "e", "f"]

```



### Data Structures

Arrays are really versatile so you can implement data structures with them with ease. For example, a stack, by using `Array.push(element)`, and `Array.pop()` (the latter returns what it removed).

You can also implement custom hash tables, queues, trees and so forth using arrays, but I'll get to those when I talk about `prototype`s in JS.

### splice(...)

You can **delete elements** from an array by doing something like `Array.splice(position,num)`, `position` argument specifies the position of the first item to delete and the `num` argument determines the number of element to delete.

```javascript
let scores = [1, 2, 3, 4, 5];
let removedScores = scores.splice(0, 3); //removes elements are returned
console.log(scores); //[4, 5]
console.log(removedScores); //[1, 2, 3]

```

You can also **insert elements** using it, `Array.splice(position,0,new_element_1,new_element_2,...)`, the first parameter is the index, the second is `0` to indicate that no elements are to be deleted, and all following elements are those you wish to insert at the index `position`. 

```javascript
let scores = [6, 7, 10];
scores.splice(2, 0, 8, 9); 
//count the gap between the beginning and the first element the 0th position, and so on so the gap between 7 and 10 was 2nd
console.log(scores); //[6, 7, 8, 9, 10]

```

And then you can combine **insertion and deletion**, `Array.splice(position,num,new_element_1,new_element_2...)`, so you can replace `num` number of elements with however many elements you specify after it:

```javascript
let scores = [11, 12, 14, "pear", "apple", 20];
let removed = scores.splice(2, 3, "replace", "with", "four", "elements"); //[14, "pear", "apple"]
console.log(scores); //[11, 12, "replace", "with", "four", "elements", 20]

```

### slice(...)

You can use it to clone an array:

```javascript
let numbers = [1,2,3,4,5];
let newNumbers = numbers.slice(); //[1, 2, 3, 4, 5]
//as otherwise, newNumbers would simply be a reference to numbers

```

You can use it to copy part of an array:

```javascript
let colors = ['red','green','blue','yellow','purple'];
let rgb = colors.slice(0,3);
console.log(rgb); // ["red", "green", "blue"]

```

And finally you can use it to convert array-like objects into arrays:

```javascript
var list = [].slice.call(document.querySelectorAll('div')); //so this will convert a NodeList of divs into an array of divs

```

### indexOf(...)

This and the functions `find(...)` and `lastIndexOf(...)`.

```javascript
var scores = [10, 20, 30, 10, 40, 20];

console.log(scores.indexOf(30)); // 2
console.log(scores.indexOf(50)); // -1, not found, so returns -1

console.log(find(10,scores)); // [0, 3], returns an array of positions where that element was found

console.log(scores.lastIndexOf(10));// 3, lastIndexOf(...) starts fro
console.log(scores.lastIndexOf(20));// 5

```

`Array.lastIndexOf(searchValue[, fromIndex])`, `fromIndex` is optional, but basically this is the exact same as `indexOf(...)`, but starts from the index `fromIndex` and works backwards. When `fromIndex` isn't specified, it's assumed `fromIndex = Array.length - 1`.

### some(...)

Used to test if every element in an array passes some test:

```javascript
function isNegative(x) {
  return x < 0;
}

console.log([-10, 0, 10].some(isNegative)); //true, at least one negative
console.log([1, 2, 3, 4].some(isNegative)); //false, no negatives

```

It's of the form `arr.some(callback(element[, index[, array]])[, thisArg])`, `element` is the current element being processed for the callback, `index` is the index of the currently being processed element, `array` is the array `some(...)` was called upon.

`thisArg` can be supplied to use as a value of this:

```javascript
let numbers = [-1, 3, 5];

let range = {
    min: 0,
    max: 10
};

let isInRange = numbers.some(function (e) {
    return e >= this.min && e <= this.max;
}, range);

console.log(isInRange); //true, at least one element is in the range

```

### every(...)

`every(...)` is basically the same as some, it's just that it returns `true` only when every single element meets the test condition of the callback function:

```javascript
let numbers = [2, 4, 6];
let isEven = numbers.every(function (e) {
    return e % 2 == 0;
});

console.log(isEven); //true


```

For both `every(...)` and `some(...)`, they both return `true` *if the array is empty*.

### sort(...)

`array.sort(comparefunction)` is used, where `compareFunction` is some function which compares 2 elements of the array and outputs `-1`, `0` or `1`.

For a `compareFunction(a, b)`, if a negative value is returned, `a` is put before `b`, if a positive is returned `b` is sorted to before `a`, otherwise nothing happens.

```javascript
let arrayX = ["c", "b", "d", "b", "a"]; //letters
let arrayY = [2,5,2,8,5,0,7,3,1,6]; //numbers
let arrayZ = ["è", "a", "é", "z"]; //non ascii characters

function compareFunction(a, b){
   return a - b;
    //if b > a, a value less than 0 is returned,
    //if a > b, a value greater than 0 is returned,
    //if a == b, 0 is returned
    //therefore this sorts numbers in ascending order (low to high)
}

//or you can do:
arrayX.sort((a, b) => a-b);

function compareFunction(a, b){
    return b - a; //so this sorts in descending order (high to low)
}

//or you can do:
arrayX.sort((a, b) => b-a);

function compareFunction(a, b){
    //compare function for strings, JS compares their "UTF-16 code units values", so compares their UTF-16 values
    if (a > b) return 1;
    if (a < b) return -1;
    return 0;
}

function compareFunction(a, b){
    //compare function for non ascii strings
    return a.localeCompare(b);
}

//or you can do:
arrayX.sort((a, b) => a.localeCompare(b));

```

### filter(...)

Basically retrieves a subset of the original array, while allowing you to set the conditions for it. It can be done using a loop, but this is easy shorthand.

```javascript
//example data set
let cities = [
    {name: 'Los Angeles', population: 3792621},
    {name: 'New York', population: 8175133},
    {name: 'Chicago', population: 2695598},
    {name: 'Houston', population: 2099451},
    {name: 'Philadelphia', population: 1526006}
];

let bigCities = cities.filter((element) => element.population > 3000000); 
//will only returns cities with populations bigger than 3 million

console.log(JSON.stringify(bigCities)); //prints a JSON string of the data returned
//output: [{"name":"Los Angeles","population":3792621},{"name":"New York","population":8175133}]

```

`arrayObject.filter(callback[,contextObject])` where `callback` is `callback(currentElement, index, array)`.

```javascript
let data = [10, 20, "30", 1, 5, 'JavaScript filter', undefined, 'example'];

let range = { //object with the range we will filter using defined in
    lower: 1,
    upper: 10
};

function isInRange(value) {
    if (isNaN(value)) return false; //if it's not a number, return false
    return value >= this.lower && value <= this.upper; //will use a range, lower to upper
}

let numberInRange = data.filter(isInRange, range); 
//passes the function which uses the range, along with the range (which acts through the `this` keyword - contextObject)

console.log(numberInRange); // [10, 1, 5]

```

### map(...)

`arrayObject.map(callback[,contextObject])`, calls the `callback` function on every element of the array, and returns a new array which contains the result.

```javascript
let circleRadii = [1, 3, 2, 5, 6.6, 12.4, 0.2];

let circleAreas = circleRadii.map((element) => Math.round(100*Math.PI*Math.pow(element,2))/100); //calculates area, then rounds to 2dp
console.log(circleAreas); //[3.14, 28.27, 12.57, 78.54, 136.85, 483.05, 0.13]

```

### forEach(...)

`arrayObject.forEach(callback[,contextObject])`, calls the `callback` function on each of the arrays variables. `map` takes the return values of the callback and makes a new array with it, whereas `foreach` discards any return value and always returns undefined.

```javascript
function randomNumArray(length){ //function to generate an array of random numbers of length 'length'
    let numbers = [];
    for(let i = 0; i < length; i++){ //will put 'length' amount of random numbers into the array
        numbers[i] = Math.round(Math.random()*100);
    }
    return numbers;
}

function sigma(sequence){ //function to act as summation of a sequence of numbers
    let sum = 0;
    numbers.forEach((element) => sum += element); //uses forEach to do the summation part
    return sum;
}

let numbers = randomNumArray(length);
console.log(sigma(numbers)); //4997 (obviously will be different each time)

```

### reduce(...)

`arayObject.reduce(reducer [, initialValue])`, it reduces an array into a single value. The `reducer` is a callback function, which looks like this: `reducer(accumulator, currentValue, currentIndex, array)`.

If no `initialValue` argument is supplied, then initially `accumulator` is the first value in the array and `currentValue` is the second value in the array.

If an `initialValue` argument is provided, then initially `accumulator`, is the `initialValue` and the `currentValue` is the first argument in the array.

`accumulator` is basically the output of the last iteration of the internal loop in reduce, so you can do summing quite easily:

```javascript
let numbers = [1, 2, 3];

let sum = numbers.reduce((accumulator, currentValue) => accumulator + currentValue); //sums the array
console.log(sum); //6

```

A more useful use case is flattening a multidimensional array:

```javascript
let multidimensional_array = [[0,1], [2,3], [4,5]];
let flattenedArray = multidimensional_array.reduce((accumulator, currentValue) => accumulator.concat(currentValue));
console.log(flattenedArray); //[0, 1, 2, 3, 4, 5]

```

And then *there's a similar method, `reduceRight`, which is exactly the same, but goes from right to left along the array*, so with the above multidimensional array flattening example, you'd have this:

```javascript
let multidimensional_array = [[0,1], [2,3], [4,5]];
let flattenedArray = multidimensional_array.reduceRight((accumulator, currentValue) => accumulator.concat(currentValue));
console.log(flattenedArray); //[4, 5, 2, 3, 0, 1]

```

### Multidimensional Arrays

You need to initialise the `ith` row as an array or you'll get a `TypeError`. i.e:

```javascript
let someArray = [];
someArray[0][0] = "something"; //TypeError

let someOtherArray = new Array(10); //create 10 rowed array

someOtherArray[9] = new Array (10); //create 10 columns for the 10th row of the array (index starts from 0, so 9 is 10th row remember)
someOtherArray[9][9] = "something";

console.log(someOtherArray[9][9]); //something

```