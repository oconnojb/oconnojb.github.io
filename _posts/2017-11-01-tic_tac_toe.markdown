---
layout: post
title:      "TIC TAC TOE"
date:       2017-11-01 11:45:37 -0400
permalink:  tic_tac_toe
---

## A JS ATTEMPT
![](https://alphabetletters.org/alphabet-letter-downloads/decorative-letters/256/alphabet-letter-a.jpg)fter my first endeavor into coding, I began working through Flatiron’s bootcamp prep course in anticipation of applying. I had already finished the bootcamp prep Javascript course, and had started work on the Ruby course which builds to a working game of tic-tac-toe.

As I was working through the steps that worked on building the helper methods that would facilitate the game’s flow, I began to become aware of the direction that the game was headed in. Even though I did not have the skills in Ruby to codify it, the steps made logical sense. And I bet I could do it in Javascript, using only what I learned from the bootcamp prep, and a touch of HTML and CSS I had already picked up online. To my surprise, it worked! *(I coded it on codepen.io which has windows for HTML, CSS, and JS already open above a site preview)*. [This link](https://codepen.io/oconnojb/full/BwWKWo/) will let you play the game with a friend! 

**Here’s how I did it:**


### Have to play somewhere!

My first task was to create the board. I didn’t care how it looked, so long as it was recognizable as a game of tic-tac-toe. I threw this simple board together in HTML so I would have something to work with:

```
<p>_ | _ | _</p>
<p>-----------</p>
<p>_ | _ | _</p>
<p>-----------</p>
<p>_ | _ | _</p>
```

I used the underscore ( _ ) as a placeholder for the X’s and O’s that would eventually populate the board to keep the spacing from changing.


### But all those rows look the same?

I knew I wanted to use Javascript to facilitate play of the game, but I had no way of targeting the correct rows when it came time to make a move, so my board wasn’t quite ready for the Javascript. I thought it best to give each row its own unique ID:

```
<p id="row-one">_ | _ | _</p>
<p>-----------</p>
<p id="row-two">_ | _ | _</p>
<p>-----------</p>
<p id="row-three">_ | _ | _</p>
```

As the main point here was to see if I could make the game work using JS instead of Ruby, I left the HTML looking like that until the game worked *(afterwords, I went back and altered it to make a nicer looking game)*. Let's get to the Javascript!


### Where's the board?

Just like in the Ruby bootcamp prep course, my game of tic-tac-toe relies heavily on an array to determine the board’s layout. I definied this array right at the top of my script. My board looks like this:

```
board = ["_", "_", "_", "_", "_", "_", "_", "_", “_”];
```

*Just like my HTML board, my JS array has those underscore placeholders.*


### A program like this needs some help!

I defined four functions to facilitate a game of tic-tac-toe.

1. updateBoard()
2. checkForWinX()
3. checkForWinO()
4. countUp()

*Let's dive in, shall we?*


### updateBoard()

The updateBoard() function is responsible for making the board look correct on the screen. It does two things:

1. Declares some variables targeting the HTML
2. Uses those variables to rewrite the targeted HTML

It looks like this:

```
function updateBoard(){
  var row_one = document.getElementById("row-one"), 
    row_two = document.getElementById("row-two"), 
    row_three = document.getElementById("row-three");
  
  row_one.textContent = `${board[0]} | ${board[1]} | ${board[2]}`;
  row_two.textContent = `${board[3]} | ${board[4]} | ${board[5]}`;
  row_three.textContent = `${board[6]} | ${board[7]} | ${board[8]}`;
}
```

In part one, the variables row_one, row_two, and row_three are set to target those unique ID's in the HTML. I used the getElementById function to make that happen, making sure that the ID I wanted to target was wrapped in quotes and written out exactly as I had written it in the HTML.

Part two is where the magic happens. For each of those targeted rows, the textContent function allows me to rewrite the HTML. I used some [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) to help keep the code readable. Alternatively, I could have written it as:

```
row_one.textContent = board[0] + " | " + board[1] + " | " + board[2];
```

*It looks messy, but works just as well.*

As the board is changed by some of our other helper functions, the updateBoard() function will make sure the players are looking at the right bard on the screen!


### checkForWinX() & checkForWinO()

If you hadn't already guessed, these functions check to see if the game was won by X or O respectively. These two blocks of code have a lot of repeated information, and I have a hunch I can combine them into one helper function *(but I don't know how to do that yet)*. For now, they live as separate, yet clearly related, functions until the day I can clean them up into one function. Let's take a look at what I wrote. Here's checkForWinX():

```
function checkForWinX(){
  if (
    //row 1 full 
    (board[0]==board[1] && board[1]==board[2] && board[0]=="X") ||
     //row 2 full 
    (board[3]==board[4] && board[4]==board[5] && board[3]=="X") ||
     //row 3 full 
    (board[6]==board[7] && board[7]==board[8] && board[6]=="X") ||
     //diagonal TL-BR full 
    (board[0]==board[4] && board[4]==board[8] && board[0]=="X") ||
     //diagonal TR-BL full 
    (board[2]==board[4] && board[4]==board[6] && board[2]=="X") ||
    //col 1 full 
    (board[0]==board[3] && board[3]==board[6] && board[0]=="X") ||
    //col 2 full 
    (board[1]==board[4] && board[4]==board[7] && board[1]=="X") ||
    //col 3 full x
    (board[2]==board[5] && board[5]==board[8] && board[2]=="X")){
    
    board = ["_", "_", "_", "_", "_", "_", "_", "_", "_"];
    playCount = []
    updateBoard();
    alert("X WINS!");
  } 
}
```

*checkForWinO() looks exactly the same, but all the X's are replaced with O's*

The checkForWin functions use a big `if` statement to make sure that the function will only run if that player wins. Each of the winning conditions *(which I kept track of for myself with some comments)* is separated by two pipes ( `||` ) which signifies `or`, because only one winning condition will ever be met in any given game. Of course, each of those winning conditions requires a few `and`'s to make it happen. Lets look at the first winning condition a little more carefully. *Remember, those tricky JS arrays are 0 indexed so board[0] is actually the first item in the array!*

```
//row 1 full 
(board[0]==board[1] && board[1]==board[2] && board[0]=="X")
```		

Here we are saying we want to check if: the first board item matches the second board item **AND** the second board item matches the third board item **AND** the first board item matches the string "X" *(without this last AND, an empty row would also fit the conditions!)*, then player-X must have won!

But the checkForWin functions are more than one-trick ponies! *(In hindsight, I've learned that functions probably should specialize in just one thing, and I probably should have done the rest of this in a separate function)* The checkForWin function also resets the game so you can play again and even congratulates the winner of the game. How does it do that, you ask? With the four lines of code that run if the if statement evaluates true:

```
1    board = ["_", "_", "_", "_", "_", "_", "_", "_", "_"];
2    playCount = []
3    updateBoard();
4    alert("X WINS!");
```

That first line resets the board array to it's original state, with nine "empty" boxes *(Ok, technically they've got that underscore placeholder)*.

That second line resets the playCount array *(**What playCount array?** Oh you'll find out, just hang on, it comes into play in the next section)*.

That third line runs the updateBoard() function from scene 24 *(scene 24 was a smashing scene with some lovely acting--)*.

That fourth line sends up an alert to the players telling them that "X WINS!" and *(by the time they dismiss that alert)*, the game is reset!


### countUp()

The countUp() function was the last one I defined to help make this game playable. Basically, it let's the computer know how many turns have been taken. It does that through a new array: the playCount array! *(it's empty for now, but I told you it would come into play in this seciton!)*

```
playCount = [];
```

In order to get the function up and running, I also needed a new variable: I called it "counter." It is equal to the length of the playCount array.

```
var counter = playCount.length;
```
*Right now, counter = 0. But all that will quickly change!*

The countUp function keeps track of the number of turns that have been taken and also informs the computer what to do in the case of a tie. Here, take a look:

```
function countUp(){
  playCount.push("num");
  if (counter == 9){
    board = ["_", "_", "_", "_", "_", "_", "_", "_", "_"];
    playCount = []
    updateBoard();
    alert("TIE GAME");
  }
}
```

First, the function changes the playCount array. It pushes the string "num" to the array. It doesn't really matter what gets pushed onto the array, but the point is each time the countUp function is called, the array gets longer by one. Next, there's the `if` statement. I figured, if nine turns have been made, then the board must be full, and there must be a tie, so the countUp() function also resets the board and playCount, updates the board, and lets the players know there was a tie.

With all the helper functions ready, the game is ready to take shape!


### But how do we play?

My first idea was to have the players input their moves into some sort of input field, but I wanted to keep the game looking clean. Instead, I added an event listener which makes the intended move when a key is pressed. In order to make it work, I needed another array: `turner`. It's a simple array, consisting of alternating strings of X's and O's.

```
turner = ["X", "O", "X", "O", "X", "O", "X", "O", "X", "O"]
```

***How is that array helpful?** Take a look at the event listener, and then it might make a bit more sense.*

```
window.addEventListener('keypress', function (e) {
  var counter = playCount.length;
    if (e.keyCode >= 49 && e.keyCode <=57) {
      board[(e.keyCode-49)]=turner[counter];
      updateBoard();
      turn.textContent=turner[counter+1];
      checkForWinX();
      checkForWinO();
      countUp();
    }
}, false);
```

This event listener listens for keypresses, but the we really only cares about the numbers 1-9 which the players will use to interact with the game. The keyCode associated with number 1 is 49, 2 is 50, three is 51, and so on all the way up to 9 which is 57. The if statement `if (e.keyCode >= 49 && e.keyCode <=57)` makes sure the code below it only runs if the player presses one of the numbers 1-9. 

I decided to use these keyCodes to let the computer know which index of the board the player wished to interact with. After making sure the player pressed a viable number, the game takes a turn. 

1. It updates the board at the index desired *(the keyCode minus 49; if the user pressed 1, the computer runs the calculation 49-49 to determine that we want the index 0)* to match the turner array at the index of whichever turn we are on *(the counter at the beginning of the game is 0 because we haven't called the countUp() function yet)*. 
2. It runs the updateBoard() function to make sure that the players are looking at the up-to-date game. 
3. It does something with the `turn` variable *(this doesn't affect gameplay, I added it later as a way of informing the players whose turn it is. If you are playing the game, this line of code is responsible for the "It is _'s turn" changing each round)*. 
4. It checks to see if X or O won the game by running checkForWinX() and then checkForWinO(). 
5. It counts up ith the countUp() function *(which is also checking for a tie)*. 
 
If there hasn't been a winner and if there isn't a tie, the game is ready for another move. If there is a winner, or if the players tie, the alert will pop up and--when the players dismiss the alert--the game will be reset to begin anew!


### And there you have it!

All of the playability of this game of tic-tac-toe has been coded. After wrapping up the functionality, I went back and made some edits to the HTML and wrote some CSS to make the game more visually appealing. If you're interested, [this link](https://codepen.io/oconnojb/pen/LOVBWb) will take you to the codepen so you can take a look at the rest of the HTML and CSS and see how it all fits together. While you're there, feel free to play a round or two!

--Until we meet again--

