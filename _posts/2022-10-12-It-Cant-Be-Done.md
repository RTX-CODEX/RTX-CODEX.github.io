---
layout: post
title:  It Can't Be Done... Unless You Know How
categories: [blogging]
---

_A blog post by E. G., CODEX Engineer_

One day long, long ago, during a boring freshman year college class, I pulled out a piece of paper and started drawing a maze.  The idea came to me to make a computer program that would present a maze to a player.  I drew a pyramid of mazes with stairways.  The base was 25x25 squares.  The next layer was 23x23 squares.  The pyramid was 13 stories tall, and there was a prize on the top floor.  The user would have to find their way to the top of the pyramid, get the prize, and then go back out the way they came before running out of turns.  The hard part was that the user would only ever see the shape of the room they were currently in.

In high school, I had taught myself Applesoft BASIC on the one-and-only Apple IIe computer in the library.  (This was before the proliferation of PCs.)  I decided to write my "Pyramid" program in Applesoft BASIC on one of the Apple IIe computers in the college library.  I started by writing out the whole program on paper.

When the program was completely written, I went to the library to type it all up.  I had a 5 and 1/4 inch floppy disk on which I saved my program.  For several days, I would go to the library to type up my program.  An Apple IIe would boot up from ROM, load a BASIC interpreter, and attempt to find a "Hello" program on the floppy disk.  The "Hello" program on my disk would present a menu of the programs on the disk.  I would select my Pyramid program, and "Hello" would load and execute it.  Pyramid would always crash, because the code was incomplete.  At that point, I was at a prompt in the BASIC interpreter, and I would continue typing in my program.

One night, in my dreams, I went to that computer, selected my program, and it crashed like usual.  In my dream, however, I read the error: `?OUT OF MEMORY ERROR IN 20`.  For the first time, I thought about what those words meant.  I realized IN MY SLEEP that the crash was not caused by incomplete code.  No.  I had consumed all 48 KBytes of the RAM on the computer!  (I wish I would routinely debug programs in my sleep!!)

In Applesoft BASIC, each line of code has a line number.  It is good practice to assign line numbers with gaps between them in case you have to insert code between two lines later.  My first two lines of code were something like this:
```
10 DIM A$(25,25,13)
20 DIM B$(25,25,13)
```
The dollar sign ($) communicates the type of the variable as string.  Therefore, these are somewhat equivalent to the following C code:
```
char* A[25][25][13];
char* B[25][25][13];
```
I found online documentation that states an empty string array member consumes three bytes: one byte for the length (0-255) and two bytes for a location pointer.  I also found an online Apple II emulator (written in Javascript and runs on your browser).

I spoke to one of the Computer Science professors about it.  He agreed that I was using too much RAM in my two 25x25x13 string arrays.  I naively designed my software to place only one character in each position in the arrays.  The first array was for the room shape, and the second array was for whether there were stairs going up, down, or no stairs at all.  My professor did me a big favor when he gave me this advice, "It can't be done."  He did not believe that I could get this program to run on such little memory.  After he said that, I was determined to succeed!

The stubborn attitude that made me want to build this program that allegedly "can't be done" is a part of my personality that works well in the culture at CODEX.  We are a team of engineers who all get excited by finding ways of doing the things that are "impossible".  Hard problems, puzzles, and mysteries -- these are delicious things in our culture.  At CODEX, however, we are not told that things cannot be done.  We are typically asked to "find a way to...".  And then, we do.

The end of my story is not very interesting.  I decided to use one string to represent one row in my pyramid.  I loaded only one floor of the pyramid at a time from the disk.  I still had separate arrays for the room shape and for the stairs state.  I finished writing and debugging the game.  Then, I played it.  It was ridiculously hard to win.  So, I only beat it once.

<ul>
<li><a href="https://www.scullinsteel.com/apple2/">Apple II Emulator</a></li>
<li><a href="http://cini.classiccmp.org/pdf/Apple/AppleSoft%20II%20Basic%20Programming%20Manual.PDF">Reference Manual</a></li>
</ul>
