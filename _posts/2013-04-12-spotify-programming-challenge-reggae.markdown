---
layout: post
title: "So This is What Reggae Feels Like"
author: "Ian Zapolsky"
comments: true
category: posts
---

On [Spotify's puzzle page][spotify], they've got three tech puzzles for potential hires
to play around with. This is the first of them, whose difficulty is rated *Reggae*.

Here's what we need to do: write a program to take some integer in decimal form
between 1 and 1,000,000,000 (inclusive), convert it to binary form, reverse that binary
string, and then convert the result back into decimal form. For example, on input 11, 
we convert to binary number 1011, reverse to get 1101, then convert back to decimal form
to get 13 (11 > 1011 > 1101 > 13).

<!--more-->

Let's continue with 11. 11 is 1011, or (1\*2^3 + 0\*2^2 + 1\*2^1 + 1\*2^0), in binary. 
Why is this?

If we divide 11 mod 2, we'll get a 1, telling us the remainder of
11/2 and consequently whether the 1s place of 11 in binary form is 1 or 0.

Now, let's divide 11/2, rounding down to the nearest integer, which makes 5. 
This division allows us to raise our modulous divisor by a power of 2, or shift left 
one place in binary. When we compute 5 mod 2 what we're really seeing is the 
remainder of 11/4 expressed in terms of 2. Notice that if we didn't divide by 
2 here and simply doubled the modulous divisor, we would get our remainder in 1s, which
in this case would be (11%4 = 3), a very unhelpful number in binary. 

The central idea is to find the remainder of the number you're converting (in this case 11)/2^(n+1) 
in terms of 2^n, which will always either be a 1 or a 0, in order to find the value of the nth 
binary slot from the right.

{% highlight bash %}
11%2 = 1 --> append to binary string: 1
11/2 = 5
5%2 = 1  --> append to binary string: 11
5/2 = 2
2%2 = 0  --> append to binary string: 011
2/2 = 1
1%2 = 1  --> append to binary string: 1011
1/2 = 0  --> stop because we reach 0
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

And that's it! We solved the first Spotify programming challenge.
	
[spotify]:https://www.spotify.com/us/jobs/tech/
