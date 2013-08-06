---
layout: post
title: "Spotify Programming Challenge 1: Reggae"
author: "Ian Zapolsky"
category: posts
---

Here's what we need to do: write a program to take some integer in decimal form
between 1 and 1,000,000,000 (inclusive), convert it to binary form, reverse that binary
string, and then convert the result back into decimal form. For example, on input 11, 
we convert to binary number 1011, reverse to get 1101, then convert back to decimal form
to get 13 (11 > 1011 > 1101 > 13).

Let's continue with 11. 11 is 1011, or (1\*2^3 + 0\*2^2 + 1\*2^1 + 1\*2^0), in binary. 
Why is this?

If we divide any number x mod 2, we get either a 1 or a 0, telling us the remainder of
x/2 and consequently if the 1s place of x in binary is 1 or 0. 11 mod 2 is 1, and thus 
we know that when divided by 2s, 11 needs an extra 1 (10/2 = 5 R1). 

Now, let's divide 11/2, rounding down to the nearest integer. This division allows us to
raise our divisor by power of 2, or shift left one place in binary. Now, when we divide mod
2 we'll told the remainder of x/4, in terms of 2s, or how many 2s are needed to make up the 
remainder of a division by 4. If we didn't divide by 2 and simply doubled the divisor, we would get our remainder in 1s, but we need it in 2s because we already figured out the 1s. 

(11/2 = 5) mod 2 also equals 1, so we know that when divided by _4s_, 11 needs 
at least an extra 2. We put a 1 in the 2s place (notice that simply 11 mod 4 = 3, a
very unhelpful number in binary).

Dividing by 2 and then by modulus 2 again, we find that (5/2 = 2) mod 2 equals 0, which
means that when divided by _8s_, 11 _does not need_ at least another 4 (which would make 12). 
We put a 0 in the 4s place.

Finally, (2/2 = 1) mod 2 is 1, meaning that when divided by 16s, 11 does need
at least another 8 (this is of course because 16 does not go into 11 any times). 
We stop there because the next division brings us to 0. 

{% highlight latex %}
	11%2 = 1 --> append to binary string: 1
	11/2 = 5
	5%2 = 1  --> append to binary string: 11
	5/2 = 2
	2%2 = 0  --> append to binary string: 011
	2/2 = 1
	1%2 = 1  --> append to binary string: 1011
	1/2 = 0  --> stop
{% endhighlight %}

Notice that the example above appends new 1s and 0s _to the left_. This means that if we were 
to append to the right in a method, as I do below, it would yield an already-reversed binary string, 
thus killing two birds with one piece of code!

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
from the end of the String, adding increasing powers of 2 to a final result.

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
	

