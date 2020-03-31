Resources used:

https://codeburst.io/javascript-demystified-variable-hoisting-c3c4d2e8fd40 (the 5 articles in this series)

https://www.javascripttutorial.net/

https://developer.mozilla.org/

# Installation

I started off by installing NVM (Node Version Manager), straight from its site, not using sudo/not as root, and followed the installation which got me to set the `$NVM_DIR` variable.

After this I installed node using `nvm install node`, and then made a small HTTP server index.js file. I tried using port 80 and running the file using `node index.js`, but got an EACCES error, so I tried `sudo node index.js`, but then got `command not found`, so I edited the secure_path vairable in /etc/sudoers, as it turned out sudo had separate path variables to the user account I was using. 

I'm told this was for security reasons.

I then added on the path of node into the secure path, after which `sudo node index.js` worked properly on port 80.

I then installed nodemon to live reload the server during development, and ran the index.js file using `sudo nodemon index.js`.

# let and var

let is block scoped, so it'd be active between the parentheses it's declared in (so in the parentheses of if else, for, while etc)

Basically let/const are more strict than var, so less bugs related to declaration and assignment would happen. var has hoisting, so if you declare a variable anywhere in some scope, it's equivalent to declaring it at the top, and so an error won't appear if you try to use the variable before it's declared, something that is an error with let/const (which is a good thing as it could be a bug).

You can also end up with a temporal dead zone, which is a result of it not being initialized unlike var, with let:

```javascript
{
    let log = function(){
        console.log(something);
    };
    
    //temporal dead zone, and trying to access 'something', for example by calling log, would cause a ReferenceError
    
    let something = 3; //we declared it here, so the temporal dead zone (TDZ) ends
    log(); //called outside the TDZ so no error
}

```

Note, that if you declare it somewhere and try accessing it before that you get a reference error, however if you use var or if you don't declare it at all the type is just undefined.

```javascript
{ //TDZ starts
    console.log(typeof bar); //undefined, as not c
    console.log(typeof foo); //ReferenceError
    let foo; // TDZ ends
}

```

var is also attached to the global object, so when you do `var x = 5`, `window.x == 5`, but if you do `let y == 15`, then `window.y == undefined`.

# const

It is conventional to put constants as upper case. 

You can put a JS object as a constant, and modify its values, but you can't reassign it, as you'd expect. To make the values *immutable*, you need to make the object, immutable be using the `Object.freeze()` function. It is *shallow*, so you'll have to apply it separately to each subobject.

Same thing for arrays, you can push, and pull, and reassign data in the array, but you cannot reassign the array itself, i.e:

```javascript
const colours = ['red'];
colours.push('green');
console.log(colours); //output: ["red", "green"]

colours.pop();
colours.pop();
console.log(colours); //output: []

colours = []; //TypeError

```

You can also use `const` in a for loop, like the C++ `for(auto...)` loops:

```javascript
let prices = [10,15,20,30];

for(const price of prices){
	//and here you won't be able to modify the 'price' variable, you can use 'let price'
    //instead of 'const price' if you want to modify the variable
}

```

Remember though, using `const` in a normal `for` loop won't work:

```javascript
for(const i = 0; i < 10; i++){ //TyperError, because 'i' is a constant, so you can't increment it
    //something
}

```

# Objects

You make them using the `key:value` syntax, and access using `objectX.propertyName` or `objectX["propertyName"]`, where `propertyName` is the property name, and `objectX` is some object with that property. If the object doesn't have that property, `undefined` will be returned.

You can use the syntax `delete objectX.propertyName` to delete that specific property from an object.

`delete` is also more than 50x slower than using 

You can do `propertyName in objectX` to check if the property `propertyName` is in the object `objectX`, it evaluates to `true` or `false`.

You can iterate over the properties of an object by using a `for...in` loop:

```javascript
for(let propertyName in objectX){
    //...
}

```

You can also set methods for objects, using the usual syntax, i.e setting the property as a function.

```javascript
objectX.someFunction = function(){
    //...
}
//or
objectX['someFunction'] = function(){
    //...
}
//or
let objectX = {
    property1: "value1",
    property2: "value2",
    someFunction: function(){
        //...
    }
}
//or in ES6
let objectX = {
    property1: "value1",
    property2: "value2",
    someFunction(){
        //...
    }
}

```

Abd then you can obviously use `this` to access the key value pairs in the current object:

```javascript
let objectX = {
    property1: "value1",
    property2: "value2",
    someFunction: function(){
        console.log(this.property1 + " -- " + this.property2);
    }
}

```

And in the case of changing context within a function, you can use another variable to preserve the `this` of this particular scope - simply store it in another variable, like `_this`. Do not store it in `self`, as there is now a global `Window.self` variable, which could cause issues.

## Array/Object conversion

You can also *convert between arrays an objects* like this:

```javascript
let someArray = ['a','b','c']; //an array
let someObject = {"0": "apple", "1": "banana", "2": "pear"}; //an array-like object (keys are numerical-ish)

let arrayFromObject = Object.values(someObject); //turns array-like object into an array
console.log(arrayFromObject); //["apple", "banana", "pear"]

let objectFromArray = Object.assign({}, someArray); //turns an array into an array-like object
console.log(objectFromArray); //{0: "a", 1: "b", 2: "c"}

let keyValuePairArrayFromObject = Object.entries(someArray);
console.log(keyValuePairArrayFromObject); //[["0", "a"], ["1", "b"], ["2", "c"]]

```

## Object Property Types

_Named data properties_ have the following:

- `[[Value]]`, holds the property's value (the data itself)
- `[[Writable]]`, holds a boolean indicating whether the value can be changed

The following are specific to _named accessor functions_:

- `[[Get]]`, holds the 'getter', a function that is called when a property is read
- `[[Set]]`, holds the 'setter', a function that is called when a property is set to a value (the function receives the value as a parameter)

And all properties have the following:

- `[[Enumerable]]`, holds a boolean which indicates enumerability (explains why this is important below)

- `[[Configurable]]`, holds a boolean which if `false`, means you cannot delete a property

**If you use `Object.defineProperty(...)` to define properties, defaults are set**:

|    Attribute Key | Default Value |
| ---------------: | :------------ |
|        [[Value]] | undefined     |
|          [[Get]] | undefined     |
|          [[Set]] | undefined     |
|     [[Writable]] | false         |
|   [[Enumerable]] | false         |
| [[Configurable]] | false         |

Which means *if you used `Object.definProperty(...)` and didn't set `[[Writable]]`, you can't modify that value*.

If an object has `[[Enumerable]] = false`, then the *non-enumerable* property *won't appear in `for...in` loops and in `Object.keys(obj)`*.

### Data Properties Examples

```javascript
let someObject = {};

Object.defineProperty(someObject, "foo", { //set the 'foo' property of 'someObject'
    value: 123
});

Object.defineProperty(someObject, "bar", { //set the 'bar' property of 'someObject'
    value: 456,
    configurable: true,
    writable: true,
    enumerable: true
});

//note how I used Object.defineProperty(...) twice rather than Object.defineProperties(...) once, this is just to show it exists

delete someObject.foo; //false - cannot be deleted as [[Configurable]] is false by default

someObject.foo = 789;
console.log(someObject.foo); //123 - can't be modified as [[Writable]] is false by default

Object.keys(someObject); //["bar"] - won't print "foo", as [[Enumerable]] is false by default

someObject.bar = 789;
console.log(someObject.bar); //789 - we set [[Writable]] to true, so we can modify it

for(let key in someObject){
    console.log(key);
}
//bar - won't print "foo" as someObject.foo's [[Enumerable]] is set to false
//but prints "bar" because we set [[Enumerable]] to true for someObject.bar

delete someObject.bar; //true - deletes it as we set [[Configurable]] to true

Object.keys(someObject); // - won't print anything, as we deleted the only enumerable object

```

### Accessor Properties

These are the `[[Get]]` and `[[Set]]` properties.

```javascript
let product = { _name: "smartphone" }; //setting the _name property

Object.defineProperties(product, {
    name: {
        set: function(value) { this.name = value.replace(/ /g, "_").toUpperCase(); }, //setter function, sets _name
		get: function() { return "🌟 "+this.name+" 🌟"; } //getter function, returns _name
    },
    price: {
        value: 750
    },
    vat: {
        value: 0.2
    },
    netPrice: {
        get: function(){
            return this.price * (1 + this.vat);
        }
    }
});

product.name = "Mobile Phone"; //sets the name
console.log(product.name); //🌟 MOBILE_PHONE 🌟

console.log(product.netPrice); //900

```

You can't set the `[[Set]]` and `[[Get]]` properties if you are setting `[[Writeable]]` and/or `[[Value]]`. 

You also can't modify the value of the property who's `[[Set]]` property you are defining - you'll have to use some other property for that, otherwise you'll get a `RangeError`.

This is because, if you do for example:

```javascript
let someObject = Object.defineProperties({}, {
    something: {
        set: function(someValue) { this.something = someValue; },
		get: function() { return this.something; }
    }
}
    
```

Then for the `[[Set]]` property, you're trying to set the value (which is always done using the `[[Set]]` property), inside the definition of the set property, this causes a `Maximum call stack size exceeded` error (`RangeError`), as it is recursively trying to set the value inside the description of how to set the value.

And for the `[[Get]]` property, it's a similar story - you're trying to access it (which is done using the `[[Get]]` property), inside the definition of the `[[Get]]` property.

And as such, you need to: 

- *use a different property than the one you defined `[[Set]]` and `[[Get]]` in for setting and getting some value you want* 
- *you need to define that property I mentioned above, before you try to use it in the `[[Set]]` and `[[Get]]` properties*

So for example, like this:

```javascript
let someObject = { _something: "a bit of text" };

Object.defineProperties({}, {
    something: {
        set: function(someValue) { this._something = someValue; },
		get: function() { return this._something; }
    }
}

```

And now there is no `RangeError`, it would work as expected (a more complex example is the longer example at the top of this section).

## Prototypes

The JS engine provides the `Object()` function and an anonymous object that can be accessed via `Object.prototype`.

The `Object.prototype` object has many built in properties (`toString()`, `valueOf()`, etc), and a `constructor` property, which points back to the `Object()` function.

*Therefore `Object.prototype.constructor == Object`.*

```javascript
function add(x, y){
    console.log(x + y);
}

console.log(add.prototype.constructor == add); //true

```

If you instantiate a function (using the `new` keyword), the objects made are linked to the original function by `object.constructor`.

The `[[Prototype]]` property links the prototype of the original function to the instances of that function.

The link of `personA`/`personB` below to the `personMaker.prototype` object is through the `[[Prototype]]` property. 

> This is called *prototype linkage*.

If the `personMaker.prototype` object doesn't have a requested function, then the `[[Prototype]]` property of `personMaker.prototype` is used to get to the anonymous object, which is the prototype of the `Object()` function, and if the function isn't defined there, then the function doesn't exist.

> This chain of using `[[Prototype]]` to get from one prototype to another is called the *prototype chain*.

The `Object.prototype.constructor == Object`, that is, that the constructor points back to the original function, and the root `__proto__` of all JS objects is `null`, so the JS engine will no where to stop looking up the *prototype chain*.

*`personA.__proto__.__proto__.__proto == null`*

```javascript
function personMaker(name){
    this.name = name;
}
let personA = new personMaker("Bob");
let personB = new personMaker("Wac");

personMaker.prototype.sayHello = function() { //adds a sayHello() function to the prototype
    console.log("Hello! My name is "+this.name+"!"); 
}

//now we can access it through personA and personB:
personA.sayHello(); //Hello! My name is Bob!
personB.sayHello(); //Hello! My name is Wac!

//you can also conceivably add completely new properties using that method too:
personMaker.prototype.howOldAmI = function() { //prompts the user for the age of this person if it's unset
    if(this.age == undefined){
        console.log("I have no idea how old I am! How old am I?");
        this.age = prompt("How old am I?","20");
        console.log("Oh! I'm "+this.age+" years old!");
    }else{
        console.log("I am "+this.age+" years old!");
    }
}

personA.howOldAmI();
//I have no idea how old I am! How old am I?
//*enters age of 25 in prompt box*
//Oh! I'm 25 years old!
personA.howOldAmI(); //I am 25 years old!
//same thing works for personB

//and you can also add a function to the anonymous object which is the prototype of is

```

You can therefore have this: `personA.__proto__.__proto__ === personMaker.prototype.__proto__`, where `__proto__` is *dunder proto*.

And the `Object()` function's constructor obviously must be the JS provided default `Function()` function, so `Object.constructor == Function`.

And so you also have `personA.__proto__.__proto__.constructor.constructor == Function`, as the anonymous object accessed by the 2nd `__proto__` is `Object.prototype`, and the constructor of that is `Object`, and we've established the constructor of that is `Function`.

## Object Patterns



# Operators

They are all the same as you'd expect, just to clear up though, *postfix incrementing* (`x++`) means that the value of `x` would be returned and then it'd be incremented, whereas *prefix decrementing* (`++x`) means the value of `x` would be incremented and then returned, i.e:

```javascript
let x = 0;
console.log(x++); //0
console.log(x); //1 (so it returned the value and then incremented)

let y = 0;
console.log(++x); //1
console.log(x); //1 (so it incremented and then returned)

```

To get the type of an unevaluated operand (a variable or otherwise), use the `typeof` operator (type returned as a string):

```javascript
console.log(typeof 42); //"number"
console.log(typeof true); //boolean

```

There is also the `instanceof` operator which checks the current object and returns true if the object is of the specified object type:

```javascript
var colourX = new String("red"); //constructs a String object
colourX instanceof String; // returns true, it is a String object
var colourY = "potato"; //no type specified, so not a String object
colourY instanceof String; // returns false (colourY is not a String object)

```

# Data Types

## Numbers

I'll show the related functions below:

```javascript
let x = new Number(1000); //number object
let y = new Number(14.3275);
let z = new Number(15455.323);
let w = 43; //number value
console.log(x.valueOf()); //1000

console.log(x.toString()); //"1000"
console.log(x.toString(16)); //"3e8", the paramater is the radix - what number base to convert to

console.log(y.toFixed(2)); //14.33
console.log(x.toExponential()); //1e+3
console.log(z.toPrecision(3)); //1.55e+4 (does significant figures, then converts to exponential if necessary)

console.log(x instanceof Number); //true, is a number object
console.log(w instanceof Number); //false, isn't a number object - is a number value

```

## Booleans

Don't use the `Boolean` object, as it'll create confusion, for example:

```javascript
let falseObj = new Boolean(false);
if (falseObj) {
    console.log('odd');
}

```

This code prints out `odd`, as objects in JS are evaluated as `true`, even though it's a Boolean object with the value `false`.

So never use Boolean objects.

## Strings

Strings have a lot of related functions:

```javascript
let someString = new String("Some string");

console.log(someString.length); //11
console.log(someString.valueOf()); //Some string
console.log(someString.toString()); //Some string
console.log(someString.toLocaleString()); //Some string

console.log(someString[0]); //S
console.log(str.charAt(0)); //S, same function as above

console.log(someString.toUpperCase()); //SOME STRING
console.log(someString.toLowerCase()); //some string

let someStringWithWhitespaces = "      Hello        ";
console.log(someStringWithWhitespaces.trim()); //Hello

```

You can also use `object.toLocaleString()` with numbers and dates to get the local formatting:

```javascript
const date1 = new Date(Date.UTC(2020, 03, 26, 3, 0, 0));

console.log(date1.toLocaleString('ar-EG'));
// expected output: "٢٦‏/٤‏/٢٠٢٠ ٤:٠٠:٠٠ ص"

const number1 = 123456.789;
console.log(number1.toLocaleString('de-DE'));
// expected output: "123.456,789"

```

Some languages have *particular rules for capitalisation* and such `toLocaleLowerCase()` and `toLocaleUpperCase()` should be used when you're not sure what language the code will use.

You can **concatenate string** using `+` or the `concat` function:

```javascript
let firstName1 = 'John';
let fullName1 = firstName1 + ' ' + 'Doe';
console.log(fullName1); // "John Doe"

let firstName2 = 'John';
let fullName2 = firstName2.concat(' ','Doe');
console.log(fullName2); // "John Doe"

```

You can work with **substrings** as well, using , , :

```javascript
let str = "A JavaScript String is Stringy, like JavaScript.";

`substring(startIndex,[length]))`
	console.log(str.substr(13,6)); //"String"

`substring(startIndex,endIndex)`
	console.log(str.substring(6, 12)); //"Script"

`string.indexOf(substring,[fromIndex])`
	console.log(str.indexOf("String")); //11 - fromIndex = 0 for this
	//fromIndex is where to start searching from, so like this:
	console.log(str.indexOf("String", 14)); //23 - as it's past the index of the first instance of the word "String"

`string.lastIndexOf(substring,[fromIndex])`
	console.log(str.lastIndexOf("JavaScript")); //37
	console.log(str.lastIndexOf("somethingnotthere")); //-1, not found

```

You can use `localeCompare()` to find *which string comes alphabetically before* another:

```javascript
let word1 = "Some Words";
let word2 = "Some More Words";
console.log(word1.localeCompare(word2)); //1
//1 means that word1 is alphabetically **after** word2
//0 means that word1 == word2
//-1 means that word1 is alphabetically **before** word2

```

Then there are **pattern matching based functions**:

```javascript
`str.match(regexp)`
	let str1 = "1 + 2 = 3";
    let matches1 = str1.match(/\d+/);
    console.log(matches1[0]); // "1", only first element is returned

	//But then you might have the global flag (g):
	let matches2 = str1.match(/\d+/g);
    matches2.forEach(function(m) {
        console.log(m);
    });
	/*
	Output:
	1
	2
	3
	*/

`string.search(searchvalue)`
	let str2 = "This is a test of search()";
    let pos = str.search(/is/);
    console.log(pos); //2

`string.replace(regularExpression, replaceText)` //to replace a substring in a string
	let str = "the baby kicks the ball";
	let newStr = str.replace('kicks', 'holds');
	console.log(newStr); //"the baby holds the ball"

```

# Functions

You can return multiple values by returning an object or array of values:

```javascript
function something(a, b, c){ //returns an array
    return [a*b, b*c, c*a];
}

function somethingElse(d, e, f){ //returns an object
    return {
        d+e+f,
        -d-e-f,
        d/e/f
    };
}

```

You can use functions as constructors of new objects, using the keyword `new`:

```javascript
function person(age, height, name){
    this.age = age;
    this.height = height;
    this.name = name;
}

let somePerson = new person(18, 185, "Potato");

```

*If you didn't use the keyword `new`*, i.e if you did `let somePerson = person(18, 185, "Potato");`, then *`somePerson` would be undefined*, as nothing is returned from that object, and then the global `window.age`, `window.height` and `window.name` would be set instead, as *the `this` in the function would refer to the global object `window`* rather than the function object.

To ensure that a function is exclusively used as an object constructor, you can use the ES6 `target.new` function. If a function was called using the `new` keyword, then `new.target` returns a reference to the function, or otherwise returns undefined.

```javascript
function person(age, height, name) {
    if (!new.target) {
        throw "must use the 'new' keyword";
    }
    this.age = age;
    this.height = height;
    this.name = name;
}

```

> One thing to note is that, as *variables assigned to objects or arrays, contain references to the objects or arrays*, passing such a variable through a functions parameters means that the *objects and arrays are effectively passed by reference*.
>
> JavaScript always passes by value, which is why object/array variable's value (the address of the array/object) is copied, rather than the array itself.

You can also *access the arguments passed to a function using the `arguments` object*:

```javascript
function add(){
    return Object.values(arguments).reduce((acc, cv) => acc + cv);
    //uses the arguments object to iterate over all supplied arguments and uses Array.reduce(...) to sum the values
}

console.log(add(1,2,3,4,5,5,2000)); //2020

```

## Function Type

```javascript
function swap(x, y) {
    return [y, x];
}

console.log(swap.length); //2
console.log(swap.prototype); // Object{}, discussed in later section

```

## Anonymous Functions

They are functions with no names.

They can be used in 3 ways:

- assigned to a variable, so that they can be called later
- used as an argument of other functions
- called immediately (IIFE - immediately invoked function execution)

```javascript
let add = function (a, b) {
    return a + b;
};
//or
let add = (a, b)  => a + b;

(function() {
    console.log('IIFE'); //immediately invoked, so prints "IIFE" immediately upon calling
})();

setTimeout(() => console.log('Execute later, after 1 second'), 1000); //will execute after 1 second

```

## IIFE's

*Immediately Invoked Function Expressions* are functions which are enclosed in brackets (*Grouping Operators*), and are invoked immediately:

```javascript
(function() {
	//some stuff
})(); //the '()' signifies it is called immediately

```

These can be given a name, but they make no difference:

```javascript
(function something(){
	//
})();

```

A practical example could be something like this:

```javascript
const calculator = (function () {
    function add(a, b) {
        return a + b;
    }

    function multiply(a, b) {
        return a * b;
    }
    return {
        add: add,
        multiply: multiply
    }
})();

```

Then you can do `calculator.add(x, y)` and `calculator.multiply(z, w)` due to the IIFE.

> You'd use IIFE's because *they don’t pollute the global object, and they are a simple way to isolate variables declarations.*

## Callback Functions

These can be asynchronous (so using stuff like event listeners):

```javascript
document.addEventListener('click', 
	(function() { //some asynchronous anonymous callback function being used for the event listener
    	console.log("something"); 
	} )
);

```

Or synchronous like:

```javascript
let numbers = [1, 2, 4, 7, 3, 5, 6];
numbers.sort((a, b) => a - b); //the arrow function here is a synchronous callback function
console.log(numbers); // [ 1, 2, 3, 4, 5, 6, 7 ]

```

## Arrow Functions

`(parameters) => { statements }` is the basic syntax of an arrow function. You can rewrite most function using this syntax:

```javascript
let add = function(x, y){
    return x + y;
}
//can be transformed to:
let add = (x, y) => x + y; //you can do {return x+y;} instead, means the same thing

```

Or to use it with objects:

```javascript
let setColour = function(colour) { 
	return { value: colour };
}
//is the same as
let setColour = colour => ({ value: colour }); 
//you need the parentheses to distinguish between the braces of the function and the object

```

> ### Things to remember
>
> - They are *anonymous functions*
>
>   - Which makes them harder to debug (no names)
>   - Can't use them for recursion
>
> - `this` is from the outside scope of where it's defined, so you wouldn't need to use `bind()` to bind a specific scope:
>
>   - ```javascript
>     let obj_1 = {
>       id: 42,
>       counter: function counter() {
>         setTimeout(function() {
>           console.log(this.id);
>         }.bind(this), 1000);
>       }
>     };
>     obj_1.counter(); //would print "42" after 1 second
>     //or you can do it with an arrow function, and don't need to use bind():
>     let obj_2 = {
>       id: 43,
>       counter: function counter() {
>         setTimeout(_ => console.log(this.id), 1000); //using "_ => something" is the same as "() => something"
>       }
>     };
>     obj_2.counter(); //would print "43" after 1 second
>     
>     ```
>
>     - i.e it goes lexically up a scope for `this`
>
> - It doesn't have its own binding of the `super` keyword (which is used to access and call functions on an object's parent)
>
> - And you can't use the `new.target` property with it to find if a function was used with the `new` keyword
