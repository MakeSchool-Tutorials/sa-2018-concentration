---
title: "Game Logic"
slug: game-logic
---

With our gameboard successfully setup, we'll move onto implementing the game logic. In other words, we'll start writing code so that the rules of the game are followed.

In this section, we'll need to implement JavaScript for the following:

- monitoring when `.board-square` `<div>` elements are clicked by the player
- flipping squares from facedown to faceup and vice versa
- checking if two squares have matching faceup colors

# Handling Square Click Events

We'll begin with monitoring and handling _click_ events whenever a player clicks on a square. To do so, we'll a method named `addEventListener()` to send a notification to your `BoardSquare` object whenever a _click_ occurs.

> [action]
>
In `main.js`, add the following line of code to your `BoardSquare` constructor:
>
```JavaScript
class BoardSquare {
  constructor(element, color) {
    this.element = element;
>
    // 1
    this.element.addEventListener("click", this, false);
>
    this.isFaceUp = false;
    this.isMatched = false;
    this.setColor(color);
  }
>
  // ... previous code
}
```
>
In the syntax above, we add an event listen to our `.board-square` DOM element so that whenever it's clicked, our `BoardSquare` object is sent a notification.

Next, we'll add a special function in our `BoardSquare` class to handle the _click_ notification that's sent.

> [action]
>
In `main.js`, add the follow function within your `BoardSquare` class:
>
```JavaScript
class BoardSquare {
>
  // ...
>
  // 1
  handleEvent(event) {
    // 2
    switch (event.type) {
      case "click":
        // 3
        console.log(this.color + ' square was clicked');
    }
  }
>
  // ...
}
```
>
Let's go through the steps above together:
>
1. We add a new method to our `BoardSquare` class definition named `handleEvent()`.
1. Check that the event that we're handling is a _click_ event. If the event is not a _click_ event, we don't handle it.
1. Log to the JavaScript console that the gameboard square was clicked.

Refresh your browser and click on a couple squares. Navigate to your JavaScript console in your DevTools window and verify that each gameboard square is handling _click_ events.

![Log Click Events](assets/log_click_events.mov)

# Game Logic

After handling the _click_ event of our squares, we can move on to actually implementing the code that will allow us to play concentration. Let's go over the rules of what happens when a user clicks on a square.

### Clicking on a Square

When a player clicks on a square, the following should happen:

1. Check that the square isn't already faceup or matched with it's pair. 
  2. If so, don't do anything.
  3. If not, flip the square faceup and set it's `isFaceUp` property to true.
4. Handle the game logic for when a square is flipped.

Let's breakdown step 3. further. 

### Handling Flipped Squares

When a square is flipped, the following should happen:

1. If the square is the first to be flipped faceup, save a reference to it and do nothing else.
2. If the square is the second to be flipped faceup, check if it's faceup color matches with the first square.
  3. If so, set the squares to matched and clear the reference of the first square.
  4. If not, flip both squares back to facedown and clear the reference to the first square.
  
We can start by implementing the logic for when a player clicks on a square.

## Implementing Game Logic

> [action]
>
In `main.js`, within the `handleEvent` method of your `BoardSquare` class definition, replace your `console.log()` with the following:
>
```JavaScript
handleEvent(event) {
  switch (event.type) {
    case "click":
      // 1
      if (this.isFaceUp || this.isMatched) {
        // 2
        return;
      }

      // 3
      this.isFaceUp = true;
      this.element.classList.add('flipped');

      // 4
      squareFlipped(this);
  }
}
```
>
We follow our logic above and implement the following:
>
1. Check that both `isFaceUp` and `isMatched` are false.
1. If either is true, return and do nothing else.
1. If both are `false`, set `isFaceUp` to `true` and add `.flipped` to the `.board-square` DOM element.
1. Call a function to handle the game logic for flipping a square. (If you're wondering, we haven't implemented this yet.)

Next, we'll move on to adding the code to handle flipped squares. But before that, let's add some helper functions in our `BoardSquare` class so that we can easily:

- set a `BoardSquare` as matched
- reset a `BoardSquare` back to it's default (facedown) state

> [action]
>
In `main.js`, in your `BoardSquare` class, add the following methods:
>
```JavaScript
class BoardSquare {
>
  // ...
>
  reset() {
    this.isFaceUp = false;
    this.isMatched = false;
    this.element.classList.remove('flipped');
  }
  >
  matchFound() {
    this.isFaceUp = true;
    this.isMatched = true;
  }
>
  // ...
}
```

After adding our new methods to our `BoardSquare` class, we can move on to implementing the remaining game logic for concentration.

> [action]
>
In `main.js`, at the bottom of your script, add the following code:
>
```JavaScript
// 1
let firstFaceupSquare = null;
>
function squareFlipped(square) {
  // 2
  if (firstFaceupSquare === null) {
    firstFaceupSquare = square;
    return
  }
>
  // 3
  if (firstFaceupSquare.color === square.color) {
    // 4
    firstFaceupSquare.matchFound();
    square.matchFound();
>
    firstFaceupSquare = null;
  } else {
    // 5
    const a = firstFaceupSquare;
    const b = square;
>
    firstFaceupSquare = null;
>
    setTimeout(function() {
      a.reset();
      b.reset();
    }, 400);
  }
}
```
>
Walking through the steps above:
>
1. First, we create a variable to hold a reference to the first faceup square. This will help us keep track of whether the square is the first or second square to be flipped.
1. Check if the square is the first square to be flipped faceup. If it is, set a reference to it using the `firstFaceupSquare` variable and return from the function.
1. If the square is the second square to be flipped, check if it's faceup color matches the first faceup square's color.
1. If both faceup colors for each square is the same, a match is made. Set both `BoardSquare` objects to matched using the `matchFound()` function and clear the `firstFaceupSquare` variable so the player can keep playing.
1. If the faceup colors aren't the same, reset each square to it's default (facedown) state.

When you're finished, refresh your landing page, and start matching away. Your game should be fully working at this point!

![Finish Game Logic](assets/finish_game_logic.mov)

# Up Next

In this section, we broke down and implement the logic for our game of concentration. In the next section, we'll finish the implementation of our game by adding some final touchups to our UI.
