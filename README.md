# Static Snake

This is an implementation of the popular Nokia game "Snake" as a single static html file, for the purpose of a Youtube tutorial. --link to video-- (I didn't make the video yet)

## Introduction

i wanted to make a tutorial for making a simple snake game in a static html file for beginner programmers who have some familiarity with html and javascript and want to practice their skills on a fun project. This tutorial assumes that the reader knows about html tags and attributes, css styles, and javascript variables, structs, arrays and functions.

The tutorial will consist of 3 parts, namely the Model, i.e. the data required to adequately represent the game in javascript, the View, i.e. the graphical elements that are used to display the data on the screen, and the Controller, i.e. the game logic and controls. This is because the MVC pattern is a good way to organise any graphical application.

Firstly, let us create a html file with the necessary boilerplate html code:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>STATIC SNAKE</title>

    <style>
        /* Add css styles here. */
    </style>
</head>
<body>
    <!-- Add html tags here. -->

    <script>
        // Add javascript code here.
    </script>
</body>
</html>
```

## Thinking about the game

"Snake" consists of a rectangular (usually square) board of cells in which a snake slithers around, a fruit summons at random and upon eating the fruit, the snake grows in size by one cell and the score increases by one. The snake consists of an array of adjacent cells where the player can control the movement direction of the first cell, or the head, the second cell follows the head, the third follows the second, and so on. Ideally, we will need to be able to quit the game and also pause it. To pause and unpause the game, I will use the spacebar, and to stop the game, and start a new game after it is stopped, I will use the "Enter" key.

For the Model, we will have to store the size of the board, an array of cell positions for the snake, a vector for the movement direction of the snake, a cell position for the fruit, a boolean for checking if the game is paused and another boolean for checking if the game is stopped (either voluntarily or when the player loses the game).

For the View, we will use the `<canvas>` html tag and draw the game into it. We will also need a way to display the score.

For the Controller, we need to write a game loop, i.e., a function that runs continuously to simulate time in the program and move the snake periodically. Then we need a function that will move the snake, and an event controller to handle all the keyboard inputs.

## Model

As we have thought about the game, we will create the variables to hold the necessary values.

```
<script>

    const boardSize = { x: 20, y: 20 }; // board size
    cosnt scale = { x: 20, y: 20 }; // size of one cell in the board in pixels

    const bgColor = "white";
    const snakeColor = "lightgreen";
    const fruitColor = "orange";

    let snake;      // array of cell positions for the snake
    let dir;        // vector for snake's movement direction
    let fruit;      // cell position for fruit

    let isPlaying;      // boolean to check if the game is playing or paused
    let isGame = false; // boolean to check if the game is playing or stopped

    let speed = 100;    // delay time (in milliseconds) for each iteration of the game loop

    /* Future javascript code goes here. */
</script>
```

I have refrained from initializing some variables because if we initialize them where we have now declared them, we can only play the game once, and to replay the game, we will have to reload the html file in the browser, which we all can agree is a little inconvenient. Therefore we will have to initialize these variables in a function, and call this function whenever we want to start the game. Therefore we will define an `initialize` function for this purpose.

```
<script>
    /* Previous javascript code goes here. */

    const randInt = (max) => { // function to produce a random integer between 0 (inclusive) and max (exclusive).
        Math.floor(Math.random() * max)
    }

    const makeFruit = () => { // function to generate a fruit at a random position not on the snake
        do {
            fruit = { x: randInt(boardSize.x), y: randInt(boardSize.y) };
        } while (snake.some(
            (cell) => cell.x === fruit.x && cell.y === fruit.y
        ))
    }

    const initialize = () => {
        snake = [               // snake initialized to have 5 cells and oriented to move up
            { x: 10, y: 10 },
            { x: 10, y: 11 },
            { x: 10, y: 12 },
            { x: 10, y: 13 },
            { x: 10, y: 14 }
        ];

        dir = { x: 0, y: -1 };  // movement direction initialized to move up

        makeFruit(); // call the function to generate the fruit

        score = 0;
        isPlaying = true;
    }

    /* Future javascript code goes here. */
</script>
```

I made a function to generate the fruit, namely `makefruit`, instead of writing the necessary code inside `initialize` itself because we will have to generate the fruit many times in the game whenever the snake eats the fruit.

with this, our model is completed.

## View

As discussed in the thinking section, we will use the `<canvas>` tag. Here is the necessary html code.
```
<body>
    <canvas id="gameCanvas">
        <div>This browser cannot display this game :(</div>
    </canvas>
    <div id="s">Score: <div id="score">0</div></div>
⋮
```

This creates a `<canvas>` tag in html, and if for some reason the browser you are using doesn't support this tag, it will display the `<div>` tag inside it. It also creates a `<div>` tag to display the score.

now we will have to place this tag at the center of the screen. Following is the necessary css style to do this:
```
<style>
    body {
        min-height: 100vh;
        background-color: 'black';
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .s {
        color: 'white';
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 40px;
    }

</style>
```
This will turn the screen black and set the canvas in the center. But if you open the html file in your browser at this point, you will see only a black screen in your website. This is because we have not yet set the width and height of the canvas. Let us do that now.

```
<script>
    /* Previous javascript code goes here. */

    const canvas = document.getElementById("gameCanvas");
    canvas.width = boardSize.x * scale.x;
    canvas.height = boardSize.y * scale.y;

    const score = document.getElementById("score");

    /* Future javascript code goes here. */
</script>
```

Then we will have to make 3 functions, one for clearing the screen and filling it with `bgcolor`, one for rendering a single cell at a given position and of a given color, and one for rendering the snake to the board.

```
<script>
    /* Previous javascript code goes here. */

    const ctx = canvas.getContext("2d"); // get an object that allows us to render objects to the canvas
    
    const clearBoard = () => { // function to fill the board with the background color
        ctx.fillStyle = bgColor;
        ctx.fillRect(0, 0, boardSize.x * scale.x, boardSize.y * scale.y);
    }

    const renderPixel = (pos, color) => { // function to render a single cell in the board at position "pos" and color "color"
        ctx.fillStyle = color;
        ctx.fillRect(pos.x * scale.x, pos.y * scale.y, scale.x, scale.y);
    }

    const renderSnake = () => { // function to render the snake to the board
        snake.forEach(cell => renderPixel(cell, snakeColor));
    }

    /* Future javascript code goes here. */
</script>
```

With this, our View is completed.

## Controller

First, let us think of what we need to do in order to move the snake on the screen. There are broadly 2 ways of doing this.

1. update the snake array, clear the screen, render the snake, and render the fruit.

2. update the snake array and simultaneously remove the previous tail cell from the screen and add the new head cell to the screen.

The first approach separates the game logic and rendering, but the second couples them into a single function. Generally, it is best practice to decouple behaviours that are not directly related to each other, but in game development, we often see that in the quest for optimization, this type of coupling is often allowed. We can see that the second approach is much more efficient than the first, and while this might not matter at all for running a snake game on any modern computer, imagine this dilemma from the perspective of designers of the actual snake game on Nokia phones. In any case, I will opt for the second approach, but it might be a fun exercise to implement the first approach yourselves. Here is my function:

```
<script>
    /* Previous javascript code goes here. */

    const moveAndRender = () => {
        // make the new head of the snake
        const head = { x: snake[0].x + dir.x, y: snake[0].y + dir.y };

        // check for collisions
        if (
            head.x < 0 || head.y < 0 ||
            head.x >= boardSize.x || head.y >= boardSize.y ||
            snake.some(cell => cell.x === head.x && cell.y === head.y)
        ) { // if the snake has collided with itself or the walls, end the game
            isPlaying = false;
            isGame = false;
        }

        // check if the snake has eaten the fruit
        if (head.x === fruit.x && head.y === fruit.y) { // if the snake ate the fruit
            score.innerText += 1;           // increase the score
            makeFruit();                    // make a new fruit
            renderPixel(fruit, fruitColor); // render the new fruit to the screen
        } else { // if the snake has not eaten the fruit, remove the tail from the array and the screen
            renderPixel(snake.pop(), bgColor);
        }

        renderPixel(head, snakeColor);  // render the new head to the screen
        snake.unshift(head);            // add it to the array
    }

    /* Future javascript code goes here. */
</script>
```

Now we will write the game loop function:
```
<script>
    /* Previous javascript code goes here. */

    const run = (control = true) => {
        if (control === false) return;
        setTimeout(() => {
            moveAndRender();
            run(isPlaying);
        }, speed);
    }

    /* Future javascript code goes here. */
</script>
```

Since javascript does not have a typical delay function like other languages, we will have to use this recursive game loop function. It will iterate every `speed` number of milliseconds as long as `isPlaying` is true.

Now we will need an event handler to handle keyboard input. the following is the same.
```
<script>
    /* Previous javascript code goes here. */

    const keyboardInput = (e) => {
        // four movement directions
        if (e.key === "ArrowUp" && dir.y != 1) dir = {x: 0, y: -1};
        else if (e.key === "ArrowDown" && dir.y != -1) dir = {x: 0, y: 1};
        else if (e.key === "ArrowRight" && dir.x != -1) dir = {x: 1, y: 0};
        else if (e.key === "ArrowLeft" && dir.x != 1) dir = {x: -1, y: 0};

        // pressing spacebar to play/pause the game
        else if (e.key === " ") {
            if (isPlaying) isPlaying = false;
            else {
                isPlaying = true;
                run();
            }
        }

        // pressing enter to play/stop the game
        else if (e.key === "Enter") {
            if (isGame) {
                isPlaying = false;
                isGame = false;
            } else {
                initialize();
                clearBoard();
                renderSnake();
                makeFruit();
                renderPixel(fruit, fruitColor);
                isGame = true;
                run();
            }
        }
    }

    /* Future javascript code goes here. */
</script>
```

Then finally we will have to actually clear the screen once when the page loads, and connect the event handler to the key press event.

```
<script>
    /* Previous javascript code goes here. */

    clearBoard();
    document.addEventListener("keydown", keyboardInput);
</script>
```

With this, we have a basic snake game inside a single html file. Enjoy.