---
layout: post
title: "Why I Don't Like Sudoku Anymore"
author: "Ian Zapolsky"
comments: true
category: posts
---

Sometime during the spring of 2013 Ruchir came to me with the idea of designing a program that could
solve sudoku puzzles as a possible weekend diversion. We talked briefly about how we might do it,
but in the midst of school and homework, it fell by the wayside.

However, recently, after becoming basically addicted to a sudoku app on my phone, I decided to take a 
crack at the project. I started to think about how a computer might play sudoku, and initially derived
two algorithmic strategies for solving these puzzles, both of which require sweeping over the entire
puzzle first and determining all of the possible values for each open box on the board (which is a task 
better suited for a computer than a human, trust me).

1. Obvious Elimination: if a box has only one possible value, then it can only be set to that value.
2. Unique Possibility: if a box has multiple possible values, but one of its possible values is not 
possible for any other open cell in that box's row, column, or square, then it must be set to that value.

Going off these two naive observations, I built sudoku\_solver, which is made up of three main classes:
SudokuBox, which stores the current value and a list of possible values for each cell, SudokuBoard, which 
represents a sudoku board as a 9x9 2D array of SudokuBoxes, and SudokuSolver, which controls the flow of
the puzzle-solving logic that is implemented in SudokuBoard. 

Because I don't, or didn't, know anything beyond the very basics about sudoku, programming and testing 
this little tool was surprisingly exciting. It was pretty dope to code up my hypothesis solution and then
see what kind of puzzles it could solve, and how fast. 

Details
=======

Every sudoku board has 9 rows, 9 columns, and 9 3x3 boxes (which subsequently form 3 rows and columns of
3 boxes each).  *The* central rule of all sudoku puzzles is that each of these rows, columns, and squares 
on the greater 9x9 board must contain numbers 1-9, inclusive and non-repeating. 

![sudoku](/images/sudoku.png)

1. While our puzzle is not complete:
	1. Sweep through the entire board, using the cells who's values we already know to determine the 
	possible values for each empty cell.
	2. Sweep through the board again, checking every cell's possible values against our two rules to 
	see if we can fill in a cell with a definite value. If at any time we do set a cell's value, 
	jump back to step 1.1 and determine all the possible values again.



Say we have some cell x that we're looking at. In order to determine all of x's possible values, we need 
to check against all the cells that have already been set in x's row, column, and box. Furthermore, when 
we go on to check x's possible values for a Unique Possibility, we also need to check against all the
possible values of the cells that are in x's row, column, and box. One challenge I ran into early was
how to 

The only tricky piece of logic that I ran into while writing this up had to do with the way 3x3 squares of
SudokuBoxes are accessed in the greater SudokuBoard data structure. *The* central rule of all
sudoku puzzles is that each row, column, and 3x3 square on the the greater 9x9 board (each of which 
have 9 total cells) must contain the numbers 1-9, non-repeating. In order to check that this is happening, we need
to be able to iterate through each row, column, and square on the board really quickly. Rows and columns are
easy enough, all we need to do is loop from 0-8, keeping either the row or column 2D index the same. However,
boxes are a little tougher. See the below diagram. Given any number 0-8, we need to be able to iterate through
the correct 9 cells that correspond to that box. Check out the image below, which shows these 9 boxes very clearly,
divided by thicker black lines.


It's important to try to break these boxes up into groups. What I initially noticed is that there are three 
boxes in "box row" 0 (the top 3), 3 more in "box column" 0 (the leftmost 3 on each row), and so on. If we look
at the top 3 boxes, they are numbered 0, 1, and 2. When we divide all these numbers by 3, we get 0, which is
the starting row index for each of them. If we look at the next three boxes, 3, 4, and 5, we see that when we
divide by 3 we only get 1, but each of these boxes begin on row 3. Likewise, when we divide 6, 7, or 8 by 3 
we get 2 but each of these boxes begin on row 6. So we see that in order to find the starting row index for
a given box, we need to divide the box number by 3, rounding down to the nearest integer, then multiply by 3.

To find the starting column, we need to divide by mod 3, then multiply by 3.   


{% highlight java %}
...
SudokuBoard board = new SudokuBoard();

// picking random row, column, and square in order to
// demonstrate iteration
int row = 3;
int column = 6;
int square = 2;	

// iterate over row
for (int i = 0; i < 9; i++)
	board[row][i].doSomething();

// iterate over column
for (int i = 0; i < 9; i++)
	board[i][column].doSomething();

// iterate over square
for (int i = 

{% endhighlight %}

			






