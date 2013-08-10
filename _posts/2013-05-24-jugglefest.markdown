---
layout: post
title: "Can You Yodle and Juggle at the Same Time?"
author: "Ian Zapolsky"
comments: true
categroy: posts
---

In the careers section of startup [Yodle's][yodle] website, there are a couple programming
problems, one of which is called [JuggleFest][jugglefest]. Here's a summary: given a 
list of x circuits and y jugglers, where each juggler has a certain number of circuit
preferences ranked 1 - n, assign the jugglers to circuits evenly such that the number of
jugglers in each circuit is y/x and no juggler could switch to a circuit that they prefer 
more than the one they are already assigned to _and_ be a better fit for that circuit than 
one of the other jugglers assigned to it. Whew.

We determine how good a match a juggler is for a circuit by taking the dot product of three
variables which they all share: Hand-eye Coordination (H), Endurance (E), and Pizzazz (P).
The higher the dot product of a juggler with a specific circuit, the better fit a juggler
is for that circuit.

This problem is a great exercise in designing an airtight algorithm from small scale to 
large scale. The sample input provided by Yodle gives us 3 circuits and 12 jugglers,
while the actual text file required to solve the challenge has 2,000 circuits and 12,000
jugglers. Below is my process broken into steps. There is no code here due to its
length, but check out the whole solution on the [github][haikus-github]. Also note that I
refer to Circuits and Jugglers with their first letters capitalized to signify that these
are classes.

1.	Read all Circuits and Jugglers into the program from an input file. This process can be
	handled many different ways, but one important requirement is that we figure out the
	total number of Jugglers and Circuits so we can determine the target number of Jugglers
	that each Circuit should hold.

2. 	Assign all Jugglers to their first preference Circuits. I designed my Circuits so that they
	contain two ArrayLists of Jugglers, one called jugglers, the other called overflow. Anytime
	the jugglers ArrayList becomes full, any subsequent Juggler added to that Circuit is checked
	against those already in jugglers. If it is better than none of them it is placed in overflow.
	If the new Juggler _is_ a better match for the Circuit than another Juggler, then the worst
	match in jugglers is bumped down to overflow and the new Juggler is inserted in its sorted
	position in jugglers.

3.	Examine Circuits one-by-one and distribute the members of their overflow array (if there are
	any) to the other Circuits. Repeat the following steps for each Circuit:
  	
	1. 	Find the worst matched Juggler in the overflow array.  

  	2. 	Check this Juggler against its next preference Circuit.

    	1. 	If the Circuit is under capacity, add the Juggler to this Circuit immediately.

    	2. 	If the Circuit is at or over capacity, but the Juggler is a better match than one of the 
	   		Jugglers currently in the Circuit's jugglers ArrayList, insert it and kick the worst
	   		performing Juggler down to overflow.

    	3. 	If the Circuit is at or over capacity and the Juggler is not a better match than one
	   		of the Jugglers currently in jugglers, check against the next preference Circuit.

		4. 	If this Juggler is not better than one of the Jugglers in its last preference Circuit,
	   		check all Circuits for an under-full Circuit and add it there.

4. 	Sweep across all the Circuits, repeating steps 3-4 until they're all filled perfectly.

[yodle]:http://www.yodle.com/
[jugglefest]:http://www.yodlecareers.com/puzzles/jugglefest.html
[haikus-github]:https://github.com/haikus-in-c/haikus-in-c/tree/master/2013.5/juggle_fest
