---
layout: post
title: "John Conway's Game of Life with jQuery"
author: "Ian Zapolsky"
comments: true
category: posts
---

In casting about for some simple projects to learn more about Javascript and
jQuery recently I came across [John Conway's Game of Life][gameoflife]. I had 
heard about the game before, in an introductory CS class I took in freshman
year of college, and since it's a relatively simple and grid-based game I 
decided to recreate it using the two aforementioned technologies for quick 
hour-long exercise.

The game isn't actually a game, it's more of a cellular life simulation. Each
cell is represented by a square on a grid. Cells can be either dead (white), or
alive (black). The game progresses forward in time units (call them years),
where each unit of time updates the state of the current population of dead
and alive cells in the following four ways:

  1.  Any live cells with < 2 live neighbors die (neighbor is understood here 
to mean any cell immediately adjacent to the given cell, including 
diagonally).
  2.  Any live cells with 2 or 3 live neighbors live.
  3.  Any live cells with > 3 live neighbors die.
  4.  Any dead cells with exactly 3 live neighbors become live cells. 

So our game has three main elements: The grid, the individual cells within the
grid, and time.

Let's start with the grid and the cells within. I chose to represent these as
HTML div elements, with descriptive IDs so that I could keep track later of
which divs on the page were adjacent. Below is code that fills a container with
my grid of 40x40 cells.

{% highlight javascript %}
var initBoxes = function( $container ) {
  var html = '';
  var i;
  for (i = 0; i < 40; i++) {
    for (j = 0; j < 40; j++) {
      var id = 'x'+i+'y'+j;
      html += '<div id="'+id+'" class="box"></div>';
    }
  }
  $container.html( html );
};
{% endhighlight %}
  




[gameoflife]:http://en.wikipedia.org/wiki/Conway's_Game_of_Life
[code]:https://github.com/ianzapolsky/game-of-life-js
