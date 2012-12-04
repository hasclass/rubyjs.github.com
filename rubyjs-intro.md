---
layout: posts
title: RubyJS - The post-launch launch post
---

Last week I launched RubyJS at [jscamp.asia](jscamp.asia).

### RubyJS is a port of the Ruby core-lib to JavaScript

RubyJS includes classes like String, Array, Numbers and almost all of the methods are implemented and are compliant (not just inspired) to Ruby.

This means that a Rubyist can immediately be more productive in JavaScript. Code can be easily ported from Ruby to JS.

### It is not about writing Ruby in JavaScript

It's all about the methods and having a proper standard library. There is no intent to mimic the object/class model or metaprogramming features of Ruby. Also it is not about the syntax.

You are still writing JavaScript but now have a set of convenience methods at your disposal. RubyJS does not save you from learning JavaScript, but it saves you from using and learning various 3rd party libraries.

For completeness sake everything is ported 1 to 1, this does however not mean you have to use everything, e.g. use native numbers for  arithmetic (+,-,/,*) operations rather than the RubyJS methods.

### The JS way. Similar to what you're already using

RubyJS is not that much different from what you're already doing.

{% highlight javascript %}
// underscorejs
_.chain([]).sortBy(fn).map(fn).value()
// RubyJS:
R([]).sortBy(fn).map(fn).toNative()
{% endhighlight %}

RubyJS objects are lightweight wrappers around native objects.

{% highlight javascript %}

  RubyJS.Fixnum = function(__native__) {
    this.__native__ = __native__;
  }

  RubyJS.Fixnum.prototype.odd = function () {
    return this.__native__ % 2 == 0;
  }

  RubyJS.Fixnum.prototype.succ = function () {
    return new RubyJS.Fixnum(this.__native__ + 1);
  }

{% endhighlight %}

In most cases you don't even have to call toNative(). JavaScript coerces correctly out of the box:

{% highlight javascript %}

var str = "#"+ R('foo').rjust(5);
// => '#  foo'

R(1.2345).round(2) + 1
// => 2.23

{% endhighlight %}



### What is the advantage over using x,y,z?

There are many libraries out there that implement missing functionality of JavaScript. Underscorejs, stringjs, underscore.string, momentjs. Each with an own API, documentation.

{% highlight javascript %}
arr = ['foo', 'bar']
str = _.map(arr, function (w) { return S(w).capitalize().s })
       .join(',');
S(str).pad(50).s
{% endhighlight %}

- RubyJS is chainable across types which leads to clean code
- One API, one way of doing things
- The Ruby core-lib is battle-hardened, small, concise yet powerful

{% highlight javascript %}
R(arr, true).map(function (w) { return w.capitalize() })
  .join(',')
  .center(50)
  .toNative();
// In most cases calling toNative() is actually not necessary.
{% endhighlight %}



### Lightweight, fast, works across browsers (and nodejs)

Not just the file-size (20kb minzipped), but also the implementation using wrapper objects. Features that have little advantage but produce a lot of extra code or felt unnatural in JavaScript were skipped. There are numerous performance optimizations to make it fast, and all major browsers are supported.


### What about that price tag

It started out as a silly experiment and eventually became an obsession. There is a lot of grunt work involved, porting specs, code, docs. Yet still I'm passionate about working on RubyJS. Charging money for RubyJS would allow me to continue working on RubyJS full-time, and give me the incentive to also do the boring parts. Not many people can pull off that stunt. At least I want to give it a try.

190$ for alpha software without support? It'll be beta soon, and you're getting upgrades for 1 year after the beta launch. Support is given on a best-effort base.

How did I came up with 190$ anyway? On a recent small JS project I've spent at least half a day just for adding missing functionality to JS. I would have gotten my money back within the first week. How valuable is your time?

