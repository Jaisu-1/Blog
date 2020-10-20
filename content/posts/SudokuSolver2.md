+++
date = "2020-10-12"
title = "Build a Sudoku Solver - Part 2"
slug = "Build a Sudoku Solver - Part 2"
tags = [
    "Development",
    "Python",
    "Beginner"
]
categories = [
    ]
series = ["Sudoku Solver"]
+++

## Game Logic

Having built our basic board structure, we can create and implement the game logic on top of the existing data structure that is our board.
Despite no apparent need for putting the Game Logic in a class, I shall do so to make sure I'm writing clean OOP code. This will enable us to import the methods neatly later on , makes the code readable and ensures structural integrity even in a place where it's not necessary.
<br>
<center>
    <img src="/Sudoku-Resources/gamelogic.jpg" alt="gamelogic">
</center>
<br>

Our Game Logic , will have properties that are present in any Game, which are -
  1. Starting the Game
  2. Checking for Win Conditions
  3. Exit the Game
  4. Restart the Game   

And them, have properties that are present specifically for Sudoku Board -

  1. No two instances of same number in Row
  2. No two instances of same number in Column
  3. No two instances of same number in Box

### Start Board

Out first step in creating the game logic, would be to initiate the board that we can simply import from the Sudoku Board file. Our start puzzle method takes care of initiating or setting up the Board Game. It does this by simply initiating the Sudoku Board and then running the integrity tests. Once all the tests are passed,  our board can be passed to a class variable which stores the 2d variables.

This is how the code would look like.

```Python
class SudokuGame(object):
    """
    """
    def __init__(self, board_file):

        self.board_file = board_file
        Board = SudokuBoard(board_file)
        self.start_puzzle = Board.board

    def start(self):
        """
        """
        # Final Solved Flag
        self.game_over = False
        self.puzzle = []
        for i in range(9):
            self.puzzle.append([])
            for j in range(9):
                self.puzzle[i].append(self.start_puzzle[i][j])
```

### Win Conditions

To solve the puzzle, you need to fill in the blank squares with numbers ranging from 1 to 9. The catch is that each square must contain a value that is unique to that row, column, and box.

<br>
<center>
    <img src="/Sudoku-Resources/sudoku-solved.jpg" alt="solved">
</center>
<br>

#### Check Win

The Check Win method has 4 Conditions to be met for it to pass the flag Victory to ```true```. All of them share the property that the set of the elements in that block, should be exactly equal to the range of elements 0..9. If there's any duplicate in the block, then the set of elements becomes a subset of the range(0..9). We can write a common method for this to extract functionality out.

``` Python
def __check_block(self, block):
    return set(block) == set(range(1, 10))
```

 A victory condition is triggered when each of the following conditions are met. In each of them, all we have to do is call the check_block but arrange the block properly.

**1. Row condition**

``` Python
def __check_row(self, row):
    return self.__check_block(self.puzzle[row])
```

**2. Column condition**

``` Python
def __check_column(self, column):
    return self.__check_block(
        [self.puzzle[row][column] for row in range(9)]
    )
```

**3. Box condition**

``` Python
def __check_square(self, row, column):
    return self.__check_block(
        [
            self.puzzle[r][c]
            for r in range(row * 3, (row + 1) * 3)
            for c in range(column * 3, (column + 1) * 3)
        ]
    )
```
**4. Full Board condition**

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

In each of the above conditions, the terms to trigger victory remain the same, each of the above structures cannot have any repeating integers from 0..9. Once it's verified that each of these conditions are met, our puzzle is solved and we can wave the victory flag. 

### Exit Condition

The exit condition are triggered once , we have solved our puzzle or click the quit button and once this condition is triggered, we exit the main rootloop by calling the interrupt.

Our puzzle board will now look like this.

```Python
[
[2, 1, 0, 0, 0, 0, 4, 0, 0],
[3, 8, 0, 4, 0, 0, 7, 0, 2],
[0, 0, 0, 7, 2, 0, 0, 0, 0],
[0, 2, 4, 8, 0, 6, 9, 0, 0],
[0, 0, 0, 0, 0, 0, 0, 0, 0],
[0, 0, 1, 2, 0, 3, 5, 4, 0],
[0, 0, 0, 0, 5, 8, 0, 0, 0],
[9, 0, 3, 0, 0, 4, 0, 2, 8],
[0, 0, 8, 0, 0, 0, 0, 5, 7]
]
```

This is a very raw text form of the data but forms the "database" for the project.


### Next Steps
That wraps up our game logic. We have the win conditions, start game and finish game, and our board is tested for various conditions so we can move onto creating the Game AI and a Game Generator.


In the [next](/posts/build-a-sudoku-solver-part-3/) post, we'll solve the tougher challenges of making a Game Solver and a Game Generator.
