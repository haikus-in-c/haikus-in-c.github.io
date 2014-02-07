---
layout: post
title: "Is Memoization Caching?"
author: "Ian Zapolsky"
comments: true
category: posts
---

The concept of caching is simple: store something that is relevant to you in a 
place that is close to you so that you can access it subsequent times in way
less time than having to go back to the original place you got it from again. 

The world without caching would suck.

Imagine that you're in the library and you're writing an essay on someone. 
Suddenly, you need to look something up about this person, so you go into the
stacks, find his biography on the shelf, read the information you need, put the 
biography back on the shelf, and come back to your desk. Without the book. 
This is the world without caching and it sucks because the next time you need to 
look up something about the subject of your essay, you need to walk back to the 
stacks and grab the book off the shelf again. Why not just bring the book to 
your desk! Put it in your cache!

<!--more-->

So then, what is memoization? Memoization is a *kind* of caching to help prevent
functions from performing redundant work. In memoization, we cache previously
calculated results of a function for later use by that same function. The
clearest example we can present to decribe memoization is calculating fibonacci 
numbers. First, we look at a fibonacci function sans memoization:

{% highlight javascript %}
var fibonacci_slow = function () {

  // keep track of how many times we call fib
  var times = 0;

  var fib = function (n) {
    times = times+1;
    if (n === 0 || n === 1) {
      return n;
    } else {
      return fib(n-1) + fib(n-2);
    }
  };

  var get_times = function () {
    return times;
  };

  return { fib : fib,
           get_times : get_times };

}();
{% endhighlight %}

For every call to fib here, we have to recursively calculate the nth fibonacci
number. This results in fib being called *a lot* of times, 21891 times to be
precise to calculate the 20th fibonacci number.

However, imagine if we stored the nth fibonacci number in an array (cache), which we could
then reference directly (like the book on our desk, instead of the library shelf).
See the code below:

{% highlight javascript %}
var fibonacci_fast = function () {

  // keep track of how many times we call fib
  var times = 0;

  // memoization cache to keep track of calculated values
  var memoization_cache = [0, 1];

  var fib = function (n) {
    times = times+1;
    var result = memoization_cache[n];
    if (typeof(result) !== 'number') {
      result = fib(n-1) + fib(n-2);
      memoization_cache[n] = result;
    }
    return result;
  };

  var get_times = function () {
    return times;
  };

  return { fib : fib,
           get_times : get_times };

}();
{% endhighlight %}

This version of fib is called only 39 times to calculate the 20th fibonacci
number, a huge improvement from 21891. It's better because once it calculates
a fibonacci number recursively, it saves that number so it can access it the
next time it needs it in constant time, instead of generating it recursively
all over again.

So, memoization *is* caching, but caching is not always memoization.


