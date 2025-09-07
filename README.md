# Static Snake
This is an implementation of the popular Nokia game "Snake" as a single static html file, for the purpose of a video tutorial on Youtube.
--link to video-- (I didn't make the video yet).

The following is a text based tutorial of the same, that will teach you key programming concepts used on a daily basis by programmers, while at the same time making a fun little game you can play in your free time.

## Introduction
What is it that we need to make a game?

Scratch that. What is it that we need to make any graphical application? We will need some graphics of course, but the graphics have to be about something, so we will need some stuff, or in other words data, that the the graphics can represent and not just be abstract gibberish. And finally we will need to be able to manipulate this data, or else any time we open our application, it will simply cycle through a bunch of predetermined graphics. In the language of programming, we refer to these three requirements (graphics, data, controls) as MVC (model, view, controller). 

Here the model is our data, and within the name "model" itself there is the implication that our data needs to have a particular structure so that we can use it effectively. The "view" here is the graphics. It is simply the visual representation of the structure of our data. The view will only be any good at representing the data if the data is well structured. The "Controller" is broadly the ways in which you are allowed to transform the data.

## Setup
We start our setup by creating a generic html file. I am doing this in Visual Studio code, which can generate the necessary boilerplate code for an html file. I named my file `index.html`, but since this project is going to be contained in a single file, you can name it whatever you want, as long as it has the `.html` extension.

Boilerplate html:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>STATIC SNAKE</title>

	<style>
	    /* put all your CSS styles here to style your View */
	</style>
</head>
<body>
	<!-- This is where the html body, i.e the View, goes -->

	<script>
		/* 
			This is where your javascript code, which contains both
			the Model and Controller goes.
		*/
	</script>
</body>
</html>
```

## View
Since our game is fairly simple, we will not need any fancy graphics libraries and such to implement the View. We will need only the `<canvas>` html tag. We will mainly use this tag to render rectangles onto it. To use it, simply add it inside the `<body>` tag just above the `<script>` tag.
```
⋮
<body>
	<canvas id="gameCanvas"></canvas>
⋮
```

This will not be enough, so we will need to specify a `width` and `height` attribute to the `<canvas>`, but for ease of use and customization, I like to do this from inside javascript. To do this, we need to select the canvas using its `id` attribute, which is a unique identifier you can give to html tags. Here, we can see from the code that the `id` is `gameCanvas`. So this will be our javascript code.

```
<script>
	const canvas = document.getElementById("gameCanvas");
	const boardSize = { x: 20, y: 20 };
	const scale = { x: 20, y: 20 };
	canvas.width = scale.x * boardSize.x;
	canvas.height = scale.y * boardSize.y;

	/* Place any future javascript code here. */
</script>
```
The first line of code fetches the html tag with `id = "gameCanvas"`, which happens to be our `<cnanvas>` tag, and assigns it to a javascript variable called `canvas`. Here, `canvas` is the javascript representation of our `<canvas>` tag. It is declared using the keyword `const` because we intend for this `canvas` variable to refer to our `<canvas>` tag throughout the duration of our game. In other words, we want `canvas` to be constant, therefore we declare it `const` (values we want to change throughout the game will be declared using the `let` keyword instead of `const`, as we will see later).

The second line declares another constant, this time it is `boardSize`, which represents the size of the board, namely that the game board will consist of a grid of 20 x 20 cells.

The third line declares the `scale` constant, which sets the size of an individual cell, here it is set to 20 x 20 pixels. Now we can see that the width of the canvas will be the board size in the x direction times the scale in the x direction, and similarly the height will be the same product but in the y direction.

In programming terms, `boardSize` and `scale` are called structures. They are simply convenient groupings of data. If structures didn't exist, we would have to maintain 4 constants, namely `boardSize_x`, `boardSize_y`, `scale_x` and `scale_y`, and there would be no obvious relationship between them besides their names. But grouping the `x` and `y` components of these into structures like this makes it convenient to work with them, as we will see later. Structures are also useful for grouping larger amounts of data, or even nested data (like a directory in a file system that has some nested folders which we want to model in javascript).

We would also like to visually differentiate our `<canvas>` from the background, so we need to add some css styles to our html tags.
```
<style>
	body {
		background-color: black;
		height: 100vh;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
```

**I will not explain css in this tutorial.**
This will create a black background and place our `<canvas>` at the center of the screen. But if you run the html file in a browser at this point, you may see that the entire webpage is black. "Where is the canvas?" you might ask. The canvas is there, it is in the correct position, and has the correct size. It is simply transparent, because we haven't drawn anything to it yet. let us do that here.

> **Note:** Inside html,  the `<!-- Any comment here -->` tag is used for writing comments that are not rendered by the browser. Within css and javascript, `/* Any comment here */` perform the same function, and anything inside the comment is neglected when the program runs. Aditionally, in javascript, we can also comment out using `//` in a single line, like this
>  ```
> valid.javascript.code() // Any comment here
> other.valid.javascript.code()
> ```

To give ourselves a white canvas upon which we can render our game, we can fill the canvas with white in javascript like so:
```
<script>
	/* Previous javascript code. */

	const bgColor = "white"; // background color of canvas
	const snakeColor = "lightgreen"; // color of snake, to be used later
	const fruitColor = "orange"; // color of fruit, to be used later
	const ctx = canvas.getContext("2d"); // get a handler that allows us to draw to the canvas

	ctx.fillStyle = bgColor; // load the background color into the handler
	ctx.fillRect(0, 0, boardSize.x * scale.x, boardSize.y * scale.y);

	/* Place any future javascript code here. */
</script>
```

Here, `ctx.fillrect` is a function that takes in 4 inputs, say `a`, `b`, `c` and `d`, and fills in a `c` times `d` sized rectangle whose left side is `a` pixels away from the left side of the canvas, and whose top side is `b` pixels away from the top side of the canvas. If the given rectangle exceeds the bounds of the canvas, only the parts that are inside the canvas are drawn. Here, we can see that `a = 0`, `b = 0`, `c = boardSize.x * scale.x` and `d = boardSize.y * scale.y`, so we can see that this simply fills the entire canvas with `bgcolor`, which is white. Now if you open your html file in a browser, you will see a white square in the middle of a black page.

With this, our View is completed, at least our initial View is. This is how our game will start.
