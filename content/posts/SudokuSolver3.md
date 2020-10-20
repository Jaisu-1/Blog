+++
date = "2020-10-13"
title = "Build a Sudoku Solver - Part 3"
slug = "Build a Sudoku Solver - Part 3"
tags = [
    "Development",
    "Python",
    "Beginner"
]
categories = [
    ]
series = ["Sudoku Solver"]
+++

## Game Solver

With the Game and it's logic properly set, we can now move on to creating a Game Solver "AI". In this case, our AI will be a simple optimizing algorithm. One of the reasons, I created this project is to teach myself how to make Game AI, later on involving Deep Learning but a good way to develop intuition is to solve a small scale problem such as here. The AI will try to solve any given Sudoku Board in the least time possible. The problem statement can safely assume that the sudoku board given will be valid and solvable, as they will be tested to be so, from the previous class methods.The first step to solving any problem is to come up with a naive, brute-force approach and we shall do the same here.

<div align="center">
    <img src="/Sudoku-Resources/possiblesolves.png" alt="Solving" width=70%>
</div>

### Naive Approach

Given any sudoku board, how do we fill it with values such that it will reach a solved state? The simplest and perhaps the dumbest way to solve it is to literally try out every possible solution and check which one would solve it correctly. Is this approach really that bad? We can estimate how bad it can be with asymptomatic time complexity analysis.

Estimating this approach is really easy, we simply have to calculate the no. of possible permutations for the board. There are 81 cells in the board and each cell can take on a possible 9 values which gives us 9^81 possible boards and calculating the solution would take a time complexity of O(9^81) or O(9^(M*N)) for other values of Board Grid Sizes.
(Even though Sudoku is typically played in a 9 x 9 grid, it can take the form of other variants.)

This is extremely expensive by time and comes close to 3.16887646 × 10^64 years!! if each board took 1nanosecond to compute. So, given my vested interest in being able to see at least one sudoku solved, I want to improve on this solution.
How do we do that though? One way would be to avoid taking paths we've already taken. Without using additional space, if we could solve a sub-problem and then try solutions from that point on, then it would significantly reduce the time complexity. One such technique is called as Backtracking.

### Backtracking

Backtracking is an algorithmic-technique for solving problems recursively by trying to build a solution incrementally, one piece at a time, removing those solutions that fail to satisfy the constraints of the problem at any point of time.
A backtracking algorithm can be thought of as a tree of possibilities. In this tree, the root node is the original problem, each node is a candidate and the leaf nodes are possible solution candidates. We traverse the tree depth-first from root to a leaf node to solve the problem.

In our example of the Sudoku , we try filling digits one by one. Whenever we find that current digit cannot lead to a solution, we remove it (backtrack) and try next digit. This is better than naive approach (generating all possible combinations of digits and then trying every combination one by one) as it drops a set of permutations whenever it backtracks. These algorithms are by definition, recursive solutions and recursive algorithms must have well defined terminating or exit conditions.

#### Exit Conditions -

We can exit our problem once we have a Valid Board with no empty cells. So, our solution will require us to at all points, find empty cells and check for Validity. These can be factored out into seperate methods to be called in every iteration of the recursion as follows -

#### Is Valid

Checks if the Board is Valid or Not, is imported from the Previous Game Logic Class

``` python
def check_win(self):

    for row in range(9):
        if not self.__check_row(row):
            return False

    for column in range(9):
        if not self.__check_column(column):
            return False

    for row in range(3):
        for col in range(3):
            if not self.__check_square(row, col):
                return False

    self.game_over = True
    return True
```

Next, we need to check for empty cells.

#### Find Empty

We need to find empty cells. If there's none, then we can simply check for validity and exit the recursion. If not, we return the row and column of the nearest empty cell, insert a random integer from 0..9 and check for validity again and so on. The find empty method would be a simple O(N ^ 2) brute force search for a cell whose value is 0.

``` Python
def find_empty(self, bo):
    for i in range(len(bo)):
        for j in range(len(bo[0])):
            if bo[i][j] == 0:
                return (i, j)  # row, col

    return None
```

Now that we have 2 of our helper methods ready, we can begin solving the sudoku board with the Backtracking algorithm which can be described as follows -

1. Find nearest empty cell.
2. Place a number, between 1 and ‘n=9’, in it and validate the candidate grid.
3. If any of the constraints fails, abandon candidate and repeat step 2 with the next number. Otherwise, check if the goal is reached.
4. If a solution is found, stop searching. Otherwise, repeat steps 2 to 4.

Visually we can think of the algorithm as the following

<div align="center">
    <img src="/Sudoku-Resources/solving.gif" alt="Solving">
</div>

Our code is very simple for the algorithm -

``` Python
def solve(self, board):
    """
    The Main Game Solver lies in this method.
    Solves through backtracking and reduces Time Complexity from O(N!)
    to O(n ^ m) where m = n. of blank spaces.
    """

    # Find nearest empty cell
    find = self.find_empty(board)

    # No empty cell, so return because we've found our solution
    if not find:
        return True
    else:
    # Get nearest empty row and column
        row, col = find

    for i in range(1,10):

        # Try all solutions and put in place
        if self.valid(board, i, (row, col)):
            board[row][col] = i

            # If it's a solution, then return
            if self.solve(board):
                return True

            # Else, make it empty again
            else:
                board[row][col] = 0

    # No solution has been found, so return false
    return False
```

And thus, we'd have solved any sudoku board as will be seen in the GUI later.

## Game Generator

With the ability to solve any Sudoku Board, can we unlock something else after this? Yes, the ability to generate games. How do we do that? A very simple way is to make a solve board and then delete random cells depending on the difficulty level.

1. Start with an empty grid.
2. Generate a complete solution using backtracking that fills up the grid.
3. Remove numbers from the grid, while making sure the solution is still unique.


##### Generate a complete solution -
```Python
def sudokuGen():
    '''Generates a completed sudoku. Sudoku is completly random'''
    cells = [i for i in range(81)] ## our cells is the positions of cells not currently set
    sudoku = emptySudoku()
    while len(cells) != 0:
        lowestNum = []
        Lowest = []

        for i in cells:
            lowestNum.append(sudoku[i].lenOfPossible())  ## finds all the lengths of of possible answers for each remaining cell
        m = min(lowestNum)  ## finds the minimum of those

        '''Puts all of the cells with the lowest number of possible answers in a list titled Lowest'''
        for i in cells:
            if sudoku[i].lenOfPossible() == m:
                Lowest.append(sudoku[i])

        '''Now we randomly choose a possible answer and set it to the cell'''
        choiceElement = random.choice(Lowest)
        choiceIndex = sudoku.index(choiceElement)
        cells.remove(choiceIndex)                 

        position1 = choiceElement.checkPosition()
        if choiceElement.solvedMethod() == False:  ##the actual setting of the cell
            possibleValues = choiceElement.returnPossible()
            finalValue = random.choice(possibleValues)
            choiceElement.setAnswer(finalValue)

            for i in cells:  ##now we iterate through the remaining unset cells and remove the input if it's in the same row, col, or box
                position2 = sudoku[i].checkPosition()
                if position1[0] == position2[0]:
                    sudoku[i].remove(finalValue)
                if position1[1] == position2[1]:
                    sudoku[i].remove(finalValue)
                if position1[2] == position2[2]:
                    sudoku[i].remove(finalValue)

        else:
            finalValue = choiceElement.returnSolved()
            for i in cells:  ##now we iterate through the remaining unset cells and remove the input if it's in the same row, col, or box
                position2 = sudoku[i].checkPosition()
                if position1[0] == position2[0]:
                    sudoku[i].remove(finalValue)
                if position1[1] == position2[1]:
                    sudoku[i].remove(finalValue)
                if position1[2] == position2[2]:
                    sudoku[i].remove(finalValue)
    return sudoku
```

##### Remove numbers from the grid based on the difficulty -
``` Python
def generate(level):
    """ Input the level of difficulty of the sudoku puzzle. Difficulty levels
        include ‘Easy’ ‘Medium’ ‘Hard’ and ‘Insane’. Outputs a sudoku of desired
        difficulty."""
    n = 0
    if level == 'Easy':
        p = perfectSudoku()
        s = puzzleGen(p)
        if s[2] != 'Easy':
            return generate(level)
        return convertToArray(s[0])
    if level == 'Medium':
        p = perfectSudoku()
        s = puzzleGen(p)
        while s[2] == 'Easy':
            n += 1
            s = puzzleGen(p)
            if n > 50:
                return generate(level)
        if s[2] != 'Medium':
            return generate(level)
        return convertToArray(s[0])
    ...
    else:
        raise(ValueError)
```

### Next Steps
With the ability to solve and generate Sudoku Boards at ease, all that's left to do is to create a Graphical User Interface for Native OS using Tkinter and that should wrap up our application. See you there!
