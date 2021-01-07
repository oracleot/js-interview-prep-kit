# JavaScript Interview Questions with Solutions

## Top 37 from TopTal

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