---
layout: post
title: "All the Numbers"
author: "Ian Zapolsky"
comments: true
category: posts
---

Finding a great solution to a computer science problem is more about the journey
than the final destination. The process of working through this challenge is a good
example of how our initial instincs about how do something can be (read: usually
are) wrong, and require deeper thought and fine-tuning.

Here's a challenge: find the sum of all the four-digit numbers made up from 1, 2, 3,
and 4, non-repeating. Initially, this problem seems to have two steps in my mind:

1. find/generate all the numbers
2. sum them.

Below is C code that does just this. I generate all the numbers with nested for
loops, then run them each through a quick comparison method that checks for repeats.

{% highlight c %}
int find_sum_slow() {
	int* arr = mallow(24*(sizeof(int)));
	char* cur = mallow(4*(sizeof(char)));
	char a,b,c,d;
	int sum, cursor;
	sum = cursor = 0;

	for (a = '1'; a < '5'; a++) {
		cur[0] = a;
		for (b = '1'; b < '5'; b++) {
			cur[1] = b;
			for (c = '1'; c < '5'; c++) {
				cur[2] = c;
				for (d = '1'; d < '5'; d++) {
					cur[3] = d;
					if (check_repeat(&cur) == 0) {
						arr[cursor++] = atoi(cur);
						printf("%d\n", atoi(cur));
					}
				}
			}
		}
	}
	for (cursor = 0; cursor < 24; cursor++)
		sum += arr[cursor];
	return sum;
}

int check_repeat(char** cur) {
	if ((*cur)[0] == (*cur)[1] || (*cur)[0] == (*cur)[2] ||
		(*cur)[0] == (*cur)[3] || (*cur)[1] == (*cur)[2] ||
		(*cur)[1] == (*cur)[3] || (*cur)[2] == (*cur)[3])
		return 1;
	else
		return 0;
}
{% endhighlight %}

There are a couple things to note. First, we can tell that there will be 4! = 24 four-digit numbers because a non-repeating four-digit number made up of only four distinct integers can be comprised of 4\*3\*2\*1 combinations of digits. Second, we can take advantage of the fact that ASCII characters are
ordered sequentially to get get chars 1-4 by incrementing the integer representation of that char.

However, after a bit more thought, and a helpful conversation with Ruchir, I realized that this
is actually a pretty horrible way of finding this sum. Let's think about the challenge in a 
different way. We know there will be 4! of these four-digit non-repeating numbers. Now let's
take that knowledge a step further: if there are 4! total numbers, it follows that for each
given digit there will by 24/4 = 6 numbers that contain that digit in a given place. In other
words, if we fix one digit of the four-digit number, there will be 3! = 6 different numbers
that can be made with the other digits arranged in various ways around the single fixed number.

For example, there will be 6 numbers in the set of 24 that have the digit 1 in the 1s place, 
6 more that have 1 in the 10s, 6 more with 1 in the 100s, and the final 6 will have a 1 in 
the 1000s place. This idea expressed in math terms for our 4-digit example looks like this:

{% highlight latex %}
sum = (1+2+3+4) * (10^0 + 10^1 + 10^2 +10^3) * (3!)
{% endhighlight %}

Using this observation, we can skip the memory and time intensive process of generating these
four-digit numbers and simply find the sum with the following simple algorithm, which uses
the idea from above to calculate the sum of all the n-digit numbers made up of integers
1-n non-repeating.

{% highlight c %}
int find_sum_fast(int digits) {
	int a,b,sum;
	sum = 0;
	for (a = digits; a > 0; a--) {
		for (b = digits-1; b >= 0; b--)
			sum += (a*pow(10, b));
	}
	for (b = digits-1; b > 0; b--)
		sum *= b;

	return sum;
}
{% endhighlight %}

This post is a great example of why I wanted to start this blog in the first place: to explore
answers to problems like this and find them on my own, instead of just reading them off
sites like careercup.
