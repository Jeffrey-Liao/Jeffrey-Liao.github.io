# Terminal Bouncing Ball Implementation **(40 points)**

## Overview

In this exam, you will implement a simple terminal-based bouncing ball animation. You are provided with a terminal drawing library (`terminal_draw.h`) that handles the basic terminal operations. Your task is to implement the ball physics, drawing functions, and collision detection.

## Provided Files

- `terminal_draw.h`: A complete library for terminal manipulation (drawing characters, clearing screen, etc.)
- `bouncing_ball.c`: A skeleton file with the main game loop and function signatures

## Available Terminal Drawing Functions

The following functions are provided in `terminal_draw.h`:

- `void clear_screen()`: Clears the terminal screen
- `void get_screen_size(int *width, int *height)`: Gets terminal dimensions
- `void draw_char(int x, int y, char c, Color color)`: Draws a character at specified coordinates
- `void sleep_ms(int milliseconds)`: Sleeps for given milliseconds
- `enum Color: Available colors (BLACK=30, RED=31, GREEN=32, YELLOW=33, BLUE=34, MAGENTA=35, CYAN=36, WHITE=37)`
- `#define NUM_COLORS 8`: Number of available colors

## Tasks
1. Basic Structures **(5 points)**

    Implement the following structures:

    - `Position`: Contains `x` and `y` coordinates
    - `Ball`: Contains position, fill color, and the character used to draw the ball

2. Line Drawing Functions **(8 points)**

    Implement two functions for drawing lines using the provided draw_char function:

    - `draw_hline(int x, int y, int length, char c, Color color)`: Draws a horizontal line
    - `draw_vline(int x, int y, int length, char c, Color color)`: Draws a vertical line

3. Ball Drawing **(10 points)**

    Implement the `drawBall(Ball *ball)` function that draws the ball in this exact pattern:

    ```
     XXX
    XXXXX
     XXX
    ```
    Where `'X'` represents the fill character of the ball, and the ball's position (stored in the structure) represents the center of the pattern.

4. State Change Functions `(7 points)`

    Implement two functions that handle state changes during collisions:

    - 'getNextColor(Color color)': Returns the next color in sequence (cycles through available colors)
    - 'getNextFillChar(char c)': Toggles between `'O'` and `'X'` characters

    The functions are given the current color and fill character as arguments, and return the new color and fill character respectively.

5. Collision Detection **(10 points)**

    Implement two collision detection functions:

    - `isCollidingX(Ball *ball, int screenWidth)`: Detects collision with left and right walls
    - `isCollidingY(Ball *ball, int screenHeight)`: Detects collision with top and bottom walls

    The functions should return true when any part of the ball pattern touches the border.

## Requirements and Behavior

1. The screen must have a blue border:
    - Horizontal borders use `'-'` character
    - Vertical borders use `'|'` character

2. Ball initialization:
    - Ball starts in the center of the screen
    - Initial fill character is `'O'`
    - Initial color is `RED`
    - Initial movement is one position right and down per frame

3. Ball movement:
    - When hitting top/bottom borders:
        - Vertical direction reverses
        - Fill character toggles between `'O'` and `'X'`
    - When hitting left/right borders:
        - Horizontal direction reverses
        - Ball color changes to the next available color

## Debugging Help

The skeleton code includes commented debug output that shows:

- Current ball X and Y coordinates
- Current screen width and height

These can be uncommented to help with development and testing.

## Evaluation Criteria

- Correct implementation of structures **(5 points)**
- Proper line drawing functions **(8 points)**
- Accurate ball drawing pattern **(10 points)**
- Correct state change functions **(7 points)**
- Accurate collision detection and ball movement **(10 points)**
- Code style and comments will be considered in the final grade

## Notes

- The main game loop is provided
- Terminal drawing functions are provided in `terminal_draw.h`
- Focus on implementing the required functions with the exact signatures as specified
- Test your collision detection thoroughly, as it's the most valuable part of the assignment
- Not only the implementation of the required functions, but also their usage is included in the grading
- The solution can be implemented by using exactly one lab computer without the usage of any other electronic device. In case of breaking this rule, the solution will be given 0 points and one may get maximum grade 2 at the end of the semester.