# Hoisting

Basically, when the JS code is being run, it goes through 2 phases, the _memory creation phase_ (which I'll call _MCP_) and then the _execution phase_ (which I'll call _EP_). 

In the _MCP_ variable declarations are recognised and put into memory for use during the execution phase, and initialised to undefined. Initialising a variable is basically the same as a reassignment, as variables are initialised as undefined.

Hoisting for variables is basically being able to use it before its declaration, because it already exists by the _EP_.

```javascript
console.log(x) //undefined, as x is a variable
var x;
console.log(y) //undefined, as y isn't even declared
console.log(z) //ReferenceError, as z is declared using let
let z;

```

Hoisting for functions is basically the same, but the entire function definition is put into memory as well, so you can call a function before actually defining it.

If you make function by using the function declaration method, functions are hoisted, if you use the function expression method, they are not.

```javascript
sayHi(); //prints Hi
function sayHi() { console.log("Hi"); }; //function declaration (using the function key word)

```

```javascript
sayHi(); //TypeError (sayHi is not a function)
let sayHi = function () { console.log("Hi"); }; //function expression method

```

A function expression is basically assigning an anonymous function to a variable, so that we can use it later.

The reason that it generates a type error is because in the _MCP_, the JS engine expects a variable declaration to follow, so basically sayHi is a variable, hoisted with the value undefined, not a function, so accessing it as a function generates the type error.

# Scope

It's exactly as you'd expect following the let and var bit, with the slightly unexpected behaviour in this example:

```javascript
let greet = 'Hello!' //global variable

function sayHi () { //function declaration
  console.log('2: ', greet) 
  //undefined, in the execution phase, local variables take precedence over global ones, the local declaration of greet is after this,   so it's hoisted, so the line above prints '2: undefined'
  let greet = 'Ciao!' //declaration of local greet
  console.log('3: ', greet) //prints local greet
}

console.log('1: ', greet) //prints global greet
sayHi() //calls sayHi()
console.log('4: ', greet)
//prints the global greet again, as it was unmodified in the function, as the function had a var of the same name in the local scope

```

# Execution Context

Before any code is created, a *global execution context* is created. 

When a function is executed/called/invoked, a *new execution context* is created, (basically the global one is a default version of these).

Every execution context provides a `this` keyword, which *points to the object* which the current *code that's being executed belongs to*.

- The execution context is an abstract concept that holds information about the environment the current code is being executed in.

## _Memory Creation Phase_

The memory creation phase has the following 3 happen during it:

1.  Creation of a scope
2.  Creation of a scope chain
3.  Determination of the value of this

> *Scope*
>
> The scope is created, the functions and variables accessible by the specific execution context is decided. Hoisting is performed at this point (engine scans through code for variable and function declarations, and puts them in memory).

> *Scope Chain*
>
> Every execution context has a reference to its outer scopes, if there are any up to the global scope, the chain is called a scope chain. 

> *Value of `this`*
>
> Every execution context has it, points to an object, to which the current code that's being executed belongs to. For the global execution context it could be the Window object.
>
> The value `this` gets set to a leading parent object of a function call, where a leading parent object is literally the parent object of a function. If the function has no parent object, by default it is the global window object (examples below).

```javascript
let obj = { 
    method1: function() { 
        console.log(this); 
    }
}
obj.method1(); //output: `{method1: f}`

let func = obj.method1; //(Eg.1)
func(); //output: `Window` (Eg.2)

let obj2 = {
    method1: function() {
        let _this = this; //the variable '_this' simply preserves the 'this' variable of this particular scope
        internalFunc();
        function internalFunc(){
            console.log(_this); //output: `{method1: f}`
            console.log(this); //output: `Window`
        }
    }
}

obj2.method1(); //(Eg.3)

```

The reason it prints window in the second example, is that we basically remove the function from the objects scope and put it into the global scope by assigning it to `func`, and when we call it from that we get `Window`, as we'd expect given what I said.

Then in the third example, we call a method in which the following is happening:

- we are setting a variable `_this` to the value of `this`, which would be `{method1: f}`, as explained for Eg.2
- we are calling `internalFunc()` (this uses hoisting)
- we declare and define `internalFunc()`, to print the `_this` and `this` variables

*The object preceding a function call is the value of `this`, if there is no preceding object, it's just `Window`*

By the above information, we can safely say that `method1()` will have the `{method1: f}` object in `this`, but inside of `method1()`, we call another function, which has no preceding object in its call, which means that by default, the `this`, in `internalFunc()` is `Window`, and then we print the `_this` variable which we set to contain the `this` value of the object, and it prints correctly.

So TL;DR, use the line in asterisks as your guidance.

> *bind(), call() and apply()*
>
> You can call these on any function, they take an argument and set it as the parent object of the execution context. bind() returns a function for you to then call, whereas call() and apply() invoke the function with the parent object being the one you supplied.
>
> ```javascript
> let greeting = "Hi"; 
> //the global greeting, so accessible through Window.greeting, or if the leading parent object is Window, this.greeting
> 
> function sayHi() { console.log(this.greeting); } //will simply print this.greeting, which may or may not be Window.greeting
> 
> let anotherGreeting = { greeting: "Hello!" } //an object with an internal variable called greeting
> 
> sayHi(); //calls with no leading object, so Window.greeting is printed (Hi)
> //for the below, anotherGreeting is set as the parent object, so anotherGreeting.greeting is printed (Hello!)
> sayHi.bind(anotherGreeting)(); //bind returns a function with the parent object set, which is then invoked
> sayHi.apply(anotherGreeting); //immediately invoked
> sayHi.call(anotherGreeting); //immediately invoked
> 
> 
> 
> ```
>
> With what I mentioned with `bind()`, you can also do this:
>
> ```javascript
> let newSayHi = sayHi.bind(anotherGreeting); //sayHi has the specified parent object set, and that version of itself is returned
> newSayHi(); //will print "Hello!"
> 
> ```
>
> The difference between `call()` and `apply()` is that *`call()` passes all arguments after the first one on to the invoked function*, while *apply() takes an array as its second argument and passes the members of that array as arguments*.
>
> ```javascript
> function addNumbers(a, b, c, d){
>     return this.initialValue+a+b+c+d;
> }
> 
> let initValObj = {
>     initialValue: 2000
> };
> 
> addNumbers.call(initValObj, 2, 4, 6, 8); //2020
> addNumbers.apply(initValObj, [2, 4, 6, 8]); //2020
> //so the end result is the same
> 
> ```

## _Execution Phase and Stack_

Each function call creates a new execution context. For example, if you make 2 function calls, there will be 3 execution contexts, 2 for the function calls, and one global context.

Each next execution context gets placed on top of the previous one, this is referred to as the execution stack. JS is single threaded, so the engine can only be executing within one context at once.

For example, take the following piece of code:

```javascript
function main(){
    hello(); //4th execution context is layered on top using this call, it prints "Hello", by using hoisting
    
    function hello(){
        console.log("Hello");
    }
}

function hi(){
    console.log("Hi");
}

function startMain(){
    main(); //3rd context layered on top is made using this
}

startMain(); //2nd context is made on top of the global execution context using this function call

//by the time the JS engine gets to this point, all of the execution contexts other than the global context have had their code executed, and they have been subsequently pushed off of the top of the execution stack

hi(); //this is back to being the 2nd context layered on top of the global context

//at this point all function calls have been made, and all the code in the execution contexts have been procssed, so all the other execution contexts have been pushed off the stack, and so you have just the global execution context left

```

The comments above explain the execution stack, but basically when a new function call is encountered, the current execution context is paused, and a new one is added to the stack and processed, then pushed off the stack and then the original one continues.

# Closure and more on scope

## More on scope

Every function, on creation, is assigned an internal property `[[scope]]`, so when the execution context encounters a function declaration/expression, the function is given a `[[scope]]` property. The value of the `[[scope]]` *property is* the *scope chain of the execution context in which the function was created*.

The scope property is assigned when a function is *created* not when it is *invoked*.

A scope chain is created during the creation of a new execution context upon a function call, and it *is a reference to variables* that belong to *outer scopes*.

In the example below, 'scope chain' shows the current state of the scope chain, and `[[scope]]` is a property of a function made on definition. 

Notice how even before a variable is defined, an 'undefined' value exists for it in the scope chain (due to hoisting).

```javascript
var a = 'global'
// scope chain: { a: 'global' }
function outer () { // [[scope]]: { a: 'global' }
  
  // scope chain: { b: undefined, outerScope: [[scope]] }  
  var b = 'outer'
  // scope chain: { b: 'outer', outerScope: [[scope]] }
  
  function inner () { // [[scope]]: { b: 'outer', outerScope: { a: 'global' } }

    // scope chain: { c: undefined, outerScope: [[scope]] }
    var c = 'inner'
    // scope chain: { c: 'inner', outerScope: [[scope]] }
    
    // [[scope]] == outerScope: { b: 'outer', outerScope: { a: 'global' } }, so:
    //scope chain: { c: 'inner', outerScope: { b: 'outer', outerScope: { a: 'global' } } }, so all 3 variables are accessible basically
    console.log('a:', a)
    console.log('b:', b)
    console.log('c:', c)
  }
  
  inner()
}

outer()

```

When a function is invoked, then at that point, via hoisting, the variables inside that function are added to the scope chain, and the scope chain inherits the `[[scope]]` property of that function, so you can get access to the variables in outer scopes.

The JS engine adds the variables of the current execution context to the scope chain structure, which also inherits the [[scope]] property of whatever function that it is in

## Closure

A *closure* is the combination of a *function enclosed* with *references to the variables* in its environment.

As it's references to the variables, the value of variables inside a closure can be modified.

```javascript
let a = 'global'

function outer () {
  let b = 'outer'
  
  return function inner () {
    let c = 'inner'
    
    console.log('a:', a)
    console.log('b:', b)
    console.log('c:', c)
  }
}

var innerFunc = outer() 
//so an instance of the outer() function is stored in innerFunc(), and that instance has references
//to a, b and c through the scope chain - this is a closure

a = 'GLOBAL'
//as the "a" variable in the closure above is a reference to the global "a" variable, modifying 
//it will mean the function prints "GLOBAL" rather than "global"

innerFunc()
// a: GLOBAL
// b: outer
// c: inner

```

> _Variable Lookups_
>
> When the JS engine needs to look for a variable, it looks first at the *local scope* and then goes up one level at a time up the scope chain to see if the variable its looking for is defined there. (The lookups obviously occur whenever you try to use a variable).

This means an example in which it seems that the closure's variables can be changed, may not be:

```javascript
var name = 'Richard'

function greet (name) {  
  return (function () {
    console.log('Hello ' + name)
  })
}

var sayHello = greet(name) 
//name is passed as an argument, so it becomes a local variable for the closure, and so the reference 
//of 'name' in the closure is to the argument 'name', not the global 'name' variable

name = 'Sam' //so modifying the global 'name' variable has no effect on the closure

sayHello()
//prints: "Hello, Richard"

```

Here's a more advanced example, which utilises a shared lexical environment (i.e multiple functions with the access to the same outer variables), from the Mozilla developer website:

```javascript
let makeCounter = function() {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return { //3 functions are returned, which all share the same 'privateCounter' variable
    increment: function() {
      changeBy(1);
    },

    decrement: function() {
      changeBy(-1);
    },

    value: function() {
      return privateCounter;
    }
  }
};

let counter1 = makeCounter();
console.log(counter1.value()); //0

counter1.increment(); //+1
counter1.increment(); //+1
console.log(counter1.value()); //2

counter1.decrement(); //-1
console.log(counter1.value()); //1

```

And this means that the `privateCounter` variable, literally acts like a private variable of the `counter1` object, so basically you can use closures to get private variable behaviour.