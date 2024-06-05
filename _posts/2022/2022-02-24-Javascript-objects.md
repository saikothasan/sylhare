---
layout: post 
title: Juggling 🤹‍♀️ with javascript objects 
color: RGB(0,155,119)
tags: [js]
---

To follow up on the [modern javascript notation][1], let's have a deep look at the `Object`, the
[javascript data type][7]. 

Most javascript objects are instances of `Object`, like in java where most class inherit from the Object class.
You'd better get acquainted with it, so let's make the most out of it with this article!

### Object's must-known

#### Copy with `Object.assin()`

To copy properties from one or more object to another one you can use the [assign][8] method on object:

```js
var person = { name: { first: 'John', middle: 'Wayne' }, picture: '😎' }
var languages = { code: ['js', 'ts'] }

var dev = Object.assign(person, languages);
// returns { name: { first: 'John', middle: 'Wayne' }, lastname: 'Rodriguez', code: ['js', 'ts'] }
```

However the `assign` only copies value which can be a reference of another object. For example the _picture_'s
value '😎' is copied in the dev object, but it's _name_'s reference to the object that is copied. so that if
you change _person_'s name with `person.name.first='Joe'` then the change will reverberate on the
`dev.name.first` property as well.

So the [deep copy][9] (where properties don't share the same reference) is not possible. One pretty slow workaround
is to serialize to string and deserialize to a JSON object with `JSON.parse(JSON.stringify(object))`. But keep in mind
that if the value is a method, it will be lost in the serialization, so it's not a perfect copy either.

#### To key, value array with `Object.entries()`

Another tip with handling objects, is the [entries][10] method which transforms an Object into an array of key values.<br>
For example:

```js
var o = { a: 'text', b: [1, 2], c: { hello: 'world' } }

Object.entries(o);
// returns [ ["a", "text"], ["b", [1,2]], ["c", {"hello":"world"}] ]
```

You can see it returns an array of pairs `[ [key, value], ... ]`. This is useful because an object doesn't have the
`map` or `filter` properties like an array does, so this is the easiest way to do it on an object. If you need to
transform is back to amn object you can use the [fromEntries][11] method.

It will take any key, value pair array and transform it into an object.

### Property shorthand notation

Since ES6, defining properties to an object can be simplified if the variable's name is the same as the object's
property key which makes for a _shorter_ notation.

Here is how it would look:

```js
const name = 'José', lastname = 'Martinez';
const person = { name, lastname };
// Same as { name: name, lastname: lastname } 
// Same as { name: 'José', lastname: 'Martinez' }
```

This can make your notation less redundant, trying to use this feature will push you into using adequate variable 👌.

> _Note_: Shorthand can be used in Javascript to refer to the fact of using [shorter][2] notations.<br>
> Like we've seen for the functions.

### Computed property name

Since ECMAScript 2015, you can pass a variable as key in your object. This is called the
[computed property name][3], because it will be computed at run time. It uses the square bracket notation that was
already used for accessing properties where `object.key` returns the same value as `object['key']` as mentioned
in the [property accessors][12] documentation.

So let's define some keys a variable and a method:

```js
var myKey = () => 'key'
var myOtherKey = 'other'
```

Now, use them in an object:

```js
var myObject = {
  normalKey: 'normalValue',
  [myKey()]: 'value',
  [myOtherKey]: 'otherValue'
}
// returns { normalKey: 'normalValue', key: 'value', other: 'otherValue' }
```

As you can see both function and variable gets computed into a string key. If you forget the parenthesis, and put
just `[myKey]`, it will be the function itself, which would work...

But not as intended, showing up as `myObject['() => \'key\'']` to return the value. 

### Destructuring

The destructuring assignment syntax is a JavaScript expression that makes it possible to unpack values from arrays, or
properties from objects, into distinct variables.

Behind this name is hidden the `{ }` brackets notation and `...` three dot operator. Basically you can cherry-pick
values from an array or properties from an object into variables. This is also called _unpacking_.

#### On Objects

It applies for objects, you can select the remaining properties of an object using the destructuring operator:

{% raw %}

```js
let car = { brand: 'Car', size: 'Big' };
let motor = { type: 'Powerful', consumption: 'expensive' };
let muscleCar = { ...car, motor };
console.log(muscleCar)
// returns { brand: "Car", size: "Big", motor: { type: "Powerful", consumption: "expensive" } }
```

{% endraw %}

And you can just create complex objects with the possibility to nest or not the composing sub-objects. This also work
reversed when assigning constants from an object.

Using the `{ }` bracket notation that you have already seen just before with the ES6 import:

```js
import { selectedModule } from 'package';
```

This will only import the module named `selectedModule` from the package (which may contain more than one). You can do
the same for assigning constants from an object:

```js
const fruitBasket = {
  apple: '🍎',
  banana: '🍌',
  kiwi: '🥝',
}
let { apple, banana: banoony } = fruitBasket
// same as: let apple = '🍎', banoony = '🍌'
```

The other elements will remain unassigned, and you can either use the shorthanded property side like for _apple_
or use your own variable name using `:` for _banoony_ instead of _banana_ if you feel special.

#### On Arrays

Same way as for objects, you can use the destructing operator using the `...` in an array:

```js
[a, b, ...rest] = [10, 20, 30, 40, 50];
console.log(a); // 10
console.log(b); // 20
console.log(rest); // [30, 40, 50]
```

The three dots is an accessor unpacks the array and take the remaining values. So you could also reuse the `rest`
defined before to create a new array:

```js
const myArray = [...rest, 1, 2];
console.log(myArray); // [30, 40, 50, 1, 2]
```

You can find also some more [examples][4], or try it out directly in your browser through the console in the developer
tool! 🤓

### Prototyping

To make it simple, [prototyping][5] consists of adding or modifying existing behaviour of an object. JavaScript is
composed of objects that have properties, those properties are stored in a prototype which also has its own properties
in a prototype, and so on... It's called the [prototype chain][6].

#### Example

So let's have an example with a Country, which has two prototypes, a name and a size:

```js
function Country(name, size) {
  this.name = name;
  this.size = size;
}

var canada = new Country('Canada', 'Big')
var panama = new Country('Panama', 'small')
```

Let's add some properties:

```js
Country.prototype.planet = 'earth'
canada.planet // returns 'earth'
delete canada.__proto__.planet
panama.planet // returns undefined
```

You can always remove one prototype using the key word `delete` like on any object. Either on a created object or the
class itself, the prototype will be deleted from the chain and so from all the linked objects.

#### Adding a prototype to base objects

Now let's add a numeric sort behaviour to an existing object like the `Array` so that it sorts numerically
in opposition to the default `sort()` that does it alphabetically:

```js
Array.prototype.sortNumerically = function () {
  return this.sort((a, b) => a - b)
} 
``` 

Take that JavaScript, finally the sort I wanted! _But..._ what if somebody starts messing around?! 😱 <br>
That could trigger unexpected behaviour, like using a library modifying already existing prototypes.
If you can't be sure what the function is doing, or worse if any use of prototype can throw an error, coding becomes
an ordeal!

> ⚠️ Usually you don't modify objects that you don't own.

So to remedy, you can use `Object.freeze(obj)` where `obj` is the object you want to block from having its prototype
modified.
Or you tailor your method so that it offers an API for other user to customize them, like the `sort` method takes on
a function to alter the comparison without overriding the sort algorithm itself.

[1]: {% post_url 2021/2021-02-21-Modern-era-js %}
[2]: https://www.sitepoint.com/shorthand-javascript-techniques/
[3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#computed_property_names
[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment
[5]: https://attacomsian.com/blog/objects-prototypes-classes-javascript
[6]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object
[8]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
[9]: https://developer.mozilla.org/en-US/docs/Glossary/Deep_copy
[10]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries
[11]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries
[12]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors
