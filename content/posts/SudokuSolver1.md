+++
date = "2020-10-10"
title = "Build a Sudoku Solver - Part 1"
slug = "Build a Sudoku Solver - Part 1"
tags = [
    "Development",
    "Python",
    "Beginner"
]
categories = [
    ]
series = ["Sudoku Solver"]
+++

### Sudoku Game

Sudoku is a logic-based, combinatorial number-placement puzzle. In classic sudoku, the objective is to fill a 9×9 grid with digits so that each column, each row, and each of the nine 3×3 subgrids that compose the grid [also called "boxes", "blocks", or "regions"] contain all of the digits from 1 to 9. The puzzle setter provides a partially completed grid, which for a well-posed puzzle has a single solution.

[Gif]

### Why Sudoku?

Creating a game and then making an intelligent system which can play or solve the game is my fundamental goal and for some reason something that really interests me. The applications I want to build have 2 components - The Game, The AI. I wanted to create increasingly complex game-solvers to learn both game making (Software development) as well as Automation (using algorithms or Deep Learning) and my first step in learning to do so was a game I've been playing all my life, Sudoku.

An average game is a perfect example of OOP concepts coming together and being tested at their core principles and it is incredibly satifying to create good clean OOP compliant code. Creating a game teaches good OOP practices and is a great way of learning how to design architectures and class structures that scale well, which is a great skill to develop when my person goal is to build a large SaaS or OS at some point in the future.

The project was created with Tkinter as I want to learn to make and build OS based programs. Python and Tkinter also happen to be incredibly accessible and can be easily be switched to C++ and QT for improved efficiency and performance. But for now, Python will do the job, especially to understand core concepts.

I also do want to write good Scalable and deployment quality software and think that the difference between an average programmer and an engineer is knowing good design, something that i hope to learn by documenting my projects. An added motivation was to write software with tests and error handling wherever possible as well as being in control of deployment for now. After a few native OS GUI projects however, it might be better to switch to web based hosting as Tkinter and QT aren't very scalable and make it tough to build complex programs unlike Full Stack programs on the web.

### Prerequisites

OOP concepts [link] to oop concepts.

Tkinter is the library that I shall be using to create the game and it's the only framework you'll need to know outside of native python itself. (Tkinter is a part of python so no external installments required).

Tkinter is a GUI library for Python that enables developers to write GUI Applications for Windows, Linux and Mac OSX using nothing but Python. Tkinter, like other similar GUI applications creates a master frame to which one can tie Buttons, widgets, windows and events to create a visual software.

It has some key components that would be good to know before jumping in.

1. Frame
2. Canvas
3. Buttons
4. Label
5. Mainloop

#### Frame

A frame in Tkinter lets you organize and group widgets. It works like a container. Its a rectangular area in which widgets can be placed.

If you make a GUI app, you’ll be using different widgets. Those widgets need to be organized somehow, and that’s where a frame comes in.

``` Python
from tkinter import *

root = Tk()
frame1 = Frame(root)
```

#### Canvas

The canvas is a general purpose widget, which is typically used to display and edit graphs and other drawings.

To draw things in the canvas, use the create methods to add new items.
``` python
from Tkinter import *

master = Tk()
w = Canvas(master, width=200, height=100)
w.create_line(0, 0, 200, 100)
w.pack()
```

#### Buttons
The Button widget is a standard Tkinter widget used to implement various kinds of buttons. Buttons can contain text or images, and you can associate a Python function or method with each button. When the button is pressed, Tkinter automatically calls that function or method.


``` Python
button = tk.Button(frame,text="QUIT",fg="red",command=quit)
button.pack(side=tk.LEFT)
```
#### Labels
This widget implements a display box where you can place text or images. The text displayed by this widget can be updated at any time you want.

It is also possible to underline part of the text (like to identify a keyboard shortcut) and span the text across multiple lines.

```Python
root = Tk()
label = Label( root, textvariable=var, relief=RAISED )
```

#### Mainloop

Now comes the most significant concept in any GUI application which the mainloop, Games and Graphics driven programs can be understood as a continously running loop which waits for events to occur (such as Clicking a Button, Taking an input) and upon said event , triggers some action written into's back end.

root.mainloop() specifically, is a method on the main window which we execute when we want to run our application. This method will loop forever, waiting for events from the user, until the user exits the program – either by closing the window, or by terminating the program with a keyboard interrupt in the console.


These 5 simple concepts will allow us to build a complex GUI application that looks like this.

![Image of Final GUI app](Main.jpg)

## Game Structure

### Board Logic

Our Game creation starts by crafting the board. The structure of the board is integral to the entire program and for our game to be developed and run smoothly. We need to create the most structurally sound Board Structure (Class in Python). A structurally sound Sudoku Board needs to maintain at all times, these 4 properties. If any of these properties fails at any time, the whole Game fails to exist as a logically cohesive program.

1. Maintain 9 Rows.
2. Maintain 9 Columns.
3. All characters on the table are Digits.
4. Is our structure, a 2D Table [Simple Hashmap].

``` Python
class SudokuBoard(object):
    """
    """
    def __init__(self, board_file):
        self.board = self.__create_board(board_file)

    # Create Private Method
    def __create_board(self, board_file):
        # A 2D Matrix or a list of list or defaultdict(list)
        # would be created
        board = []

        # Step 1 : Iterate over each row
        for row in board_file:
            row = row.strip()

            # Step 2 : Go through each column
            if len(row) != 9:
                board = []
                raise SudokuError("Must have exactly 9 rows.")

            # Add a list to each rows.
            board.append([])

            # Iterate over each character
            for c in row:
                # Raise error if character is not an integer
                if not c.isdigit():
                    raise SudokuError("Only digits from 0-9 are allowed to be characters.")
                board[-1].append(int(c))
        # Check for 9 lines (Assert over it), raise error if not true
        if len(board) != 9:
            raise SudokuError("Each sudoku puzzle must be 9 lines long")

        # Return the fully constructed board
        return board
```

### Grid Logic

Our 9 x 9 Grid has essentially 81 boxes lumped together in a simple square structure. Each of these units is called a Cell and will be used to maintain the structure for both the GUI and the game logic. Let's say each of these Cells has a size Size and a margin Margin (space between the frame and the grid itself).

Given these parameters, our GUI will have a height and weight as follows.

```
Grid = 9 * Size
Full_Margin = 2 * margin
WIDTH = HEIGHT = Full_Margin +  GRID
```

WIDTH and HEIGHT are global variables and will be shared around the entire project.


### Error class

We define our own Exception because we want to create error handling that’s specific to our Sudoku application, rather than file I/O exceptions, or database exceptions, etc. This is all we need to create our own exception:

``` Python
class SudokuError(Exception):
    """
    """
    def __init__(self, error):
        self.error = error
        raise self.error
```

This forms a solid foundation for us to build the Game Logic and the AI later on, which will see in Part-2.

Next Up :- Building the Game Logic and the Script for Back-End.
