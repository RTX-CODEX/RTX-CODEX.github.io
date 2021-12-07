---
layout: post
title: Stepping back in time with your code
categories: [blogging, debugging, reverse execution]
---

_Blog post by Rusty Coleman, CODEX Engineer_

It’s hard to say when you’ll encounter a problem that causes you to look
to an obscure software tool for assistance. As a programmer, I have an
absolutely insane number of development and debug tools to choose from.
You see, I work for CODEX, an elite group within Raytheon Technologies
that specializes in cyber offense and defense work. We are a relatively
small group, so the full breadth of experience with development and
debug tools can be limited within the team. Sometimes, you have to find
the tool for yourself.

One day, I was programming away on a highly multi-threaded new Linux
tool we were developing, minding my own business, when suddenly I
started encountering crashes, ones that were not predictable and varied
in reproducibility from easy to difficult. It seemed some of them were
only happening in a release build, which of course has little to no
debug support. The core dumps that were created were only the tiniest
bit helpful. I could deduce that some variable access in some function
tried to go somewhere non-existent, but I could not determine which code
had set that variable to its garbage value.

This particular code had anywhere from a dozen to two dozen threads, all
working together, performing message passing between themselves, and it
all \*should\* have been thread safe. But somewhere along the way, one
of these threads was not doing what it was supposed to and wrote, or
freed, this variable that later crashed the program. What I really
needed to be able to do was go backwards in the program execution and
figure out where this variable was last written. But, alas, core dumps
do not provide you with such info—they are only a snapshot of what
everything looked like when the train derailed.

While pondering this, I recalled an emulator tool I had used at a
previous job that permitted reverse execution. I looked for something
similar to use here and found a tool that is actually specialized for
what I’m wanting. It’s not an emulator but rather a tool that provides
this reverse execution capability. It’s called “rr,” Record and Replay.
It’s a Linux tool that allows you to execute a program while recording
the accesses it makes to the system as well as the order of thread
executions, etc.

Originally developed by Mozilla for FireFox debugging, Mozilla has made
rr available outside its org. The concept behind it is rather
simple—execute the code natively but capture all OS accesses and
record what is exchanged. Think of it like this: you write code that
reads the system time and displays it to the screen. However, the code
does some fancy calculations to the time before displaying it. The rr
tool would only need to record the time request and the display request.
If the code is executed again later and provided the exact same system
time, the results should always be the same. It makes the later
executions of the code deterministic.

This means if you record an execution that later results in a crash and
then replay the execution, the crash will occur again in the same place
at the same point of execution. The rr tool does not, and need not,
record every single assembly instruction executed. In fact, during a
replay, all the code runs natively so it executes at nearly full speed.
The rr tool only needs to record the OS accesses.

Now, this works great on its own with a single-threaded application.
However, adding multi-threading complicates this process quite a bit.
The rr tool does support multi-threading, but it does so in a single
execution thread. In other words, your 12-threaded code runs as though
you had a single core processor in your PC. The threads swap on this
single core, and the rr tool records which thread got executed when,
which thread was next, etc. Effectively, the tool can simulate full
multi-threading but it will execute much slower, of course.

Now, on to my specific problem… The code I was developing was
experiencing crashes randomly, and sometimes it seemed to only happen in
a release build. So, I fired up a run with the rr tool in record mode.
There was a noticeable, but not humongous, hit to the performance of my
code. However, what might have taken 30 minutes to crash now might take
an hour, for example. However, it wasn’t long before the code did its
crash and the tool stopped the execution.

To playback a recording in a useful way, rr lets you connect to it with
gdb. You get the full gamut of gdb commands and capabilities with the
additional reverse stepping and execution commands provided by rr. You
can literally set a breakpoint on a memory access, and instead of
forward execution, you can reverse execute. I was shaking with
excitement on this first playback. I was going to be the savior of the
group and find a bug that would have been almost impossible to find
otherwise\!

I started the playback, connected gdb, and let it run until the crash.
Ah yes… a crappy memory value being accessed by a message-passing
variable. Why, oh why, was this crappy value there? Who set this? Which
developer do I get to roast for not writing thread-safe code?

What if it’s me??? I suppose I can quietly fix the issue if that’s the
case….

I then set a breakpoint on writing to this memory address and gave the
reverse execute command. (Fingers probably got crossed at some point
here, though I can’t confirm.) Reverse execution did seem to be slower,
that or time dilated in my mind as I anxiously awaited the location of
the dirty deeds in my program.

Alas\! I hit the breakpoint\!

The last write was in a bit of code that I didn’t author (YES\!), and I
didn’t understand what this code was doing, so I eagerly notified my
fellow developer. At the same time, I let him know how I found this bug.
He had never heard of this crazy rr tool before, either. Because my
colleague now knew which code was involved, it didn’t take him long to
inspect and eliminate this particular issue.

Some time passes, a week or two perhaps, and additional obscure crashes
start to appear in this code. Some of these crashes were just not
reproducible in debug builds and only occurred in release builds. That’s
always a stomach dropping sensation…. Sure, the rr tool can record
release builds, and the playback tool lets you step through it, but now
you’ve got to do some side work to figure out the memory location
blah-blah-blah is part of. There are ways to do this, so I won’t
describe it here.

However, the crashes I was seeing in release builds were really only
happening there because the threads in that build focused directly on
the task at hand and didn’t spend time writing to the debug console,
among other side tasks. As it turns out, the rr tool has a switch that
can make these sporadic and very hard to reproduce multi-threaded bugs
bubble up to the top. This mode has a most fitting name—chaos mode\! It
basically modifies the thread execution priorities to complete
craziness. Some threads get extraordinary amounts of execution time,
while others get interrupted almost immediately. And these priorities
get shuffled around while the code is executing. If you have a
thread-unsafe condition, this chaos mode is likely to get the code to
misbehave while it’s being recorded.

Using this tool, I was able to identify a number of bugs in the code in
a straightforward way. Finding some of the bugs wasn’t as easy as
setting a write breakpoint and reverse executing. However, even in these
cases, if I could identify the specific function call where things go
awry somewhere inside, I could break the execution right at the
beginning of this function and step through, already knowing this
particular execution of this particular function is going to go south
somewhere along the way. After all, I can debug code that \*always
fails\*. But I can’t easily debug code that almost always works except
in very specific conditions, ones that I have no idea what they are.
Stepping through a function that has otherwise worked fine for hundreds
of thousands of calls, but isn’t going to work on \*this\* particular
call, is quite exciting. I love a good puzzle, and this is one where the
tumblers require precise alignment.

This tool is perfectly designed for the problems we were facing. I don’t
know how we would have found the bugs causing these issues without just
total code scrutiny and trial-and-error. I believe some of the other
developers on the team also started using this tool because the number
of random crashes significantly dropped after word got around. That, or
we started writing better code to begin with.
