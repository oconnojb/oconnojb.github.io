---
layout: post
title:      "SUPPLY RUN: a zombie apocalypse game"
date:       2018-01-19 18:19:47 +0000
permalink:  supply_run_a_zombie_apocalypse_game
---


Hey guys, I made a game. It's not finished, but it's something! Let me tell you a bit about it...

First of all, I've been into zombies recently. And, I thought, what if I can make a zombie game using my Javascript skills?

I had completed the rock dodger game through the Learn curriculum, and I knew I wanted something similar, so I built off of that game as a starting point.

Here's what rock dodger does:
* A player is controlled by the arrow keys, it can move left or right but not up or down.
* Rocks are generated at the top of the screen and fall towards the player
* If the rock hits you, game over
* If the rock misses you, the game continues

My concept was a Zombie Apocalypse Supply Run. Shown from above, the player would be constantly running forward. Though to represent this, the player would appear to stay stagnant while the environment shifts toward the bottom of the screen. So, conceptually, as the player runs up to something, it will look more like the something is falling down towards it. Although, at the time of writing, the Supply Run environment consists of a black screen, the illusion will be complete when I add a background that scrolls.

Heres what Supply Run does:
* A player is controlled by the arrow keys, the icon can move to the left or right, but not up or down. *(NEVER STOP MOVING WHEN YOU ARE FACING DOWN AN ARMY OF THE UNDEAD!!)*
* Supply Boxes are generated at the top of the screen and "fall" towards the player. *(Though technically the player is running and they are stationary)*
* If the Box hits you, yay! You collected some supplies! *(But who knows what's in the box? Looters are everywhere these days)*
* If the box misses you, darn! But it's not like it's the end of the world! *(Well... it is... but it's not game over, at least)*
* Zombies are generated at the top of the screen and "fall" towards the player. *(but zombies can locomote, so they won't just wait for the player to get to them!)*
* If the zombie hits you, game over. *(IT'S THE END OF THE WORLD)*
* If the zombie misses you, rejoice. You live to collect another supply!

See the similarities?

The HTML and CSS are pretty similar between the two games. I changed some names and switched around a bit of formatting to make the player and zombies squares instead of rectangles and messed with some of the colors. The game doesn't look great, but this was more... proof of concept. The biggest difference in appearance is the supply counter. Rock Dodger doesn't keep track of any info for the player, but Supply Run needs to tell you how many supplies you collected. The HTML I added looks like this:

```
<div id="counter1" style="bottom: 380px; left: 0px;">Supplies Collected:</div>
<div id="counter2" style="bottom: 360px; left: 25px;">0</div>
```

This CSS accompanies it:

```
#counter1 {
  background-color: white;
  height: 20px;
  position: absolute;
  width: 130px;
}

#counter2 {
  background-color: white;
  height: 20px;
  position: absolute;
  width: 80px;
}
```

The end result is two white boxes superimposed over the map. The top white box says **Supplies Collected:** and the bottom white box is a number, automatically set to **0**. As supply boxes are collected, we have some javascript code that will edit #counter2's inner HTML to reflect the number of supplies gathered.

In Rock Dodger, rocks were created by a function called createRock. The game ran the function at a specific game interval when the start button was clicked. Like this:
```
gameInterval = setInterval(function() {
    createRock(Math.floor(Math.random() *  (MAP_WIDTH - 20)))
  }, 1000)
}
```

In Supply Run, that game interval needs to create more than just one type of thing. We need Supply Boxes, and we need Zombies. *(More Zombies than Supply Boxes)* So I split the createRock function into createSupply and createZombie. These functions are also responsible for moving the objects using `window.requestAnimationFrame()`. Here's the createSupply function:
```
1. function createSupply(x) {
2.   const supply = document.createElement('div');
3.   supply.className = 'supply'
4.   supply.style.left = `${x}px`
5.   var top = supply.style.top = 0
6.   MAP.appendChild(supply)
7.   function moveSupply() {
8.     supply.style.top = `${top += .5}px`;
9.     if (checkCollision(supply, PLAYER)) {
10.      COLLECTED.push(Math.random() * 0.25);
11.      supply.remove();
12.      var sum=parseInt(COLLECTED.reduce(add, 0));
13.      document.getElementById("counter2").innerHTML = sum;
14.    }
15.   if (top < MAP_HEIGHT) {
16.      window.requestAnimationFrame(moveSupply)
17.    } else {
18.      supply.remove()
19.    }
20.  }
21.  window.requestAnimationFrame(moveSupply)
22.  SUPPLIES.push(supply)
23.  return supply
24. }
```
Line-by-line we:
1. define the function and pass it a variable (x) which becomes it's location: `createSupply(x)`
2. defines a new constant called `supply` which creates a new div
3. gives our new `<div>` the class `.supply` so it looks correct on screen
4. sets the position of the left side of the supply box. It uses the argument `x` which we generate elsewhere to have the supply box appear in a random location.
5. sets the position of the top of the supply box as 0, the top of the screen
6. puts the newly created div into the map in the HTML *(at the top of my JS is the line `const MAP = document.getElementById('map')`)*
7. defines a function that will move the supply box towards the bottom of the screen: `moveSupply()`
8. moves the box .5px away from the top of the screen, sliding the box downward
9. an if statement! It's checking if there's a collision between the supply box and the player. If there is...
10. an array (`const COLLECTED=[]` is defined at the top of the JS) gets a random, *small* number pushed into it. This is how the game keeps track of the collected supplies.
11. the supply is removed from the map, we already collected it!
12. a variable (`sum`) now equals the interger that is the sum of all the numbers in the `COLLECTED` array. I had to create a simple function that would add them up too. It's called `add` and it looks like this:
```
function add(a, b) {
    return a + b;
}
```
13. the inner HTML of counter2 is changed to reflect the number of supplies collected
14. end of the if statement started on line 8!
15. a new if statement! This one is checking to see if the supply box is still on the map. If it is...
16. we request an animation frame to move the supply
17. if the box is off the map... *(else)*
18. remove the supply box from the game
19. end of the if statement started on line 14
20. end of the moveSupply function within the createSupply function
21. the createSupply function asks to move the supply
22. an array called (`const SUPPLIES=[]` is at the top of the JS) gets the supply box pushed to it.
23. the funciton returns the supply in question
24. end of function

Woah! Sweet! We can collect supplies now! And, because it's the zombie apocapypse and no one is really sure what they're going to find in any give location, each supply box ups the score by a random interger! *(see line 10)*

But what about the zombies? Aren't they an important part of this game?

YES! They are! And, I wanted to make the zombies more menacing than rocks falling from the top of the screen. Rocks fall in a straight line, but zombies have eyes and ears. They might be dumb and slow, but if they see you they'll walk towards you! I made a function `createZombie(x)` to facilitate making zombies. It's pretty similar to the `createSupply(x)` funciton, so I won't paste the whole thing here, but there are some key changes!

```
zombie.style.top = `${top += .75}px`;    
```

The above line makes the zombie "fall" towards the player .25px faster than the supply crates *(compare to line 8 in `createSupply`)*. This makes sense becasue of the birds-eye-view vantage point. The player runs towards the boxes and the boxes don't move. However, the player is also moving towards the zombies generated at the "top" of the map, and zombies can walk, too! They shamble down the map towards the player, making it more difficult to collect supplies. But zombies can see you! They change course to more accurately intercept and devour your flesh. Enter the next few lines:

```
    if(playerLeftEdge > zombieLeftEdge){
      zombie.style.left = `${x += 0.125}px`;
    } else {
      zombie.style.left = `${x -= 0.125}px`;
    }
```

These lines of code check where the player is in relation to the zombie. If the zombie is to the right of the player, it moves left to intercept. Or, if the zombie is to the left of the player, it moves right! SCARY!

I had mentioned above how Rock Dodger creates rocks through it's game interval, like this:

```
gameInterval = setInterval(function() {
    createRock(Math.floor(Math.random() *  (MAP_WIDTH - 20)))
  }, 1000)
}
```

If I were to leave this unaltered, the game would try and create rocks instead of supply crates or zombies. Since, I removed the createRock function, nothing would work. If I changed the game interval to call on the funciton createSupply or createZombie, it would only do one and not the other. So I made a new function for the game interval to call on! Check it:

```
function create(x, rand){
  if (rand >= 0.85){
    createSupply(x);
  } else {
    createZombie(x);
  }
}
```

`create` is passed two arguments. The first (x) is used in the same way as the createSupply or createZombie function. In fact, within `create` I call on createSupply (or createZombie) and pass it that variable x. The second is a randomly generated number between 0 and 1 *(I'll point out where that number is generated in a bit!)* This number takes care of how frequently a zombie or supply crate appears for the player. Because we want a lot of zombies and only a few supply crates, if the random number is below 0.85 *(which is much more common than a number greater than 0.85)* it runs createZombie, and if the number is above 0.85 it runs createSupply.

Then I changed the game interval like this:

```
gameInterval = setInterval(function() {
    create(Math.floor(Math.random() *  (MAP_WIDTH - 20)), Math.random())
  }, 1000)
```

Notice how it calls the generic `create` function and passes it two randomly generated numbers. First: `Math.floor(Math.random() *  (MAP_WIDTH - 20)` which will determin the created object's starting location. And second: `Math.random()` which helps `create` decide whether to make a supply crate or a zombie.

There's more I want to do with this game. I want to add some environmental obstacles like walls that could trap players or zombies, I want to make the map more visually interesting than a black screen to help sell the illusion that we are looking from above, and I want to figure out what is causing the glitch that causes the game to alert that 

Looking to test your skills during the apocalypse? Play it [here!](https://codepen.io/oconnojb/pen/ZvyjgX?editors=1010) Happy hunting!
