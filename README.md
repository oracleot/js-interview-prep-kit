# JavaScript Interview Questions with Solutions

**JavaScript Fundamentals** | [Data Structures & Algorithm](https://github.com/oracleot/js-interview-prep-kit/tree/data-structure)
## JavaScript Fundamentals

### 1.
**Question**  
What will the following code output and why?

        var b = 1;
        function outer() {
            var b = 2  
            function inner() {
                b++
                var b = 3
                console.log(b)
            }
            inner()
        }
        outer()
    
**Answer**  
Output to the console will be “3”.

There are three closures in the example, each with it’s own var b declaration. When a variable is invoked closures will be checked in order from local to global until an instance is found. Since the inner closure has a b variable of its own, that is what will be output.

Furthermore, due to hoisting the code in inner will be interpreted as follows:

    function inner () {
        var b; // b is undefined
        b++; // b is NaN
        b = 3; // b is 3
        console.log(b); // output "3"
    }

### 2.
**Question**  
What will the following code return?

    console.log(type typeof 1)
    
**Answer**  
`string` `typeof 1` will return `"number"` and `typeof "number"` will return `string`

### 3.
**Question**  
What is the value of `typeof undefined === typeof NULL`?
    
**Answer**  
The expression will be evaluated to true, since `NULL` will be treated as any other undefined variable.   
Note: JavaScript is case-sensitive and here we are using `NULL` instead of `null`.


### 4.
**Question**  
Imagine you have this code: `var a = [1,2,3]`   
a) Will this result in a crash? `a[10] = 99`   
b) What will this output? `console.log(a[6])`

    
    
**Answer**  
a) It will not crash. The JavaScript engine will make array slots 3 through 9 be “empty slots.”   
b) Here, `a[6]` will output `undefined`, but the slot still remains empty rather than filled with `undefined`. This may be an important nuance in some cases. For example, when using `map()`, empty slots will remain empty in `map()`’s output, but `undefined` slots will be remapped using the function passed to it:

    var b = [undefined];
    b[2] = 1;
    console.log(b);             // (3) [undefined, empty × 1, 1]
    console.log(b.map(e => 7)); // (3) [7,         empty × 1, 7]


### 5.
**Question**  
How do you add an element at the beginning of an array? How do you add one at the end?
    
    
**Answer**  

    var myArray = ['a', 'b', 'c', 'd'];
    myArray.push('end');
    myArray.unshift('start');
    console.log(myArray); // ["start", "a", "b", "c", "d", "end"]
    
With ES6, one can use the spread operator:

    myArray = ['start', ...myArray];
    myArray = [...myArray, 'end'];
    
Or, in short:

    myArray = ['start', ...myArray, 'end'];


### 6.
**Question**  
What do the following lines output and why?
    
    console.log(1 < 2 < 3)
    console.log(3 > 2 > 1)
    
**Answer**  

The first statement returns `true` which is as expected.

The second returns `false` because of how the engine works regarding operator associativity for `<` and `>`. It compares left to right, so `3 > 2 > 1` JavaScript translates to `true > 1`. `true` has value `1`, so it then compares `1 > 1`, which is `false`.


### 7.
**Question**  
What will this code print?
    
    for(let i = 0; i < 5; i++) {
        setTimeout(function(){
            console.log(i)
        }, i * 1000)
    }
    
**Answer**  

It will print `0 1 2 3 4`, because we use `let` instead of `var` here. The variable `i` is only seen in the `for` loop’s block scope.

if `var` is used instead, it will print `5 5 5 5 5`


### 8.
**Question**  
How do you clone an object?
    
**Answer**  

    var obj = {a: 1 ,b: 2}
    var objclone = Object.assign({},obj);
    
Now the value of `objclone` is `{a: 1 ,b: 2}` but points to a different object than `obj`.

Note the potential pitfall, though: `Object.assign()` will just do a shallow copy, not a deep copy. This means that nested objects aren’t copied. They still refer to the same nested objects as the original:

    let obj = {
        a: 1,
        b: 2,
        c: {
            age: 30
        }
    };
    
    var objclone = Object.assign({},obj);
    console.log('objclone: ', objclone);
    
    obj.c.age = 45;
    console.log('After Change - obj: ', obj);           // 45
    console.log('After Change - objclone: ', objclone); // 45 - This also changes


### 9.
**Question**  
What will be the output of this code?

    var x = 21
    var girl = function() {
        console.log(x)
        var x = 20
    }
    girl()
    
**Answer**  

Neither 21, nor 20, the result is `undefined`. This is because JavaScript initialization is not hoisted.

(Why doesn’t it show the global value of 21? The reason is that when the function is executed, it checks that there’s a local x variable present but doesn’t yet declare it, so it won’t look for global one.)


### 10.
**Question**  
Consider the following code, what will be the output and why?

    (function() {
        try {
            throw new Error();
        }
        catch (x) {
           var x = 1, y = 2;
           console.log(x) 
        }
        console.log(x);
        console.log(y);
    })()
    
**Answer**  

`1   undefined   2`

`var` statements are hoisted (without their value initialization) to the top of the global or function scope it belongs to, even when it’s inside a `with` or `catch` block. However, the error’s identifier is only visible inside the `catch` block. It is equivalent to:

    (function () {
        var x, y; // outer and hoisted
        try {
            throw new Error();
        } catch (x /* inner */) {
            x = 1; // inner x, not the outer one
            y = 2; // there is only one y, which is in the outer scope
            console.log(x /* inner */);
        }
        console.log(x);
        console.log(y);
    })();


### 11.
**Question**  
Testing your `this` knowledge in JavaScript: what is the output of the following code?

    var length = 10;
    
    function fn() {
        console.log(this.length)
    }
    
    var obj = {
        length: 5,
        method: function fn() {
            fn();
            arguments[0]();
        }
    };
    
    obj.method(fn, 1);
    
**Answer**  

`10  2`

Why isn’t it `10` and `5`?

In the first place, as `fn` is passed as a parameter to the function `method`, the scope (`this`) of the function `fn` is `window`. `var length = 10`; is declared at the `window` level. It also can be accessed as `window.length` or `length` or `this.length` (when `this === window`).

`method` is bound to `Object obj`, and `obj.method` is called with parameters `fn` and `1`. Though `method` is accepting only one parameter, while invoking it has passed two parameters; the first is a function callback and other is just a number.

When `fn()` is called inside `method`, which was passed the function as a parameter at the global level, `this.length` will have access to `var length = 10` (declared globally) not `length = 5` as defined in `Object obj`.

Now, we know that we can access any number of arguments in a JavaScript function using the arguments[] array.

Hence `arguments[0]()` is nothing but calling `fn()`. Inside `fn` now, the scope of this function becomes the `arguments` array, and logging the length of `arguments[]` will return `2`.

Hence the output will be as above.


### 12.
**Question**  

Create a function that, given a DOM element on a page, will visit the element itself and all of its dependencies (not just its immediate children). For each element visited, the function should pass that element to a provided callback function.

The arguments to the function should be:

- a DOM elemnt
- a callback function (that takes a DOM element as its argument)
    
**Answer**  

Visiting all elements in a tree (DOM) is a classic Depth-First-Search algorithm application. Here’s an example solution:

    function Traverse (p_element,p_callback) {
        p_callback(p_element);
        var list = p_element.children;
        for (var i = 0; i < list.length; i++) {
            Traverse(list[i],p_callback);  // recursive call
        }
    }


### 13.
**Question**  

What will the following code output to the console and why:

    var hero = {
        _name: 'John Doe',
        getSecretIdentity: function() {
            return this._name
        }
    }
    
    var stoleSecretIdentity = hero.getSecretIdentity;
    
    console.log(stoleSecretIdentity());
    console.log(hero.getSecretIdentity());
    
What is the issue with the code and how can it be fixed?
    
**Answer**  

`undefined John Doe`

The first `console.log` prints `undefined` because we are extracting the method from the `hero` object, so `stoleSecretIdentity()` is being invoked in the global context (i.e., the `window` object) where the `_name` property does not exist.

One way to fix the `stoleSecretIdentity()` function is as follows:

`var stoleSecretIdentity = hero.getSecretIdentity.bind(hero);`


### 14.
**Question**  

Consider the code snippet below. What will the console output be and why?

    (function(x) {
        return (function(y) {
            console.log(x)
        })(2)
    })(1)
    
**Answer**  

The output will be `1`, even though the value of `x` is never set in the inner function. Here’s why:

As explained in our JavaScript Hiring Guide, a closure is a function, along with all variables or functions that were in-scope at the time that the closure was created. In JavaScript, a closure is implemented as an “inner function”; i.e., a function defined within the body of another function. An important feature of closures is that an inner function still has access to the outer function’s variables.

Therefore, in this example, since `x` is not defined in the inner function, the scope of the outer function is searched for a defined variable `x`, which is found to have a value of `1`.


### 15.
**Question**  

What will the following code output to the console and why?

`console.log((function f(n) { return ((n > 1) ? n * f(n - 1) : n) })(10))`
    
**Answer**  

10! or 3,628,800

Here’s why:

The named function `f()` calls itself recursively, until it gets down to calling `f(1)` which simply returns `1`.

    f(1): returns n, which is 1
    f(2): returns 2 * f(1), which is 2
    f(3): returns 3 * f(2), which is 6
    f(4): returns 4 * f(3), which is 24
    f(5): returns 5 * f(4), which is 120
    f(6): returns 6 * f(5), which is 720
    f(7): returns 7 * f(6), which is 5040
    f(8): returns 8 * f(7), which is 40320
    f(9): returns 9 * f(8), which is 362880
    f(10): returns 10 * f(9), which is 3628800


### 16.
**Question**  

What is the ouput of the following code, explain your answer?

    var a = {},
        b = { key: 'b' },
        c = { key: 'c' }
    a[b] = 123
    a[c] = 456
    
    console.log(a[b])
    
**Answer**  

The output of this code will be `456` (not `123`).

The reason for this is as follows: When setting an object property, JavaScript will implicitly *stringify* the parameter value. In this case, since `b` and `c` are both objects, they will both be converted to `"[object Object]"`. As a result, `a[b]` and `a[c]` are both equivalent to `a["[object Object]"]` and can be used interchangeably. Therefore, setting or referencing `a[c]` is precisely the same as setting or referencing `a[b]`.


### 17.
**Question**  

What is the ouput when the following code is executed?

    console.log(false == '0')
    console.log(false === '0')
    
**Answer**  

The code will output: `true false`

In JavaScript, there are two sets of equality operators. The triple-equal operator `===` behaves like any traditional equality operator would: evaluates to true if the two expressions on either of its sides have the same type and the same value. The double-equal operator, however, tries to coerce the values before comparing them. It is therefore generally good practice to use the `===` rather than `==`. The same holds true for `!==` vs `!=`.


### 18.
**Question**  

What will the following lines of code output to the console?

    console.log("0 || 1 = " + (0 || 1))
    console.log("1 || 2 = " + (1 || 2))
    console.log("0 && 1 = " + (0 && 1))
    console.log("1 && 2 = " + (1 && 2))
    
**Answer**  

The code will output the following four lines:

    0 || 1 = 1
    1 || 2 = 1
    0 && 1 = 0
    1 && 2 = 2

In JavaScript, both `||` and `&&` are logical operators that return the first fully-determined “logical value” when evaluated from left to right.

The or (`||`) operator. In an expression of the form `X || Y`, `X` is first evaluated and interpreted as a boolean value. If this boolean value is `true`, then `true (1)` is returned and `Y` is not evaluated, since the “or” condition has already been satisfied. If this boolean value is “false”, though, we still don’t know if `X || Y` is true or false until we evaluate Y, and interpret it as a boolean value as well.

Accordingly, `0 || 1` evaluates to true (1), as does `1 || 2`.

The and (&&) operator. In an expression of the form `X && Y`, `X` is first evaluated and interpreted as a boolean value. If this boolean value is `false`, then `false (0)` is returned and `Y` is not evaluated, since the “and” condition has already failed. If this boolean value is “true”, though, we still don’t know if `X && Y` is true or false until we evaluate `Y`, and interpret it as a boolean value as well.

However, the interesting thing with the && operator is that when an expression is evaluated as “true”, then the expression itself is returned. This is fine, since it counts as “true” in logical expressions, but also can be used to return that value when you care to do so. This explains why, somewhat surprisingly, `1 && 2` returns 2 (whereas you might it expect it to return `true` or `1`).


### 19.
**Question**  

What will be the output of the following code?

    for (var i = 0; i < 5; i++) {
        setTimout(function() {
            console.log(i)
        }, i * 1000)
    }
    
Explain your answer and how could the use of a closure help?
    
**Answer**  

The code sample shown will not display the values 0, 1, 2, 3, and 4 as might be expected; rather, it will display 5, 5, 5, 5, and 5.

The reason for this is that each function executed within the loop will be executed after the entire loop has completed and all will therefore reference the last value stored in i, which was 5.

Closures can be used to prevent this problem by creating a unique scope for each iteration, storing each unique value of the variable within its scope, as follows:

    for (var i = 0; i < 5; i++) {
        (function(x) {
            setTimeout(function() { console.log(x); }, x * 1000 );
        })(i);
    }
    
This will produce the presumably desired result of logging 0, 1, 2, 3, and 4 to the console.

In an ES2015 context, you can simply use let instead of var in the original code:

    for (let i = 0; i < 5; i++) {
        setTimeout(function() { console.log(i); }, i * 1000 );
    }


### 20.
**Question**  

What is a closure in JavaScript? Provide an example
    
**Answer**  

A closure is an inner function that has access to the variables in the outer (enclosing) function’s scope chain. The closure has access to variables in three scopes; specifically: (1) variable in its own scope, (2) variables in the enclosing function’s scope, and (3) global variables.

Here is an example:

    var globalVar = "xyz";

    (function outerFunc(outerArg) {
        var outerVar = 'a';
        
        (function innerFunc(innerArg) {
        var innerVar = 'b';
        
        console.log(
            "outerArg = " + outerArg + "\n" +
            "innerArg = " + innerArg + "\n" +
            "outerVar = " + outerVar + "\n" +
            "innerVar = " + innerVar + "\n" +
            "globalVar = " + globalVar);
        
        })(456);
    })(123);
    
In the above example, variables from innerFunc, outerFunc, and the global namespace are all in scope in the innerFunc. The above code will therefore produce the following output:

    outerArg = 123
    innerArg = 456
    outerVar = a
    innerVar = b
    globalVar = xyz


### 21.
**Question**  

What is a potential pitfall with using `typeof bar === 'object'` to determine if `bar` is an object? How can this pitfall be avoided?
    
**Answer**  

Although `typeof bar === "object"` is a reliable way of checking if `bar` is an object, the surprising gotcha in JavaScript is that `null` is also considered an object!

Therefore, the following code will, to the surprise of most developers, log `true` (not `false`) to the console:

    var bar = null;
    console.log(typeof bar === "object");  // logs true!
    
As long as one is aware of this, the problem can easily be avoided by also checking if bar is null:

    console.log((bar !== null) && (typeof bar === "object"));  // logs false
    
To be entirely thorough in our answer, there are two other things worth noting:

First, the above solution will return `false` if `bar` is a function. In most cases, this is the desired behavior, but in situations where you want to also return `true` for functions, you could amend the above solution to be:

    console.log((bar !== null) && ((typeof bar === "object") || (typeof bar === "function")));
    
Second, the above solution will return `true` if `bar` is an array (e.g., if `var bar = [];`). In most cases, this is the desired behavior, since arrays are indeed objects, but in situations where you want to also `false` for arrays, you could amend the above solution to be:

    console.log((bar !== null) && (typeof bar === "object") && (toString.call(bar) !== "[object Array]"));
    
However, there’s one other alternative that returns `false` for nulls, arrays, and functions, but `true` for objects:

    console.log((bar !== null) && (bar.constructor === Object));
    
Or, if you’re using jQuery:

    console.log((bar !== null) && (typeof bar === "object") && (! $.isArray(bar)));
    
    
ES5 makes the array case quite simple, including its own null check:

    console.log(Array.isArray(bar));
    
    
### 22.
**Question**  

What will the code below output to the console and why?

    (function() {
        var a = b = 3;
    })();
    
    console.log("a defined? " + (typeof a !== 'undefined'))
    console.log("b defined? " + (typeof b !== 'undefined'))
    
**Answer**  

Since both `a` and `b` are defined within the enclosing scope of the function, and since the line they are on begins with the `var` keyword, most JavaScript developers would expect `typeof a` and `typeof b` to both be undefined in the above example.

However, that is not the case. The issue here is that most developers incorrectly understand the statement `var a = b = 3;` to be shorthand for:

    var b = 3;
    var a = b;
    
But in fact, var a = b = 3; is actually shorthand for:

    b = 3;
    var a = b;
    
As a result (if you are not using strict mode), the output of the code snippet would be:

    a defined? false
    b defined? true
    
But how can `b` be defined outside of the scope of the enclosing function? Well, since the statement `var a = b = 3;` is shorthand for the statements `b = 3;` and `var a = b;`, b ends up being a global variable (since it is not preceded by the var keyword) and is therefore still in scope even outside of the enclosing function.

Note that, in strict mode (i.e., with `use strict`), the statement `var a = b = 3;` will generate a runtime error of `ReferenceError: b is not defined`, thereby avoiding any headfakes/bugs that might othewise result. (Yet another prime example of why you should use `use strict` as a matter of course in your code!)
    
    
### 23.
**Question**  

What will the code below output to the console and why?

    var myObject = {
        foo: 'bar',
        func: function() {
            var self = this;
            console.log("outer func: this.foo = " + this.foo);
            console.log("outer func: self.foo = " + self.foo);
            (function() {
                console.log("inner func: this.foo = " + this.foo);
                console.log("inner func: self.foo = " + self.foo);
            })
        }
    };
    myObject.func();
    
**Answer**  

The above code will output the following to the console:

    outer func:  this.foo = bar
    outer func:  self.foo = bar
    inner func:  this.foo = undefined
    inner func:  self.foo = bar
    
In the outer function, both `this` and `self` refer to `myObject` and therefore both can properly reference and access `foo`.

In the inner function, though, `this` no longer refers to `myObject`. As a result, `this.foo` is undefined in the inner function, whereas the reference to the local variable self remains in scope and is accessible there.
    
    
### 24.
**Question**  

What is the significance of, and reason for, wrapping the entire content of a JavaScript source file in a function block?
    
**Answer**  

This is an increasingly common practice, employed by many popular JavaScript libraries (jQuery, Node.js, etc.). This technique creates a closure around the entire contents of the file which, ** perhaps most importantly, creates a private namespace ** and thereby helps avoid potential name clashes between different JavaScript modules and libraries.

Another feature of this technique is to allow for an easily referenceable (presumably shorter) alias for a global variable. This is often used, for example, in jQuery plugins. jQuery allows you to disable the `$` reference to the jQuery namespace, using `jQuery.noConflict()`. If this has been done, your code can still use `$` employing this closure technique, as follows:

    (function($) { /* jQuery plugin code referencing $ */ } )(jQuery);
    
    
### 25.
**Question**  

What is the significance, and what are the benefits, of including `use strict` at the beginning of a JavaScript source file?
    
**Answer**  

The short and most important answer here is that `use strict` is a way to voluntarily enforce stricter parsing and error handling on your JavaScript code at runtime. Code errors that would otherwise have been ignored or would have failed silently will now generate errors or throw exceptions. In general, it is a good practice.

Some of the key benefits of strict mode include:

- ** Makes debugging easier. ** Code errors that would otherwise have been ignored or would have failed silently will now generate errors or throw exceptions, alerting you sooner to problems in your code and directing you more quickly to their source.

- ** Prevents accidental globals. ** Without `strict mode`, assigning a value to an undeclared variable automatically creates a global variable with that name. This is one of the most common errors in JavaScript. In `strict mode`, attempting to do so throws an error.

- ** Eliminates `this` coercion.** Without `strict mode`, a reference to a `this` value of `null` or `undefined` is automatically coerced to the global. This can cause many headfakes and pull-out-your-hair kind of bugs. In `strict mode`, referencing a `this` value of `null` or `undefined` throws an error.

- ** Disallows duplicate parameter values. ** Strict mode throws an error when it detects a duplicate named argument for a function (e.g., `function foo(val1, val2, val1){}`), thereby catching what is almost certainly a bug in your code that you might otherwise have wasted lots of time tracking down.
Note: It used to be (in ECMAScript 5) that strict mode would disallow duplicate property names (e.g. `var object = {foo: "bar", foo: "baz"};`) but as of ECMAScript 2015 this is no longer the case.

- ** Makes eval() safer. ** There are some differences in the way `eval()` behaves in `strict mode` and in non-strict mode. Most significantly, in `strict mode`, variables and functions declared inside of an `eval()` statement are not created in the containing scope (they are created in the containing scope in non-strict mode, which can also be a common source of problems).

- ** Throws error on invalid usage of `delete`. ** The `delete` operator (used to remove properties from objects) cannot be used on non-configurable properties of the object. Non-strict code will fail silently when an attempt is made to delete a non-configurable property, whereas strict mode will throw an error in such a case.
    
    
### 26.
**Question**  

Consider the two functions below, will they both return the same thing? Why or why not?

    function foo1() {
        return {
            bar: "hello"
        }
    }
    
    function foo2() {
        return
        {
            bar: "hello"
        }
    }
    
**Answer**

Surprisingly, these two functions will not return the same thing. Rather:
    
    console.log("foo1 returns:"); // foo1 returns:
    console.log(foo1()); // Object {bar: "hello"}
    console.log("foo2 returns:"); // foo2 returns:
    console.log(foo2()); // undefined
    
Not only is this surprising, but what makes this particularly gnarly is that `foo2()` returns undefined without any error being thrown.

The reason for this has to do with the fact that semicolons are technically optional in JavaScript (although omitting them is generally really bad form). As a result, when the line containing the `return` statement (with nothing else on the line) is encountered in `foo2()`, a semicolon is automatically inserted immediately after the `return` statement.

No error is thrown since the remainder of the code is perfectly valid, even though it doesn’t ever get invoked or do anything (it is simply an unused code block that defines a property bar which is equal to the `string` `"hello"`).

This behavior also argues for following the convention of placing an opening curly brace at the end of a line in JavaScript, rather than on the beginning of a new line. As shown here, this becomes more than just a stylistic preference in JavaScript.
    
    
### 27.
**Question**  

What is `NaN`? What is its type? How can you reliably test if a value is equal to `NaN`?
    
**Answer**

The `NaN` property represents a value that is “not a number”. This special value results from an operation that could not be performed either because one of the operands was non-numeric (e.g., `"abc" / 4`), or because the result of the operation is non-numeric.

While this seems straightforward enough, there are a couple of somewhat surprising characteristics of `NaN` that can result in hair-pulling bugs if one is not aware of them.

For one thing, although `NaN` means “not a number”, its type is, believe it or not, `Number`:
    
    console.log(typeof NaN === "number");  // logs "true"
    
Additionally, `NaN` compared to anything – even itself! – is false:

    console.log(NaN === NaN);  // logs "false"
    
A semi-reliable way to test whether a number is equal to `NaN` is with the built-in function `isNaN()`, but even using `isNaN()` is an imperfect solution.

A better solution would either be to use `value !== value`, which would only produce `true` if the value is equal to `NaN`. Also, ES6 offers a new `Number.isNaN()` function, which is a different and more reliable than the old global `isNaN()` function.
    
    
### 28.
**Question**  

What will the code below output? Explain your answer

    console.log(0.1 + 0.2)
    console.log(0.1 + 0.2 == 0.3)
    
**Answer**

An educated answer to this question would simply be: **“You can’t be sure. it might print out 0.3 and true, or it might not.** Numbers in JavaScript are all treated with _floating point precision_, and as such, may not always yield the expected results.”

The example provided above is classic case that demonstrates this issue. Surprisingly, it will print out:

    0.30000000000000004
    false
    
A typical solution is to compare the absolute difference between two numbers with the special constant `Number.EPSILON`:

    function areTheNumbersAlmostEqual(num1, num2) {
        return Math.abs( num1 - num2 ) < Number.EPSILON;
    }
    console.log(areTheNumbersAlmostEqual(0.1 + 0.2, 0.3));
    
    
### 29.
**Question**  

Discuss possible ways to write a function `isInteger(x)` that determines if `x` is an integer
    
**Answer**

This may sound trivial and, in fact, it is trivial with `ECMAscript 6` which introduces a new `Number.isInteger()` function for precisely this purpose. However, prior to `ECMAScript 6`, this is a bit more complicated, since no equivalent of the `Number.isInteger()` method is provided.

The issue is that, in the ECMAScript specification, integers only exist conceptually; i.e., numeric values are always stored as floating point values.

With that in mind, the simplest and cleanest `pre-ECMAScript-6` solution (which is also sufficiently robust to return `false` even if a non-numeric value such as a `string` or `null` is passed to the function) would be the following use of the bitwise XOR operator:

    function isInteger(x) { return (x ^ 0) === x; }
    
The following solution would also work, although not as elegant as the one above:

    function isInteger(x) { return (typeof x === 'number') && (x % 1 === 0); }
    
    
The following function (or with `Math.ceil()` or `Math.floor()` in place of `Math.round()`) might also seem useful, but the results are not exactly the same as with the above two functions:

    function isInteger(x) { return Math.round(x) === x; }
    
The difference is, these Math-based solutions return `true` for `Infinity` and `-Infinity`, whereas the others (and notably `ES6’s Number.isInteger()`) return false.

Another fairly common incorrect solution is the following:

    function isInteger(x) { return parseInt(x, 10) === x; }
    
While this parseInt-based approach will work well for many values of `x`, once `x` becomes quite large, it will fail to work properly. The problem is that `parseInt()` coerces its first parameter to a string before parsing digits. Therefore, once the number becomes sufficiently large, its string representation will be presented in exponential form (e.g., `1e+21`). Accordingly, `parseInt()` will then try to parse `1e+21`, but will stop parsing when it reaches the e character and will therefore return a value of `1`. Observe:

    > String(1000000000000000000000)
    '1e+21'

    > parseInt(1000000000000000000000, 10)
    1

    > parseInt(1000000000000000000000, 10) === 1000000000000000000000
    false
    
    
### 30.
**Question**  

In what order will the numbers 1 to 4 be logged to the console when the code below is executed? Why?
    
    (function() {
        console.log(1)
        setTimeout(function() { consolelo.log(2) }, 1000)
        setTimeout(function() { consolelo.log(3) }, 0)
        console.log(4)
    })
    
**Answer**

The values will be logged in the following order: `1 4 3 2`

Let’s first explain the parts of this that are presumably more obvious:

`1` and `4` are displayed first since they are logged by simple calls to `console.log()` without any delay

`2` is displayed after `3` because `2` is being logged after a delay of 1000 msecs (i.e., 1 second) whereas `3` is being logged after a delay of 0 msecs.

OK, fine. But if `3` is being logged after a delay of 0 msecs, doesn’t that mean that it is being logged right away? And, if so, shouldn’t it be logged before `4`, since `4` is being logged by a later line of code?

The answer has to do with properly understanding ** JavaScript events and timing. **

The browser has an event loop which checks the event queue and processes pending events. For example, if an event happens in the background (e.g., a script `onload` event) while the browser is busy (e.g., processing an `onclick`), the event gets appended to the queue. When the `onclick` handler is complete, the queue is checked and the event is then handled (e.g., the `onload` script is executed).

Similarly, `setTimeout()` also puts execution of its referenced function into the event queue if the browser is busy.

When a value of zero is passed as the second argument to `setTimeout()`, it attempts to execute the specified function “as soon as possible”. Specifically, execution of the function is placed on the event queue to occur on the next timer tick. Note, though, that this is not immediate; the function is not executed until the next tick. That’s why in the above example, the call to `console.log(4)` occurs before the call to `console.log(3)` (since the call to `console.log(3)` is invoked via `setTimeout`, so it is slightly delayed).
    false

### 31.
**Question**  

Write a simple function (less than 160 characters) that returns a boolean indicating whether or not a string is a palindrome
    
**Answer**

The following one line function will return `true` if `str` is a palindrome; otherwise, it returns false.

    function isPalindrome(str) {
        str = str.replace(/\W/g, '').toLowerCase();
        return (str == str.split('').reverse().join(''));
    }
    console.log(isPalindrome("level"));                   // logs 'true'
    console.log(isPalindrome("levels"));                  // logs 'false'
    console.log(isPalindrome("A car, a man, a maraca"));  // logs 'true'

### 32.
**Question**  

Write a sum method which will work properly when invoked using either synthax below

    sum(2,3)
    sum(2)(3)
    
**Answer**

There are at least two (2) ways to do this:

Method 1:

    function sum(x) {
        if (arguments.length == 2) {
            return arguments[0] + arguments[1]
        } else {
            return function(y) { return x + y }
        }
    }

In JavaScript, functions provide access to an `arguments` object which provides access to the actual arguments passed to a function. This enables us to use the `length` property to determine at runtime the number of arguments passed to the function.

If two arguments are passed, we simply add them together and return.

Otherwise, we assume it was called in the form `sum(2)(3)`, so we return an anonymous function that adds together the argument passed to sum() (in this case 2) and the argument passed to the anonymous function (in this case 3).

Method 2:

    function sum(x, y) {
        if (y !== undefined) {
            return x + y
        } else {
            return function(y) { return x + y }
        }
    }

When a function is invoked, JavaScript does not require the number of arguments to match the number of arguments in the function definition. If the number of arguments passed exceeds the number of arguments in the function definition, the excess arguments will simply be ignored. On the other hand, if the number of arguments passed is less than the number of arguments in the function definition, the missing arguments will have a value of `undefined` when referenced within the function. So, in the above example, by simply checking if the 2nd argument is undefined, we can determine which way the function was invoked and proceed accordingly.


### References

[Toptal's 37 Essential JavaScript Interview Questions](https://www.toptal.com/javascript/interview-questions)
