---
layout: layout
title: "Spotify Programming Challenge 1: Reggae"
category: posts
---

Here's what we need to do: write a program that takes some integer in decimal form
between 1 and 1000000000 (inclusive), converts it to binary form, reverses that binary
number, and then converts the result back into decimal form.

For example, on input 4, we convert to binary number 100, reverse it to 001, then convert 
that back to decimal form, which in this case would be 1. So 4 > 100 > 001 > 1.

This problem is pretty simple, we really only need to do three things: convert an integer
from decimal to binary and vice versa, and reverse a string. It should be noted that there
are standard library functions to do all of these things. The Integer class has a builtin
toBinaryString() method and the String class has a built in reverse() method. But of course,
we're not going to use those.

So, to tackle the first of our problems, let's look at converting an integer to a binary string.
A good first attempt might look like the following:
 
