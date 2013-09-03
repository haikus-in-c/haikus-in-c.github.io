---
layout: post
title: "When an Argentinian Drinking Game Meets Computer Science"
author: "Ian Zapolsky"
comments: true
category: posts
---

I love drinking games. 

They're social, they're entertaining, but most importantly they have the unique 
quality of making losing fun. Perhaps the most famous drinking game in the 
world is beer pong, a game of which I am a strong proponent. However, beer pong 
offers little in the way of statistics or information a computer scientist could 
analyze to gain the upper hand: it all comes down to whether or not you can make 
the shot.

There are other, more cerebral types of drinking games of course, that *do* offer
opportunties for <strike>cheating</strike> observation and analysis. Today I 
would like the share one with you that I learned this past weekend in
Buenos Aires. As far as I can tell it does not have an official name, and there
very well may be a North American/English speaking version already being played.
It's great fun but, as we'll see in this post, it is completely unfair. 

Here's the premise: x people are in a circle. The person whose turn it is to start
the round (usually the person who lost the last round) begins by saying out loud 
"1". The next person in the circle (assume that we always start going in 
clockwise order, though durnkards will almost certainly switch the intial 
starting direction during the game multiple times) says "2", and so on up
until infinity.

The catch is that on a certain set of numbers, the person whose turn it is to say
that number claps, without saying the number, and then the direction of the turns 
reverses (e.g. counterclockwise) and the next number is called out (or clapped 
on, in which case the order reverses again). Here are the criteria for a clapping
number:

1.	Multiple of 7 (e.g. 14) 
2.	Ends with 7 (e.g. 17)
3. 	Individual digits sum to 7 (e.g. 16)
4. 	Palindrome, excluding single digit numbers (e.g. 11)

Every time the circle reaches a number that necesitates a clap, the person whose
turn happens to land on that number is in the hotseat: if they forget to clap, or
say the number, or break the rhythm of the count, they have to drink, and everyone
starts from 1 again.

To wrap your head around the game here's a list of the numbers under 50 that need
to be clapped on. Note that the real difficulty in this game comes when consecutive
numbers necesitate claps, because as these numbers are not called out it is easy to
lose your spot in the count, especially when you're trying to keep track of all
the direction changes that are constantly happening.

{% highlight latex %}
[7, 11, 14, 16, 17, 21, 22, 25, 27, 28, 33, 34, 35, 37, 42, 43, 44, 47, 49] 
{% endhighlight %}

Here's the interesting part: this game is totally unfair for certain numbers of
players. I wrote a [simple python representation of this game][code] that allows
you to simulate a round for any number of players, up to any number you want, 
and then find out how many claps (assuming no mistakes) each player
would have had to make. Here are the results for just 2 players, simming up to 100. 

	player 1: 22
	player 2: 14	

Takeaway #1: it's almost always better not to start a round. Increasing the 
breakpoint of the game to 1000 makes the difference becomes clearer:
	
	player 1: 206
	player 2: 126

Obviously we should expect this kind of behavior because a disporpotionate number
of claps come on odd numbers. Now let's switch over to 4 players, a much more
common real world case, simming up to 1000 again (which of course is uncommon: in
the games I've played the group made it past 100 once).

	player 1: 103
	player 2: 64
	player 3: 103
	player 4: 62

Takeaway #2: you don't want to get stuck on the odd numbers in this game. It puts
you on the hotseat to clap about 1.6x the number of times you get with the even
numbers. For all games with an even number of players up to 8 we find
this 1.6x number distinguishing the even number players from the odd.

Now let's look at an odd number of players. First 3, then 5, then 7:

	player 1: 130 
	player 2: 99
	player 3: 103

	player 1: 54
	player 2: 128
	player 3: 51
	player 4: 53
	player 5: 46

	player 1: 31
	player 2: 33
	player 3: 32
	player 4: 31
	player 5: 31
	player 6: 32
	player 7: 142

Takeaway #3: don't be number 7. You'll catch numbers that end with 7 *and* numbers
that are multiples of 7 much more often than everyone else.

Takeaway #4: when playing with an odd number of people up to 9, the distribution 
of claps is fairly even, except for one person who gets the shaft. This unlucky
person will be sitting in the (number\_of\_players)%7 spot. 3%7 = 1 (player 1
gets the shaft), 5%7 = 2 (player 2 gets the shaft), 7%7 = 0, but since there is
no player 0, player 7 gets the shaft, and 9%7 = 7, and you can test for yourself
that player 7 does indeed get the shaft when playing with 9 people.

So what does this all mean? Basically nothing, apart from the fact that you now
know more about this game than you ever wanted to. You can use this knowledge to
your advantage because often the person who lost the previous turn does not 
automatically start the next round because they are distracted by all the drinking
they're doing. This is your opportunity to turn to your right hand neighbor kindly
suggest that he or she start off the next round (if you're playing with an even
number of people), or arrange your odd number of friends so that you never get the
shaft.

Then sit back and relax, knowing that your superior knowledge of the game is
bringing you victory, which, in drinking games, actually means you're losing.

[code]:https://github.com/haikus-in-c/haikus-in-c/blob/master/2013.9/drinking_game/game.py
