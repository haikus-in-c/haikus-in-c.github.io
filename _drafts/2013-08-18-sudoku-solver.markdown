---
layout: post
title: "Why I Don't Like Sudoku Anymore"
author: "Ian Zapolsky"
comments: true
category: posts
---

Earlier this school year Ruchir and I kicked around the idea of trying to code a program that
could solve sudoku puzzles. We talked about how we might do it, but in the midst of school and
homework it fell off the map. However, recently I became addicted to a sudoku app on my phone 
and it inspired me to take a crack at the project. 

I started to think about how a computer might play sudoku, and came up with two basic rules for 
figuring out the values of individual sudoku cells, both of which require sweeping over the entire 
puzzle first to determine all of the possible values for each open cell on the board (which 
is a task better suited for a computer than a human, trust me).

1. Obvious Elimination: if a box has only one possible value, then it can only be set to that value.
2. Unique Possibility: if a box has multiple possible values, but one of its possible values is not 
possible for any other open cell in that box's row, column, or square, then it must be set to that value.

I was sure from my pen and paper/phone testing that these two rules alone would be enough to solve at 
least some sudokus, so I started thinking about how to design a sudoku board data structure. The 
sudoku board itself initially screams out for a 2D array of some sort, because ultimately all sudoku 
boards are just 9x9 squares made up of 81 individual cells (9 rows, 9 columns, and 9 3x3 boxes of 
cells):

![sudoku](/images/sudoku.png)

However, we can't just use a 2D array of integers, because each cell needs to hold more
information than just its own value; it also needs to know all of the possible values it could be set
to based on its position, and, depending on our implementation, shoud also potentially know which
row, column, and box it belongs to so that when we later write methods to populate its list of
possible values we can instantly know its location on the board. 

A simple [SudokuBox][sudokubox] object should do the trick, with logic to determine its box number
based on its row and column index, and methods to abstract low level interaction with its list of
possible values (things like adding and removing values and checking the list for a specific
value). Now we can represent the board as a 9x9 2D array of SudokuBox objects. 

*The* central rule of all sudoku puzzles is that each row, column, and box on the 
greater 9x9 board must contain the numbers 1-9, non-repeating. What this means from a structure
design standpoint is that we need to be able to quickly iterate through individaul rows, columns,
and boxes in order to determine what values are still possible for cells in a given position. This 
was the first tricky piece of logic that presented itself in this project. Rows and columns
are easy enough flip through, because one index of the 2D array stays the same, while the other
increments through:

{% highlight java %}
...
SudokuBox[][] board = new SudokuBox[9][9]; 

// picking random row and column
int row = 3;
int column = 6;

// iterate over row
for (int i = 0; i < 9; i++)
	board[row][i].doSomething();

// iterate over column
for (int i = 0; i < 9; i++)
	board[i][column].doSomething();
{% endhighlight %}

However, iterating through a single box's cells proves to be a little more complicated because the
cells of a box live on three separate rows and three separate columns. The key to figuring this out
is to subdivide the boxes into columns and rows of boxes. Take another look at the picture of the
board above. If we start in the top-left corner and work left to right, going to the next row down
once we reach the end of a line, we get boxes 0-8. Boxes 0, 1, and 2 all begin with a row index of 0,
boxes 3, 4, and 5 begin at row 3, and boxes 6, 7, and 8 all begin at row 6. This pattern is pretty
clear: take a box number, divide by 3 (rounding down to the nearest whole number, as integer division
does in most programming languages), and then multiply by 3.

{% highlight java %}
...
int box = 4;
int start_row = (4/3)*3;

// iterate over box
for (int row = start_row; row < (start_row+3); row++) {
	// column logic ....
{% endhighlight %}

This is perfect: our row index is going to loop through the rows we need for box 4, which are 3, 4,
and 5. However, we have to figure out our column index too. This time we subdivide along the columns:
boxes 0, 3, and 6 all begin with a column index of 0, boxes 1, 4, and 7 begin with a column index of 3,
and boxes 2, 5, and 8 begin with a column index of 6. Regular division obviously won't work here, but
modulus divison will because the numbers from these groups each 

{% highlight java %}
...
int box = 4;
int start_row = (4/3)*3; 		// = 3, the start row for box 4
int start_column = (4%3)*3;		// = 3, the start column for box 4

// iterate over box
for (int row = start_row; row < (start_row+3); row++) {
	for (int column = start_column; column < (start_column+3); column++)
		board[row][column].doSomething();
} 
{% endhighlight %}

Now we've got a data structure that represents an entire sudoku board, and we have logic in place that
will allow us to quickly pinpoint the location of a cell, and then traverse all its related cells to 
figure out what values should be possible for that cell. Now all I needed to do was to write my
solving strategies into code. Below is the outline of my process:

1. While our puzzle is not complete:
	1. Sweep through the entire board, using the cells who's values we already know to determine the 
	possible values for each empty cell.
	2. Sweep through the board again, checking every cell's possible values against our two rules to 
	see if we can fill in a cell with a definite value. If at any time we do set a cell's value, 
	jump back to step 1.1 and determine all the possible values again.

The code for the [SudokuBoard][sudokuboard] class itself is fairly long, so I'll omit direct examples
here, but there are several main methods that can be called from a top-level solver class. These are
fillPossibleAll(), which fills all the possible values of all the open SudokuBoxes in the array, and
checkToSetAll(), which checks every box against my two rules to see if it can be set to a number with
100% certainty.

