# sudoku-AI

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
How to Run:		Pass in a sudoku puzzle consisting of a 9x9 grid with values from 1-9 in the sudoku_solver() function.
			Output gives the solution, or if the puzzle is unsolvable, a 9x9 grid filled with '-1's 


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Problem Description:	Sudoku is a puzzle in which you must fill the empty cells of a 9x9 grid
			with numbers from 1-9 subject to these constraints:
				
					1) No row can contain duplicates from 1-9. i.e., no multiple '2's in the same row
					2) No column can contain duplicates from 1-9. i.e., no multiple '2's in the same column
					3) No 3x3 block of the grid (where no blocks overlap and there are 9 blocks in the grid) can contain duplicates from 1-9. i.e., each 3x3 block must contain all numbers 1-9.

			The objective is to write an agent that solves these puzzles, given an input of a 9x9 grid with some fixed values.
			Some input puzzles will not be solveable. 
			
			The output of the program should be either:
				- A 9x9 grid of the solution to the input puzzle
				- A 9x9 grid consisting of '-1's for any puzzle which isn't solveable
				

			 Note: for difficult sudoku puzzles, there is a time constraint which we must meet for the puzzle to be considered solved.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Implementation:		To solve this problem, I decided to use constraints satisfaction. This is because, as shown above, there are clearly defined constraints that we can use to solve the problem.

			First, I identified these constraints and created 2d arrays to store and maintain them - these are called 'rowConstraints', 'columnConstraints' and 'squareConstraints'.
			Each one holds the possible values that can be used in their respective quality i.e., 'rowConstraints' is a 2d array, where each array represents a row and stores the possible values that can
			be used within that row. I initialised them so that they held all values from 1-9, and then used a nested loop to iterate through the input grid to remove invalid values.

			Using the same nested loop, I also created an array that holds all of the positions of the empty squares within the 9x9 grid. This is called 'empty'.
			
			From here, I created a dictionary, where each key is an empty slot in the grid, and the values are a set that stores the possible values for each slot that meets the constraints based on the previously
			created constraints. The set was created by taking the intersection of the 3 corresponding arrays of constraints for that particular square/slot. 


			This gives me a set of constraints for the given problem, that I can then use to perform constraints propogation and backtracking through. The initial function calls a recursive function that 
			does this to solve the problem.

			The recursive solution works like this:
					1) Select an empty slot to fill

					2) Check to see if the current state is complete:
						return the grid (solution)

					3) Otherwise check if it's invalid:
						return False (invalid selection made previously)

					4) Then if it is neither complete nor invalid:
						a) For each possible value for the empty square, place the value in the square.
						b) Update the empty square array and constraints
						c) Get the next square to be filled
						d) Recursively call the function, passing in the new square to be filled.
						e) Check if there is any selection of values where the grid has a solution:
							return the grid (solution)
						f) Otherwise, restore the values in the empty square array and constraints. Set the empty square to 0 again.
						g) Repeat steps a-f until all possible values have been tested.
					
					5) Then return False (no solution for this state)

			
			Initially, this seems quite complicated, but from a higher-level perspective, it is quite simple. Essentially, the solution is to repeatedly try different values in each empty square that meet the constraints
			given, until you get to a point where the next empty square has no possible values, then you backtrack and try another value for the previous empty square.

			The implementation was a bit more complicated, but I worked through it step-by-step, solving each problem that I came across one at a time until all difficulties were cleared.
			I will briefly describe the functions that I used to solve this problem, and how I implemented them.

			Firstly, the recursive function. This is called 'depthFirst' and works like I described above. It takes the selected empty square, the current grid state, the constraints and a stack as arguments.
			This function calls the other functions, that I will describe below, to perform its task.

			To select an empty slot to fill, I made a function which takes the array that stores the empty squares and returns one. Initially, this simply returned the first element of the array, but when I reached 
			the 'difficult' difficulty sudokus, my solution was too slow. To improve the speed, I changed the function so that it strategically returns the square with the least number of possible values, or return 
			'None' if there is an empty square with no possible values. This works because the program will have to try less combinations of values before reaching either a solution or determining that there is no solution,
			as the number of possible states it has to try is lower when there is a lower number of choices to be made.

			The next two functions I made: 'updateConstraints' and 'restoreConstraints' are designed to be called when a value is assigned to the grid and when the new state is determined to be invalid respectively.
			To implement 'updateConstraints', I used a loop and pattern matching to update the individual constraints by removing the selected value, as required for each empty square. 
			To implement 'restoreConstraints', I did the reverse of the above, and added the value back to the possible values stored in 'individualConstraints'. However, the implementation was not this simple, as 
			it was not possible to know if an empty square could take the removed possible value before it was removed. To overcome this, I created a 'stack' that stores 2d arrays that indicate whether each square in the
			grid held the previously input value. The 2d array is in the form : [ [9 0's or 1's - indicating rows], [9 0's or 1's - indicating columns], [9 0's or 1's - indicating blocks]]
			Each time updateConstraints is called, a new set of 'states' is pushed onto the stack, and each time restoreConstraints is called, the last input array is popped from the stack, and used to restore the constraints
			as required.

			Finally, two functions are used to check if the current state of the grid is valid or complete.
			'finalInvalid' checks whether there is a 0 in the grid - if there is, then the grid is incomplete.
			'solved' checks to see that all empty squares are filled.
			
			Once the program finishes the recursive calls and returns either the solution or False, the initial function either returns the solution or a 9x9 grid filled with '-1's as required.

		


Alternatives:		One of the ways I thought of tackling the problem was to have another dictionary - similar to 'individualConstraints' - that held the attempted values called 'attemptedConstraints'.
			Then at the start of the recursive function, I could take the set of possible values for the empty square to be the set of {individual constraints - attempted constraints}.
			I quickly realised that this was not necessary, and decided to take another path to solve the problem, but this perhaps could be an alternative way for me to solve the problem.


			I described above one of the ways I optimised my solution. This was done in the 'getNode' function, which selects and returns the next square to be filled. At first, I was going to implement this
			optimisation in the 'updateConstraints' function, but realised it would be slightly cumbersome to do so, as I would have to adapt the rest of my code to fit this. Implementing this in the 'getNode'
			function meant that I didn't have to alter the rest of my code, as it already fit quite well (and also makes more sense logically).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Research:		The solution is based on the constraints satisfaction approach from the Moodle Course. The design of the algorithm used is similar to the algorithm shown for the Eight Queen's example on Moodle.
			I also did research on some of the technical aspects of the implementation e.g., how to use sets in Python. 
			
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
Future Improvements:	My solution uses a lot of memory due to recursion and the need to retain a stack of values for backtracking to be performed (when restoring individual constraints).
			In future, I could try to reduce the memory usage and improve its memory efficiency. 

			I could also experiment and change the method I use to assign values to each square. For example, instead of looking at the problem from the perspective of empty squares, I could approach the problem 
			from the perspective that each value from 1-9 within a division of the grid must appear once, and so I could try to find an empty slot for each value, rather than the value for each slot.
			I am unsure whether this would be an improvement, but it would be interesting to see if I could make a solution taking this approach.
