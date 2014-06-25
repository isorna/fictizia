# JavaScript for Web Developers course

**ADAPT THIS INTRO**  

This book is targeted at professional developers wishing to improve their knowledge of design patterns and how they can be applied to the JavaScript programming language.

Some of the concepts covered (closures, prototypal inheritance) will assume a level of basic prior knowledge and understanding. If you find yourself needing to read further about these topics, a list of suggested titles is provided for convenience.

If you would like to learn how to write beautiful, structured and organized code, I believe this is the book for you.

`TOC`

## Basic concepts

### Data structures: Objects and Arrays

```js
var text = "purple haze";
show(text["length"]);
show(text.length);

var nothing = null;
show(nothing.length);
```

An object can be written like this:

```js
var cat = {colour: "grey", name: "Spot", size: 46};
cat.size = 47;
show(cat.size);
delete cat.size;
show(cat.size);
show(cat);
```

The keyword delete cuts off properties. Trying to read a non-existent property gives the value undefined.

If a property that does not yet exist is set with the = operator, it is added to the object.

```js
var empty = {};
empty.notReally = 1000;
show(empty.notReally);
```

Properties whose names are not valid variable names have to be quoted when creating the object, and approached using brackets:

```js
var thing = {"gabba gabba": "hey", "5": 10};
show(thing["5"]);
thing["5"] = 20;
show(thing[2 + 3]);
delete thing["gabba gabba"];
```

As you can see, the part between the brackets can be any expression. It is converted to a string to determine the property name it refers to. One can even use variables to name properties:

```js
var propertyName = "length";
var text = "mainline";
show(text[propertyName]);
```

The operator `in` can be used to test whether an object has a certain property. It produces a boolean.

```js
var chineseBox = {};
chineseBox.content = chineseBox;
show("content" in chineseBox);
show("content" in chineseBox.content);
```

When we have two numbers, `120` and `120`, they can for all practical purposes be considered the precise same number. With objects, there is a difference between having two references to the same object and having two different objects that contain the same properties. Consider the following code:

```js
var object1 = {value: 10};
var object2 = object1;
var object3 = {value: 10};

show(object1 == object2);
show(object1 == object3);

object1.value = 15;
show(object2.value);
show(object3.value);
```

`object1` and `object2` are two variables grasping the same value. There is only one actual object, which is why changing `object1` also changes the value of `object2`. The variable `object3` points to another object, which initially contains the same properties as `object1, but lives a separate life.

Collections of things are what objects are used for. `(...)` Luck has it that there is a special kind of objects specifically for this kind of use. They are called arrays, and they provide some conveniences, such as a length property that contains the amount of values in the array, and a number of operations useful for this kind of collection.

New arrays can be created using brackets (`[` and `]`):

```js
var mailArchive = ["mail one", "mail two", "mail three"];

for (var current = 0; current < mailArchive.length; current++)
  print("Processing e-mail #", current, ": ", mailArchive[current]);
```


### Variables vs Properties

### Polyfill

A polyfill, or polyfiller, is a piece of code (or plugin) that provides the technology that you, the developer, expect the browser to provide natively. Flattening the API landscape if you will.

## Advanced concepts

### Object creation

Syntax

```js
Object.create(proto [, propertiesObject ])
```

Explain this and talk a little bit about inheritance:

```js
var object1 = {};

var object2 = new Object();

console.log((object1 == object2));
```

Examples

```js
// Shape - superclass
function Shape() {
  this.x = 0;
  this.y = 0;
}

// superclass method
Shape.prototype.move = function(x, y) {
    this.x += x;
    this.y += y;
    console.info("Shape moved.");
};

// Rectangle - subclass
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// subclass extends superclass
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();

rect instanceof Rectangle // true.
rect instanceof Shape // true.

rect.move(1, 1); // Outputs, "Shape moved."
```

### Object properties

Examples

```js
function Constructor(){}
o = new Constructor();
// is equivalent to:
o = Object.create(Constructor.prototype);
// Of course, if there is actual initialization code in the Constructor function,
// the Object.create cannot reflect it

// create a new object whose prototype is a new, empty object
// and a adding single property 'p', with value 42
o = Object.create({}, { p: { value: 42 } })

// by default properties ARE NOT writable, enumerable or configurable:
o.p = 24
o.p
// 42

o.q = 12
for (var prop in o) {
   console.log(prop)
}
// "q"

delete o.p
// false

// to specify an ES3 property
o2 = Object.create({}, { p: {
      value: 42,
      writable: true,
      enumerable: true,
      configurable: true }
});
```

### Object inheritance

READ MORE HERE:
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind

Syntax

```js
function.apply(thisArg, [argsArray])
```

### Mixins: Functional inheritance

Mixins give all the benefits of multiple inheritance, without hierarchy (prototypal inheritance in JavaScript). They both allow you to reuse an interface (or set of functions) on multiple objects.

Examples

```js
function mixin(target, source) {
    function copyProperty(key) {
        target[key] = source[key];
    }

    if (arguments.length > 2) {
        // If there are arguments beyond target and source then treat them as
        // keys of the specific properties/methods that should be copied over.
        Array.prototype.slice.call(arguments, 2).forEach(copyProperty);
    } else {
        // Otherwise copy all properties/methods from the source to the target.
        Object.keys(source).forEach(copyProperty);
    }
};
```

```js
Object.prototype.addMixin = function (mixin) {    
    for (var prop in mixin) {
        if (mixin.hasOwnProperty(prop)) {
            this.prototype[prop] = mixin[prop];
        }
    }
};
```

### Enviroments and Closures

When a function is defined inside another function, its local environment will be based on the local environment that surrounds it instead of the top-level environment.

```js
var variable = "top-level";

function parentFunction() {
  var variable = "local";
  function childFunction() {
    print(variable);
  }
  childFunction();
}
parentFunction();
```

What this comes down to is that which variables are visible inside a function is determined by the place of that function in the program text. All variables that were defined 'above' a function's definition are visible, which means both those in function bodies that enclose it, and those at the top-level of the program. This approach to variable visibility is called lexical scoping.

Here is a case that might surprise you:

```js
var variable = "top-level";
function parentFunction() {
  var variable = "local";
  function childFunction() {
    print(variable);
  }
  return childFunction;
}

var child = parentFunction();
child();
```

`parentFunction` returns its internal function, and the code at the bottom calls this function. Even though `parentFunction` has finished executing at this point, the local environment where `variable` has the value "local" still exists, and `childFunction` still uses it. This phenomenon is called **closure**.

Apart from making it very easy to quickly see in which part of a program a variable will be available by looking at the shape of the program text, lexical scoping also allows us to 'synthesise' functions. By using some of the variables from an enclosing function, an inner function can be made to do different things. Imagine we need a few different but similar functions, one that adds 2 to its argument, one that adds 5, and so on.

```js
function makeAddFunction(amount) {
  function add(number) {
    return number + amount;
  }
  return add;
}

var addTwo = makeAddFunction(2);
var addFive = makeAddFunction(5);
show(addTwo(1) + addFive(1));
```

To wrap your head around this, you should consider functions to not just package up a computation, but also an environment. Top-level functions simply execute in the top-level environment, that much is obvious. But a function defined inside another function retains access to the environment that existed in that function at the point when it was defined.

Thus, the `add` function in the above example, which is created when `makeAddFunction` is called, captures an environment in which `amount` has a certain value. It packages this environment, together with the computation `return number + amount`, into a value, which is then returned from the outer function.

When this returned function (`addTwo` or `addFive`) is called, a new environment -in which the variable `number` has a value― is created, as a sub-environment of the captured environment (in which `amount` has a value). These two values are then added, and the result is returned.

### Callbacks / async

### Bind, defer, throttle, curry

### Call vs Apply vs Bind

### Partials

### JavaScript Design Patterns

| Creational | Based on the concept of creating an object. |
| --- | --- |
| Class |
| Factory Method | This makes an instance of several derived classes based on interfaced data or events. |
| Object |
| Abstract Factory | Creates an instance of several families of classes without detailing concrete classes. |
| Builder | Separates object construction from its representation, always creates the same type of object. |
| Prototype | A fully initialized instance used for copying or cloning. |
| Singleton | A class with only a single instance with global access points. |

| Structural | Based on the idea of building blocks of objects |
| --- | --- |
| Class |
| Adapter | Match interfaces of different classes therefore classes can work together despite incompatible interfaces. |
| Object |
| Adapter | Match interfaces of different classes therefore classes can work together despite incompatible interfaces. |
| Bridge | Separates an object's interface from its implementation so the two can vary independently. |
| Composite | A structure of simple and composite objects which makes the total object more than just the sum of its parts. |
| Decorator | Dynamically add alternate processing to objects. |
| Facade | A single class that hides the complexity of an entire subsystem. |
| Flyweight | A fine-grained instance used for efficient sharing of information that is contained elsewhere. |
| Proxy | A place holder object representing the true object. |

| Behavioral | Based on the way objects play and work together. |
| --- | --- |
| Class |
| Interpreter | A way to include language elements in an application to match the grammar of the intended language. |
| Template |   |
| Method | Creates the shell of an algorithm in a method, then defer the exact steps to a subclass. |
| Object |
| Chain of Responsibility | A way of passing a request between a chain of objects to find the object that can handle the request. |
| Command | Encapsulate a command request as an object to enable, logging and/or queuing of requests, and provides error-handling for unhandled requests. |
| Iterator | Sequentially access the elements of a collection without knowing the inner workings of the collection. |
| Mediator | Defines simplified communication between classes to prevent a group of classes from referring explicitly to each other. |
| Memento | Capture an object's internal state to be able to restore it later. |
| Observer | A way of notifying change to a number of classes to ensure consistency between the classes. |
| State | Alter an object's behavior when its state changes. |
| Strategy | Encapsulates an algorithm inside a class separating the selection from the implementation. |
| Visitor | Adds a new operation to a class without changing the class. |

### Delegation vs Inheritance

## Extra Ball

### JSON and JSONP

### Modernizr.js

### Backbone.js

### Tests
