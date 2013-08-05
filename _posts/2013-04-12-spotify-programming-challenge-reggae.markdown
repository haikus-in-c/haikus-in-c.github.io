---
layout: post
title: "Spotify Programming Challenge 1: Reggae"
category: posts
---

Here's what we need to do: write a program that takes some integer in decimal form
between 1 and 1,000,000,000 (inclusive), converts it to binary form, reverses that binary
number, and then converts the result back into decimal form.

For example, on input 4, we convert to binary number 100, reverse it to 001, then convert 
that back to decimal form, which in this case would be 1. So 4 > 100 > 001 > 1.

This problem is pretty simple, we really only need to do three things: convert an integer
from decimal to binary and vice versa, and reverse a string. It should be noted that there
are standard library functions to do all of these things. The Integer class has a builtin
toBinaryString() method and the String class has a built in reverse() method. But of course,
we're not going to use those.

So, to tackle the first of our problems let's look at converting an integer to a binary string.
Let's return to 4, since it's easy to work with. As we saw above, 4 is 100 (1\*2^2 + 0\*2^1 + 0\*2^0) 
in binary. This literally means that 4 has: one 4, zero 2s, and zero 1s. To see the pattern a
a little clearer, let's go bigger: 100 = 1100100. 100 has: one 64, one 32, zero 16s, zero 8s, one 4,
zero 2s, and zero 1s. OK, I think we've got the pattern now. What we need to do is figure out how
many times each power of two is in the number. The key is modulus division.

If we divide some integer x mod 2, we get either a 1 or a 0, telling us that 2 perfectly fit into
this number, or that it didn't. This 1 or 0 also represents the 1s place of the binary string
representation of that integer.  If we take (x/2) mod 2, we again get either a 1 or a 0, but
because we divided by 2, _this_ 1 or 0 represents the 2s place of the binary string. We can
continue this method until the next division by 2 results in a 0, then we have the whole
binary string. 

{% highlight latex %}
	4%2 = 0 --> append to binary string: 0
	4/2 = 2
	2%2 = 0 --> append to binary string: 00
	2/2 = 1
	1%2 = 1 --> append to binary string: 001
{% endhighlight %}

Notice that this method gives us the number in a reversed form _already_, assuming we
just appended each number onto a String as we got it. Below is code that executes this
idea.

{% highlight java %}
String convertDecimalToReversedBinary(int value) {
	String result = "";
	while (value > 0) {
		result += ""+(value%2);
		value = value/2;
	}
	return result;
}
{% endhighlight %}

Now, all we need is a method to convert a binary string back into an integer. This isn't
hard, as we already know how binary strings are put together. My method works backwards
from the end of the string, adding increasing powers of 2 to a final result value that's
returned at the end.

{% highlight java %}
int convertBinaryToDecimal(String binary) {
	int value = 0;
	int count = 0;
	for (int i = (binary.length()-1); i >= 0; i--) {
		if (binary.charAt(i) == '1')
			value += Math.pow(2, count);
		count++;
	}
	return value;
}
{% endhighlight %}

And that's it! We solved the first spotify programming challenge.
	

