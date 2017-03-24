---
layout: post
title: "Ruby String Manipulation"
date: 2017-03-24 11:17:15 -400
---

Here's what I've been working on today:

![Ruby Oxford Comma Code](http://i.imgur.com/pInyfOR.png)

Let me tell you what's goine on. The whole point is to take an array such as, ```["Harry", "Ron", "Hermoine", "Draco"]``` and
turn that into a string complete with Oxford comma and an "and" before the last item in the list, like so: "Harry, Ron, 
Hermoine, and Draco." Easier said than done.

We're at that point in proceedural Ruby where each lab gives less and less informaiton about how to solve it's puzzles with the
expectation that we start researching more and more on our own, discovering new ways to solve the problems, even with methods
they never covered. Well I did just that. I'll explain my solution to the problem (top), which is slightly more verbose than 
their solution (bottom), which I added into my code after checking it with their's after submission. Don't worry, no way to 
cheat here! ;)

I discovered a new method called ```.gsub``` in my research, not to mention the possibility of shoveling(```<<```) into strings
like you can arrays. Basically, what my code is doing is:
1. It tests an array's length to see if it's more than two, meaning any list of more than 2 items.
2. If it is, the array is ```.join```ed into a string with a comma and space separating them.
3. Then the code substitutes the last item in the list, which we discover by comparing it to ```array.last```, and adds "and ".
4. Finally, it takes the last element in the array and "shovels" it back on at the end.

If someone wrote out the process it would look like this:
```string.["Harry", "Ron", "Hermoine", "Draco"].join(", ") #=> "Harry, Ron, Hermoine, Draco"
"Harry, Ron, Hermoine, Draco".gsub(array.last, "and") #=> "Harry, Ron, Hermoine, and"
"Harry, Ron, Hermoine, and " << " #{array.last}" #=> "Harry, Ron, Hermoine, and Draco"```

The rest of the code is easy being a way of making a list of two appear: "Harry and Ron", and a list one appear "Harry".

Flatiron's solution is little more complex althought more elegant being the same as mine, but only one line. For an array 
of more than two in length it does the following:
```array[-1].insert(0, "and")```

SO what's happening here? When the array calls an index of -1 the code is looking at the last element in the array. 
Simple enough, but the second half gets tricky. Normally ```.insert(index, element)``` refers to the array its working on, so 
if ```index = -1``` and ```element = "and"``` the array I've been playing with would equal ```["Harry", "Ron", "Hermoine", "and", "Draco"]```
, which doesn't help anybody. But here it doesn't. It actually looks like```["Harry", "Ron", "Hermoine", "and Draco"]```. Why?

Since the index was called before the ```.insert``` method, the index called *in* the method is actually referring to the index 
of the *string*. Strings have indexes, too! ```"mind" == "blown"```. This is why the can do in one line what I did in there becaseu
if you see at the very end of the code the have one line reading ```array.join(", ")``` which becomes "Harry, Ron, Hermoine, and Draco".

Okay, okay, so this might not seem like a big deal to everyone, but when I researched their solution on line, I could find no 
one that revealed that possibility for ```.insert```, which made me want to share it. I also wanted to give my soltuion since 
I'm still unsure of which one is better. Mine might be a little more verbose (a whopping two more lines of code), but 
anyone can see what's happening. I'm not sure about the other...

Anyways! Goodbye for now!
