---
layout: rubyjsmain
title: RubyJS
canonical: http://rubyjs.org
---


## R( ) Function

`R(obj, recursive, block)` is the equivalent to `$` in jQuery  or underscorejs' `_()`. It is also a shortcut to the RubyJS namespace where all classes are defined, ie R.String, R.Array. `R(arg, recursive=false)` converts the given argument into a corresponding RubyJS object. The returned object contains all the Ruby methods.

{% highlight javascript %}

R(1)                 // => R.Fixnum
R(1).odd()           // => true

str = R('RubyJS')
str.swapcase()       // => "rUBYjs" R.String

R([1,15,8]).sort()   // => [1,8,15] R.Array
// Inline blocks return native objects:
R([1,15,8], function () { return this.sort(); })
// => [1,8,15] JS Array

{% endhighlight %}

This works for all Javascript types that have a corresponding Ruby class. Numbers, Strings, Regexp, Array. It works with Javascript primitives and their wrapper objects. Types that contain other objects, most notably Array, per default do not typecast their members. Use the recursive flag if you want to do so `R([1,2], true)` is equivalent to `R([R(1), R(2)])`

{% highlight javascript %}

R(1)                 // => RubyJS.Fixnum
R(1.2)               // => RubyJS.Float
R("foo")             // => RubyJS.String
R(/\w+/)             // => RubyJS.Regexp

// --- Arrays -------------------------------------------------------
R([1,2])             // => RubyJS.Array [1, 2]
R([1,2], true)       // => RubyJS.Array [R(1), R(2)] Recursive

// --- With primitive wrappers --------------------------------------
R(new Number(1))     // => RubyJS.Fixnum
R(new Number(1.2))   // => RubyJS.Float
R(new Array())       // => RubyJS.Array
R(new String('foo')) // => RubyJS.String

{% endhighlight %}

If a RubyJS class has no equivalent in Javascript such as _Range_ then shortcuts are provided.

{% highlight javascript %}

// --- Ranges -------------------------------------------------------
R.rng(1, 5)          // => RubyJS.Range (1..5)  includes 5
R.rng(1, 5, true)    // => RubyJS.Range (1...5) excludes 5

{% endhighlight %}

#### R( ) Inline functions

When you pass a function to `R(obj, function() {} )` it is executed with the context of `obj` and (recursively) converts the function return value to a native object. This makes sense when you want to use RubyJS methods but continue working with a native object.

{% highlight javascript %}

R([1,2,3], function () {
  return this.each_cons(2).to_a();
})
// => [[1,2], [2,3]]
// Equivalent to:
R([1,2,3]).each_cons(2).to_a().to_native(true);

{% endhighlight %}

Frankly, inline functions add syntactic beauty with CoffeeScript only.

{% highlight coffeescript %}

# CoffeeScript
R([1,5], -> @sort() )

{% endhighlight %}

Todo: this currently only does not work with the type specific typecasting methods `R.i( )`, `R.rng( )`, `R.w( )`, etc.

#### R( ) Unsupported Types and Special Cases

RubyJS objects, null and classes that are not implemented are simply returned.

{% highlight javascript %}

// --- Time and Hash/Object not yet supported -----------------------
R({})                // => {}
R(new Date())        // => native Date
R(null)              // => null

// --- Returns RubyJS untouched -------------------------------------
obj = R("foo")
R(obj) === obj       // => true, obj is not a clone
// Below we create two R.String objects.
R('foo') === R('foo') // => false

{% endhighlight %}

Special case: When passed a number to `R()`, it returns a Fixnum if there are no digits after the separator (when `1.0 % 1 === 0`) and a Float otherwise. Use `R.i(1.0)` or `R.f(1.0)` to force it into a specific class.

{% highlight javascript %}

// --- Floats -------------------------------------------------------
R(1.0)               // => RubyJS.Fixnum
R(1.0).to_f()        // => RubyJS.Float
R.f(1.0)             // => RubyJS.Float
R.i(1.0)             // => RubyJS.Fixnum

{% endhighlight %}

The `R()` method is optimized to not cause too much overhead. Of course you can also create RubyJS objects using traditional constructors `new R.String('foo')`, `R.String.new('')` or `R.$String()`.


## Class Overview

Following a list of implemented classes from the [Ruby 1.9.3 core-lib](http://ruby-doc.org/core-1.9.3/). They conform closely to [rubyspec.org](http://rubyspec.org). Unsupported features are encodings, bit operations, trusting, tainting and freezing objects. Freezing objects is on the roadmap.

RubyJS Modules

- `R.Kernel` included in Object
- `R.Numeric` included in all number classes
- `R.Integer`
- `R.Enumerable` Array, Enumerator, Range. Must implement #each
- `R.Comparable` Must implement a '<=>' method

RubyJS classes

- `R.Object` methods shared among all classes
- `R.Array`
- `R.Enumerator` mainly used for chaining enumerable methods
- `R.String`
- `R.Regexp`
- `R.MatchData`
- `R.Fixnum`
- `R.Float`
- `R.Range`
- `R.ArgumentError`, `R.IndexError`, `R.TypeError`, etc. The same error types are raised in RubyJS. At the moment however without the additional error messages.

Missing classes on the roadmap (in order of priority):

- `R.Hash`
- `R.Time`
- `R.Math`
- `R.Complex`, `R.Bignum`, `R.Rational`
- `IO`, `File` and others that make no sense in the browser.

#### The R Namespace

`R` or `RubyJS` is the namespace where all classes reside. Like Rubys main object it includes the Kernel module. So you have access to various methods that are also available in all objects, e.g. `R.rand(10)`, `R.puts('hello')`. Also the R namespace includes the special variables `R['$~']`, `R['$;']`, etc that are fully functional. They are likely to get less awkard aliases.

#### Naming/Aliases/CamelCase

Ruby method names that are not valid javascript names are added as strings and conssitently aliased with valid names. For those with strong feelings against snake_case method names there will be camelCased aliases.

- Operator methods as strings with an alias
- Remove `?` from method names. `include?` => `include`
- Replace `!` with `_bang`

{% highlight javascript %}
str = R('foo')

str['<=>']('bar') // => -1
str.cmp(bar)      // cmp alias to <=>

str['==']('bar')  // => false
str.equals('bar') // equals alias to ==

// Mapping:
'=='  : equals
'===' : equal_case
'<=>' : cmp
'%'   : modulo
'+'   : plus
'-'   : minus
'*'   : multiply
'**'  : exp
'/'   : divide
'<<'  : append

{% endhighlight %}

Special variables and methods in the R namespace like `$~` will reside in `R['$~']`. Typecasting methods Kernel#String, Kernel#Float unfortunately collide with the class names and for the time being are prefixed with a `$`: `R.$Float("1.23")`, `R.$String([])`.

#### Constructors

The RubyJS objects work much like the primitive wrapper objects in JavaScript. They coerce back to their primitive values using `valueOf`, `toString`.

{% highlight javascript %}

    R('foo')

    // To get the same functionality as Rubys String.new, use:
    R.String.new('foo') // => 'foo'

    // Call the constructor directly for speed
    new R.String('foo') // => 'foo'

    // If not passed a string primitive, valueOf() will be called
    new R.String(1)     // => '1'
    new R.String( )     // => ''
    new R.String(null)  // => ''

{% endhighlight %}



#### to_native()

To access the underlying native JS primitive or object use `to_native()` and `to_native(true)` also converts an objects member (e.g. array elements). If there is no native JS object it will return self/this.

{% highlight javascript %}

R('foo').size().to_native()                 // => 3  JS primitive

{% endhighlight %}


#### Equality operator

The ruby equality operators `==`, `===`, `<=>` are implemented to follow rubyspec. When comparing a rubyjs object with native js types keep the following in mind.

{% highlight javascript %}

    str = R('foo')

    // RubyJS.String objects coerce to primitives
    str   == 'foo'                            //=> true
    'foo' == str                              //=> true

    // RubyJS.Objects behave like the native wrapper objects
    'foo' === str                             //=> false
    'foo' === new String('foo')               //=> false

    // Also R() creates different objects for the same String
    R('foo') === R('foo')                     //=> false
    new String('foo') === new String('foo')   //=> false

{% endhighlight %}

## Methods

#### Chainable

Methods are chainable by design as they return RubyJS objects. So no further typecasting with R() is needed and we can write powerful one-liners.

{% highlight javascript %}

str = R('foo')                       // => 'foo'    <RubyJS.String>
str.size()                           // => 3        <RubyJS.Fixnum>
str.size().upto(5)                   // => <Enum>   <RubyJS.Enumerator>
str.size().upto(5).to_a()            // => [3,4,5]  <RubyJS.Array>
str.size().upto(5).to_a().join(",")  // => "3,4,5"  <RubyJS.String>

{% endhighlight %}

Note that block arguments in Enumerable methods typically are never converted, both for performance reasons and to be able to support the various useages of block arguments.

{% highlight javascript %}

R(['foo', 'bar']).each(function(w) {
  // => 'foo', 'bar' both remain primitives

R([1,2,3,4]).each_slice(2, function(arr) {
  // => [1,2] and [3,4] arr both native arrays

{% endhighlight %}


#### Arguments

All RubyJS methods typecast method arguments. So it doesnt matter whether you pass a RubyJS object, a primitive or a native JS object. Typecasting follows closely the Ruby specification and supports duck-typing.

{% highlight javascript %}

str = R("foo")
str.rjust(   5  )         // => "  foo"  (Recommended)

str.rjust( R(5) )         // => "  foo"
str.rjust(new Number(5))  // => "  foo"

// Duck-typing, object that implements to_int
str.rjust( {
  to_int: function() { return R(5); }
})

{% endhighlight %}

This process is fast. Methods internally use JS primitives and arguments will only be typecasted to a RubyJS object when needed.


#### Mutator methods

Destructive methods change the object. Non-destructive methods do not change the object itself but return a new one. They are called mutator methods as they mutate the object. In Javascript there are only a handful of mutator methods for arrays.

{% highlight javascript %}

arr = [1,2,3]       // a normal javascript array
arr.concat(4)       // => [1,2,3,4]
arr                 // => [1,2,3] unchanged -> non-destructive method
arr.push(4)         // => 4
arr                 // => [1,2,3,4] changed -> destructive method

{% endhighlight %}

In Ruby destructive methods have a `!` suffix, `capitalize` vs `capitalize!`. As this is not a valid method name it is replaced with `capitalize_bang` until a better solution is found.

{% highlight javascript %}

str = R('foo')
str.capitalize()       // => 'Foo'
str                    // => 'foo'
str.capitalize_bang()
str                    // => 'Foo'

{% endhighlight %}




## Enumerables

Complete support for Ruby Enumerables and Enumerators.

{% highlight javascript %}

arr = R.w('foo bar baz')  // => ['foo', 'bar', 'baz']

arr.map(function (w) { return w.size() }   // => [3,3,3]

arr.each_with_object({}, function(w, obj) {
  return obj[w] = w.reverse();
})
// => { foo: "oof", bar: "rab", baz: "zab" }

{% endhighlight %}


#### Block arguments

When passing arguments to a block RubyJS adapts to the arity of the block (Function.length) and works exactly like ruby.

{% highlight javascript %}

arr = R([ [1,2,3] ])
arr.each(function (a    ) { })          // a: [1,2,3]
arr.each(function (a,b,c) { })          // a: 1, b: 2, c: 3
arr.each(function (     ) { })          // arguments: [1,2,3]

arr = R(['foo'])
arr.each_with_index(function (a, i) {}) // a: 'foo', i: 0
arr.each_with_index(function (a   ) {}) // a: 'foo'

{% endhighlight %}

Splatting arguments with CoffeeScript works too:

{% highlight coffeescript %}
coffee.each_with_index (a...) -> # a: ['foo', 0]
{% endhighlight %}

{% highlight javascript %}
js_arr.each_with_index(function () {
  var args = Array.prototype.slice.call([])
}) // args: ['foo', 0]
{% endhighlight %}

#### Mixins

Enumerable is implemented as a module that can be included in others. So you can create your own Collection, e.g. a Set, OrderedArray, etc. Implement `#each`.





#### Breaking loops

{% highlight javascript %}

arr = R([1,2,3])

result = R.catch_break( function (breaker) {
  arr.each function (el) {
    breaker.break("yay") if el == 2
  }
})

result // => 'yay'

{% endhighlight %}



By [hasclass](http://hasclass.com)

