---
layout: post
title:  "How I Did It"
date:   2017-04-07 11:04:28 -0400
---

![Young Frankenstein - How I Did iIt](http://i.imgur.com/TgSF15c.gif)

Okay so maybe this is a little dramatic, but I made tic tac toe game that can't beat itself. I know what your thing: isn't that a bad thing? Actually it's not as far as tic tac toe goes. A perfect game of tic tac toe, one in which at least one player knows some strategy, is impossible to win.

Sounds difficult to code, right? It can be, but with enough confidence and a few happy accidents, you can do it.

Before even the pseudocode or actually constructing a method in Ruby, I played myself at tic tac toe. They say it hard to beat yourself at any game, but constructing an AI that can't beat itself, it's kinda the same thing. At first I'd be playing games and winning against myself, which is always embarrassing. But then I started to notice that the center should always be taken at the first opportunity and that a good opponent controls the corners. I had pseudocode of the whole program written out in minutes, tweaking it with each passing Cat's game.

The hard part was implementation. There are many ways to prime an AI for tic tac toe. Trust me I saw countless suggestions while programming it, and even after it was over, other solutions kept coming.

I tried planning it out move by move, making an AI so detailed it would know the principle behind an opponent's every move. I gave up on that fast. At about the third move, my code began to break. It didn't know how to think. It just knew to do the path I had set. My case statement (and when that became too unreadable my if/elsif/else statement) caused countless headaches and brainwracking as I tried (to no avail) to teach the computer to play even a competent game with a human. It was then I had the idea to make each block of code as simple as possible.

The rest of this post will dive into some of the code I used to make my tic tac toe AI (Artificial Inteligence). If you're in Flatiron's Full Stack Dev program like me, have a little going through it, build it on your own first, then see what I have to say.

![Code](http://i.imgur.com/fD4vpZh.png)

Here is the nexus of my computer's AI. Look's simple, right? If the middle square isn't taken, the computer should grab it, otherwise, it should strategize. It took my awhile to come up with that, but I needed my move method to be simple, refactored down to its simplest form. (I'll explain all of the `+1`'s later).

![More Code](http://i.imgur.com/S9csQ3S.png)

This part is even better. Basically, I'm telling the computer to analyze whether it has a winning move or a blocking move, if it should take a corner or if the move needs only to be a random number between 0 and 8.

Picking a corner was the easiest to program. Random selections were harder to control because for my computer continued picking truly random numbers and bogging down the system with heaps of invalid moves. That's when I rediscovered the `.sample` method to pick unique integers from an array.

The code I'm most proud of is the crux of what both my `.win(board)` method and my `.block(board)` method rely on.

![Even More Code](http://i.imgur.com/tzG0qZd.pngd)

It's beautiful right? I think it's best I explain this a little. `Game::WIN_COMBINATIONS` is a constant in `class Game` that keeps track of all of the ways one can win at tic tac toe. I spaced out the rest of it for easy viewing, but what's going on is it checks to see if either the first two, the last two, or the first and last of a combination are taken and by the same token. For the first combo where this returns true the `.detect` method sends an array of the winning combo back to the functions that called it.

Forgive the added integers in there. They're necessary to make the code run since the method they are calling was intended for the user. The `.taken?` method checks to see if a spot is taken by a token; however, it relies on another method called `.position` that takes a *user* input, meaning that while the index of the array for the tic tac toe board is equal to 0-8 a user will see the empty spaces on a tic tac toe board as *1-9*, so the method has to adjust for the difference.

Believe it or not, there isn't much more to it. Just a few functions that analyze the `.winning_combo?` method, deciding where to go on whether the two taken spaces are the computer's token or its opponent.

I admit that I didn't expect my AI to be unable to beat itself, but when I sarted it up and got it to play itself 100 times, this happend War Games style:

![War Games - Tic Tac Toe](http://i.imgur.com/Ks0kdQV.gif)

I was ecstatic.


