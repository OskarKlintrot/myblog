---
layout: post
title: Examples about this, bind, call and arrow functions is Javascript and Typescript
comments: True
blog: Tech
tags: [Javascript, Typescript]
description: The keyword this is perhaps one of the most confusing things about Javascript, at least it is for me. If you think so to then this article is for you! Here we will talk more about what this actually is good for and how to avoid some pitfalls.
---
The keyword `this` is something I have had a lot of trouble to understand and still have. This article is a direct result of a discussion I hade with some brilliant Javascript programmers from my class and the class above at the program I study, web development at LNU in Sweden. It all started with a quite bad [blog post](www.walkercoderanger.com/blog/2014/02/typescript-isnt-the-answer/) where the author states that:
>The real problem with TypeScript is contained in the statement that it is a “superset of JavaScript.” That means that all legal JavaScript programs are also legal typescript programs. TypeScript doesn’t fix anything in JavaScript beyond some things that were fixed in ECMA Script 5.

However, that's not the problem with Typescript. That's the reason why projects like AnuglarJS and Heap choose to use Typescript. If you know Javascript, you know Typescript. Typescript just adds some good features as type checking, classes (ok, there in ES2015 now I know) and since Typescript is compiled down to Javascript you get a chance to find error before run-time. Still, everything you can do in Javascript you can do in Typescript. Win win!
## The problem with Typescript is the problem with Javascript...
The argument against Typescript is that the following code:
 {% highlight JavaScript %}
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet = function() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound());
{% endhighlight %}

Produces the output `Hello, undefined` and not `Hello, world` as you might expect if you'r not used to Javascript (like me). However, as I said the corresponding code in Javascript produce the same result:
{% highlight JavaScript %}
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound()); //Hello, undefined
{% endhighlight %}

To solve it without changing the object we could do this in Javascript:
{% highlight Javascript %}
var greeter = new Greeter("world");
var unbound = greeter.greet();
console.log(unbound); //Hello, world
{% endhighlight %}

The most obvious way, and best practice, to fix it in Typescript is to use arrow functions:
{% highlight Javascript %}
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet = () => {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound());
{% endhighlight %}

This will output `Hello, world` as expected.

## Using `bind`, `call`, arrow functions and avoiding this in Javascript
### `.bind()`
Using `.bind()` we create a new function where the `this` keyword is bound to the given value. Here is how it would look in the previous example:
{% highlight Javascript %}
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = greeter.greet.bind(greeter);
console.log(unbound());
{%endhighlight%}
When declaring the varible `unbound` and assigning the function `greeter.greet()` to it, we create a new function with `.bind()` where we bind `this` to `greeter` instead of `unbound`. The function is not executed until the very last line; `console.log(unbound());`.

### `.call()` and `.apply()`
`.call()` and `.apply()` is more or less the same thing, just that `.call()` takes single arguments while `.apply()` takes an array as argument.

Take a look first on the code and see if you can understand the differens between `.bind()` and `.call()`:
{% highlight Javascript %}
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound.call(greeter));
{%endhighlight%}

Did you figure out the differens? `.bind()` creates a new function and bind `this` to whatever we want whereas `.call()` set the value of `this` when it is actually called (hence the name, I guess). This way we can later call `unbound` but whit a new value for `this`, something we can't do if we used `.bind()`.

### Passing arguments when using `.bind()`, `.call()` and `.apply()`.
First, let us change the `greet` function a bit:
{% highlight Javascript %}
Greeter.prototype.greet = function (foo, bar) {
    return "Hello, " + this.greeting + foo + bar;
};
{%endhighlight%}

What do you think the following code will produce as an output?
{% highlight Javascript %}
var greeter = new Greeter("world");
var unbound = greeter.greet.bind(greeter, "!", "!");
console.log(unbound("?", "?"));
{%endhighlight%}

It will actually output `Hello, world!!` since we passed two `"!"` as arguments when we created the function with `.bind()`. If we instead don't pass any arguments we can get the result we propable where hoping for:
{% highlight Javascript %}
var greeter = new Greeter("world");
var unbound = greeter.greet.bind(greeter);
console.log(unbound("?", "?"));
{%endhighlight%}
That code will output the expected `Hello, world??`.

Similar, if we want to pass an argument when using `.call()` and `.apply()` we can easily do that as well:
{% highlight Javascript %}
var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound.call(greeter, "!", " Using .call"));     //Hello, world! Using .call
console.log(unbound.apply(greeter, ["!", " Using .apply"])); //Hello, world! Using .apply
{%endhighlight%}
As you see, when using `.call()` we pass the arguemnts one by one but when using `.apply()` we pass them as an array.

### Arrow functions
Using arrow functions we can create an anonymous function where `this` is bound in the same way as when using `.bind()`:
{% highlight Javascript %}
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = () => greeter.greet();
console.log(unbound());
{%endhighlight%}
The output would be `Hello, world`, just as expected. Arrow functions is a neat feature introduced in ES2015.
### Avoiding `this`
`this` lets us share methods between objects, that is why it behaves kinda strange, so if you want to use the `Array.prototype.forEach` function somewhere else you can; `MyList.prototype.forEach = Array.prototype.forEach`. However, if you remove `this` you no longer can do that. Anyway, here is what it would look like without `this`:
{% highlight Javascript %}
var Greeter = (function () {
    function Greeter(message) {
        Greeter.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + Greeter.greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound());
{%endhighlight%}
or by using block scope:
{% highlight Javascript %}
var Greeter = (function () {
    var greeting = "";
    function Greeter(message) {
        greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + greeting;
    };
    return Greeter;
})();
var greeter = new Greeter("world");
var unbound = greeter.greet;
console.log(unbound());
{%endhighlight%}
Again, `Hello, world` is the output. This might be the easiest way if you are ok with the drawbacks. But even Crockford avoids `this`. But I don't like him so I use `this` and Typescript! :)

#### Dangers with `this`
The real problem with `this` is that it's very easy to put stuff global by mistake. Let's take a quick look at that as well (code from [this amazing interactive Javascript tutorial](http://ejohn.org/apps/learn/)):
{% highlight Javascript %}
function katana(){
  this.isSharp = true;
}
katana();
console.log(isSharp); //true
{%endhighlight%}

The code above will actually put `isSharp` globaly!

{% highlight Javascript %}
function ninja(){
  this.swung = true;
}
var littleNinja = new ninja();
console.log(littleNinja.swung); // true
console.log(swung); //undefined
{%endhighlight%}
That's better. The difference is that we added the `new` keyword. That's another keyword Crockford avoids because as you can see it is really easy to put stuff globaly unintended when using `this` and (not using) `new`.

Hope you learned something new!

{% include plugs/twitter.html %}  
{% include plugs/signature.html %}

## Further readings
* [`.bind()` at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
* [`.call()` at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
* [Arrow Functions at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [`this` at MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
